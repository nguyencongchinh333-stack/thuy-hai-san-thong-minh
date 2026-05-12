# 06 — Firmware Đầy Đủ — thuy-hai-san.ino

---

## 6.1 Môi Trường & Thư Viện

```bash
# LibreTuya + Arduino IDE 2.x
# Board: BK7231N (CB2S)

arduino-cli lib install \
  "WiFiManager" "PubSubClient" "ArduinoJson" \
  "Adafruit MCP23017 Arduino Library" \
  "Adafruit ADS1X15" \
  "DallasTemperature" "OneWire" \
  "VL53L0X" "MS5837"
```

---

## 6.2 Cấu Hình (Nhập Qua WiFiManager Portal)

```cpp
// Các giá trị mặc định — user nhập qua portal lần đầu:
char cfgMaKhach[32]  = "";                    // VD: "TOM_001"
char cfgMqttHost[64] = "broker.hosco.com.vn"; // Broker của bạn
char cfgMqttUser[32] = "device";
char cfgMqttPass[32] = "secret";
char cfgMqttPort[8]  = "1883";
```

---

## 6.3 Code Đầy Đủ

```cpp
// ════════════════════════════════════════════════════════════
// THỦY HẢI SẢN THÔNG MINH — NextFarm v1.0
// CB2S (BK7231N) + 2× MCP23017 + 31 relay
// MQTT: broker.hosco.com.vn
//
// ƯU TIÊN TUYỆT ĐỐI: Bật quạt sục khí TRƯỚC cả WiFi
// nếu DO thấp khi boot — tôm/cá không chờ được
// ════════════════════════════════════════════════════════════

#include <WiFi.h>
#include <WiFiManager.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>
#include <Preferences.h>
#include <Wire.h>
#include <Adafruit_MCP23X17.h>
#include <Adafruit_ADS1X15.h>
#include <OneWire.h>
#include <DallasTemperature.h>
#include <VL53L0X.h>
#include <MS5837.h>
#include <LiquidCrystal_I2C.h>
#include <esp_task_wdt.h>
#include <time.h>

// ── MQTT Config (nhập qua WiFiManager) ──────────────────────
#define MQTT_PORT_DEFAULT 1883
#define WDT_TIMEOUT 30
const char* NTP_SERVER = "pool.ntp.org";
const char* TZ_VN      = "ICT-7";

char cfgMaKhach[32]  = "";
char cfgMqttHost[64] = "broker.hosco.com.vn";
char cfgMqttUser[32] = "device";
char cfgMqttPass[32] = "secret";
char cfgMqttPort[8]  = "1883";

// ── GPIO ────────────────────────────────────────────────────
#define PIN_SDA      0
#define PIN_SCL      1
#define PIN_UART_TX  4    // → MH-Z19B (không dùng trong thủy sản)
#define PIN_UART_RX  5
#define PIN_DS18B20  8
#define PIN_BTN      9
#define PIN_LED_AUTO 10
#define PIN_LED_TAY  11
#define FLOW_CAP_PIN 14   // YF-B10 nước cấp
#define FLOW_THAI_PIN 26  // YF-B10 nước thải

// ── Relay kênh ───────────────────────────────────────────────
#define K_BOM_CAP    0
#define K_QUAT1      1
#define K_QUAT2      2
#define K_QUAT3      3
#define K_QUAT4      4
#define K_BLOWER1    5
#define K_BLOWER2    6
#define K_BOM_THAI   7
#define K_BOM_TUAN   8
#define K_BOM_VOI    9
#define K_BOM_KHOANG 10
#define K_DEN_UV     11
#define K_SUOI_NUOC  12
#define K_DEN_AO_A   13
#define K_DEN_AO_B   14
// 15 = dự phòng
#define K_VAN_LANG1  16
#define K_VAN_LANG2  17
#define K_VAN_LANG3  18
#define K_VAN_LANG4  19
#define K_CHOAN1     20
#define K_CHOAN2     21
#define K_CHOAN3     22
#define K_CHOAN4     23
#define K_VAN_XA1    24
#define K_VAN_XA2    25
#define K_BOM_BUN    26
#define K_VAN_THAI   27
#define K_BOM_DP     28
#define K_VAN_O2     29

// ── Atlas EZO I2C Addresses ──────────────────────────────────
#define ADDR_DO   0x61
#define ADDR_ORP  0x62
#define ADDR_PH   0x63
#define ADDR_EC   0x64

// ── Hằng số ─────────────────────────────────────────────────
#define SO_CHOAN     4
#define MAX_LICH_AN  6

// ── Cấu hình ngưỡng ─────────────────────────────────────────
struct NguongCamBien {
  float do_tiet_kiem;    // > này: tắt bớt quạt
  float do_duy_tri;      // bình thường
  float do_canh_bao;     // bật 2 quạt
  float do_nguy_hiem;    // bật 4 quạt + blower
  float do_bat_het;      // bật tất cả + bơm DP
  float do_khan_cap;     // + van O₂
  float ph_min_cb;  float ph_max_cb;
  float ph_min_kc;  float ph_max_kc;
  float ec_min;     float ec_max;
  float orp_canh_bao;  float orp_nguy_hiem;
  float nhiet_min;  float nhiet_max;
  float muc_nuoc_cb;
  float do_ngung_an;   // DO dưới mức này: ngừng cho ăn
};

NguongCamBien nguong = {
  .do_tiet_kiem = 6.5, .do_duy_tri = 5.5, .do_canh_bao = 5.0,
  .do_nguy_hiem = 4.0, .do_bat_het = 3.5, .do_khan_cap = 3.0,
  .ph_min_cb = 7.5, .ph_max_cb = 8.5,
  .ph_min_kc = 7.0, .ph_max_kc = 9.0,
  .ec_min = 12.0, .ec_max = 28.0,
  .orp_canh_bao = 200, .orp_nguy_hiem = 150,
  .nhiet_min = 23.0, .nhiet_max = 32.0,
  .muc_nuoc_cb = 70,
  .do_ngung_an = 4.0
};

struct LichChoAn {
  uint8_t gio, phut, giay_chay;
  bool    may[SO_CHOAN];
};

LichChoAn lichAn[MAX_LICH_AN] = {
  {6,0,45,{true,true,true,true}},
  {9,0,45,{true,true,true,true}},
  {12,0,45,{true,true,true,true}},
  {15,0,45,{true,true,true,true}},
  {18,0,45,{true,true,true,true}},
  {21,0,45,{true,true,true,true}}
};

// ── Hardware Objects ─────────────────────────────────────────
Adafruit_MCP23X17 mcp1, mcp2;
Adafruit_ADS1115  ads;
VL53L0X           vlx;
MS5837            ms5837;
OneWire           ow(PIN_DS18B20);
DallasTemperature ds18b20(&ow);
LiquidCrystal_I2C lcd(0x3C, 20, 4);
WiFiClient        net;
PubSubClient      mqtt(net);
Preferences       prefs;

// ── Dữ Liệu Cảm Biến ────────────────────────────────────────
String  deviceID, macAddress;
float   doVal    = 0;
float   phVal    = 0;
float   ecVal    = 0;
float   orpVal   = 0;
float   nhietDo[3] = {28, 28, 28};
float   turbidity  = 0;
float   mucNuocCM  = 150;
float   apSuatBar  = 0;
float   doManPpt   = 0;

volatile uint32_t pulseCap  = 0;
volatile uint32_t pulseThai = 0;

int     quatDangChay = 0;
bool    boomPhongChay = false;

// ── Trạng Thái Cảnh Báo ──────────────────────────────────────
enum MucCanhBao { OK_MUC=0, CANH_BAO=1, NGUY_HIEM=2, KHAN_CAP=3 };
MucCanhBao mucDO=OK_MUC, mucPH=OK_MUC, mucEC=OK_MUC;
unsigned long lastAlertTime[8] = {0};

// ── Timers ───────────────────────────────────────────────────
unsigned long lastSensor=0, lastLcd=0, lastSchedule=0;
unsigned long lastBtn=0, lastLuuFlash=0;

// ════════════════════════════════════════════════════════════
//  RELAY
// ════════════════════════════════════════════════════════════
void setRelay(int k, bool on) {
  if (k < 0 || k > 31) return;
  bool v = on ? HIGH : LOW;
  if (k < 16) mcp1.digitalWrite(k,      v);
  else        mcp2.digitalWrite(k - 16, v);
}
void tatTatCaRelay() {
  for (int i = 0; i < 16; i++) {
    mcp1.digitalWrite(i, LOW);
    mcp2.digitalWrite(i, LOW);
  }
}

// ════════════════════════════════════════════════════════════
//  ĐỌC ATLAS EZO (I2C 3.3V trực tiếp)
// ════════════════════════════════════════════════════════════
float docAtlasEZO(uint8_t addr) {
  Wire.beginTransmission(addr);
  Wire.write('R');
  Wire.endTransmission();
  delay(600);
  Wire.requestFrom(addr, (uint8_t)7);
  char buf[8] = {0};
  int i = 0;
  while (Wire.available() && i < 7) {
    char c = Wire.read();
    if (c != 1 && c != 2) buf[i++] = c;
  }
  return atof(buf);
}

void docCamBienNuoc() {
  doVal  = docAtlasEZO(ADDR_DO);
  phVal  = docAtlasEZO(ADDR_PH);
  ecVal  = docAtlasEZO(ADDR_EC);
  orpVal = docAtlasEZO(ADDR_ORP);

  ds18b20.requestTemperatures();
  for (int i = 0; i < 3; i++) {
    float t = ds18b20.getTempCByIndex(i);
    if (t != DEVICE_DISCONNECTED_C) nhietDo[i] = t;
  }

  // Turbidity qua ADS1115 AIN0 (3.3V power → 0–3.3V output)
  int16_t rawT = ads.readADC_SingleEnded(0);
  float voltT  = rawT * 0.0001875f;  // 3.3V range
  turbidity    = (voltT >= 2.5f) ? 0 : max(0.0f,
                  -1120.4f*voltT*voltT + 5742.3f*voltT - 4353.8f);

  // Mực nước VL53L0X (I2C 3.3V)
  uint16_t dist = vlx.readRangeSingleMillimeters();
  if (!vlx.timeoutOccurred() && dist < 3000)
    mucNuocCM = max(0.0f, 200.0f - dist / 10.0f);

  // Áp suất MS5837 (I2C 3.3V)
  ms5837.read();
  apSuatBar = ms5837.pressure() / 1000.0f;

  // Quy đổi EC → ppt
  doManPpt = (ecVal / 1000.0f) * 0.55f;

  // Flow
  float flowCap  = pulseCap  * 0.0035f;
  float flowThai = pulseThai * 0.0035f;

  publishCamBien(flowCap, flowThai);
}

// ════════════════════════════════════════════════════════════
//  ĐIỀU KHIỂN DO — 6 CẤP (Ưu Tiên Tuyệt Đối)
// ════════════════════════════════════════════════════════════
void kiemTraDoVaDieuKhien() {
  // Không check chế độ AUTO — DO emergency override mọi lúc
  if (doVal < nguong.do_khan_cap) {
    // Cấp KHẨN CẤP: Tất cả + O₂
    for (int i = K_QUAT1; i <= K_BLOWER2; i++) setRelay(i, true);
    setRelay(K_BOM_DP, true);
    setRelay(K_VAN_O2, true);
    guiCanhBao("DO", KHAN_CAP, doVal, "mg/L", 0);  // Không anti-spam
    quatDangChay = 4;

  } else if (doVal < nguong.do_bat_het) {
    for (int i = K_QUAT1; i <= K_BLOWER2; i++) setRelay(i, true);
    setRelay(K_BOM_DP, true);
    guiCanhBaoAntiSpam("DO", NGUY_HIEM, doVal, "mg/L", 5);
    quatDangChay = 4;

  } else if (doVal < nguong.do_nguy_hiem) {
    for (int i = K_QUAT1; i <= K_BLOWER2; i++) setRelay(i, true);
    setRelay(K_BOM_DP, false);
    guiCanhBaoAntiSpam("DO", NGUY_HIEM, doVal, "mg/L", 10);
    quatDangChay = 4;

  } else if (doVal < nguong.do_canh_bao) {
    // Bật 2 quạt
    setRelay(K_QUAT1, true); setRelay(K_QUAT2, true);
    setRelay(K_QUAT3, false); setRelay(K_QUAT4, false);
    setRelay(K_BLOWER1, false); setRelay(K_BLOWER2, false);
    guiCanhBaoAntiSpam("DO", CANH_BAO, doVal, "mg/L", 15);
    quatDangChay = 2;

  } else if (doVal > nguong.do_tiet_kiem) {
    // DO cao — tiết kiệm điện
    setRelay(K_QUAT3, false); setRelay(K_QUAT4, false);
    setRelay(K_BLOWER1, false); setRelay(K_BLOWER2, false);
    quatDangChay = 2;

  } else {
    // Bình thường
    setRelay(K_QUAT1, true); setRelay(K_QUAT2, true);
    quatDangChay = 2;
  }
}

// ════════════════════════════════════════════════════════════
//  ĐIỀU KHIỂN pH
// ════════════════════════════════════════════════════════════
unsigned long lastBomVoi = 0;
bool bomVoiDangChay = false;

void kiemTraPH() {
  if (phVal < nguong.ph_min_kc || phVal > nguong.ph_max_kc) {
    guiCanhBao("pH", KHAN_CAP, phVal, "", 0);
  } else if (phVal < nguong.ph_min_cb) {
    // pH thấp → Bơm vôi tối đa 30 giây, nghỉ 2 giờ
    if (!bomVoiDangChay && millis() - lastBomVoi > 7200000UL) {
      setRelay(K_BOM_VOI, true);
      bomVoiDangChay = true;
      lastBomVoi = millis();
    }
    guiCanhBaoAntiSpam("pH", CANH_BAO, phVal, "", 20);
  } else if (phVal > nguong.ph_max_cb) {
    guiCanhBaoAntiSpam("pH", CANH_BAO, phVal, "", 20);
  }

  // Tắt bơm vôi sau 30 giây
  if (bomVoiDangChay && millis() - lastBomVoi > 30000) {
    setRelay(K_BOM_VOI, false);
    bomVoiDangChay = false;
  }
}

// ════════════════════════════════════════════════════════════
//  CHO ĂN TỰ ĐỘNG
// ════════════════════════════════════════════════════════════
void kiemTraLichChoAn() {
  time_t now = time(nullptr);
  struct tm* t = localtime(&now);
  if (t->tm_sec > 5) return;

  for (int l = 0; l < MAX_LICH_AN; l++) {
    if (lichAn[l].gio != t->tm_hour || lichAn[l].phut != t->tm_min) continue;

    // KHÔNG cho ăn khi DO thấp
    if (doVal < nguong.do_ngung_an) {
      String msg = "{\"ca\":" + String(l) + ",\"bo_qua\":true,\"ly_do\":\"DO_THAP\",\"do\":" + String(doVal, 1) + "}";
      mqtt.publish(("aqua/"+deviceID+"/cho_an/bo_qua").c_str(), msg.c_str());
      return;
    }

    for (int m = 0; m < SO_CHOAN; m++) {
      if (lichAn[l].may[m]) setRelay(K_CHOAN1 + m, true);
    }
    delay(lichAn[l].giay_chay * 1000UL);
    for (int m = 0; m < SO_CHOAN; m++) setRelay(K_CHOAN1 + m, false);

    String msg = "{\"ca\":" + String(l) + ",\"giay\":" + String(lichAn[l].giay_chay) + "}";
    mqtt.publish(("aqua/"+deviceID+"/cho_an/done").c_str(), msg.c_str());
  }
}

// ════════════════════════════════════════════════════════════
//  CẢNH BÁO MQTT + LCD
// ════════════════════════════════════════════════════════════
void guiCanhBao(const char* loai, MucCanhBao muc,
                float val, const char* dv, uint16_t anti_phut) {
  // anti_phut = 0: Gửi ngay (không anti-spam)
  static const char* TEN[] = {"ok","canh_bao","nguy_hiem","khan_cap"};
  String topic = "aqua/" + deviceID + "/alert/" + loai;
  String msg = "{\"muc\":\"" + String(TEN[muc]) +
               "\",\"gia_tri\":" + String(val, 2) +
               ",\"don_vi\":\"" + dv +
               "\",\"t\":" + String((uint32_t)time(nullptr)) + "}";
  mqtt.publish(topic.c_str(), msg.c_str());
  capNhatLCD_CanhBao(loai, muc, val);
}

void guiCanhBaoAntiSpam(const char* loai, MucCanhBao muc,
                         float val, const char* dv, uint16_t phut) {
  static const char* LOAI[] = {"DO","pH","EC","ORP","NHIET","MUC","THUC_AN","DIEN"};
  int idx = 0;
  for (int i = 0; i < 8; i++) if (strcmp(loai, LOAI[i]) == 0) { idx = i; break; }
  if (millis() - lastAlertTime[idx] < (unsigned long)phut * 60000UL) return;
  lastAlertTime[idx] = millis();
  guiCanhBao(loai, muc, val, dv, phut);
}

// ════════════════════════════════════════════════════════════
//  LCD OLED SH1106 (I2C 3.3V)
// ════════════════════════════════════════════════════════════
bool lcdBlink = false;

void capNhatLCD_BinhThuong() {
  char buf[21];
  lcd.setCursor(0,0);
  snprintf(buf,21,"%s DO:%.1f pH:%.1f  ",
           deviceID.c_str(), doVal, phVal);
  lcd.print(buf);
  lcd.setCursor(0,1);
  snprintf(buf,21,"T:%.1fC Sal:%.1fppt",
           nhietDo[1], doManPpt);
  lcd.print(buf);
  lcd.setCursor(0,2);
  float pct = (mucNuocCM / 200.0f) * 100.0f;
  snprintf(buf,21,"ORP:%dmV N:%.0f%%    ",
           (int)orpVal, pct);
  lcd.print(buf);
  lcd.setCursor(0,3);
  snprintf(buf,21,"Quat:%d/4 Trb:%.0fNTU",
           quatDangChay, turbidity);
  lcd.print(buf);
}

void capNhatLCD_CanhBao(const char* loai, MucCanhBao muc, float val) {
  if (muc < NGUY_HIEM) return;
  lcd.clear();
  if (muc == KHAN_CAP) {
    lcd.setCursor(0,0); lcd.print(lcdBlink ? "[!!!] KHAN CAP [!!!]"
                                           : "                    ");
  } else {
    lcd.setCursor(0,0); lcd.print("!!! CANH BAO !!!");
  }
  char buf[21];
  snprintf(buf, 21, "%s: %.2f", loai, val);
  lcd.setCursor(0,1); lcd.print(buf);
  if (muc == KHAN_CAP && strcmp(loai,"DO")==0) {
    lcd.setCursor(0,2); lcd.print("Da bat O2 + BOM DP  ");
    lcd.setCursor(0,3); lcd.print("BAO DONG NGAY!!!    ");
  }
}

// ════════════════════════════════════════════════════════════
//  PUBLISH CẢM BIẾN
// ════════════════════════════════════════════════════════════
void publishCamBien(float flowCap, float flowThai) {
  StaticJsonDocument<512> doc;
  doc["do"]           = doVal;
  doc["ph"]           = phVal;
  doc["ec_uS"]        = ecVal;
  doc["do_man_ppt"]   = doManPpt;
  doc["orp_mV"]       = orpVal;
  JsonArray ta = doc.createNestedArray("nhiet_do");
  for (int i = 0; i < 3; i++) ta.add(nhietDo[i]);
  doc["nhiet_do_tb"]  = (nhietDo[0]+nhietDo[1]+nhietDo[2])/3.0f;
  doc["turbidity_ntu"]= turbidity;
  doc["muc_nuoc_cm"]  = mucNuocCM;
  doc["ap_suat_bar"]  = apSuatBar;
  doc["flow_cap_L"]   = flowCap;
  doc["flow_thai_L"]  = flowThai;
  doc["quat_dang_chay"] = quatDangChay;
  char buf[512]; serializeJson(doc, buf);
  mqtt.publish(("aqua/"+deviceID+"/sensor").c_str(), buf);
}

// ════════════════════════════════════════════════════════════
//  SAVE / LOAD CONFIG
// ════════════════════════════════════════════════════════════
void saveConfig() {
  prefs.begin("aqua", false);
  prefs.putBytes("nguong", &nguong, sizeof(nguong));
  prefs.putBytes("lican",  lichAn,  sizeof(lichAn));
  prefs.end();
}
void loadConfig() {
  prefs.begin("aqua", true);
  prefs.getBytes("nguong", &nguong, sizeof(nguong));
  prefs.getBytes("lican",  lichAn,  sizeof(lichAn));
  prefs.end();
}
void loadMqttConfig() {
  prefs.begin("mqtt_cfg", true);
  prefs.getString("ma_khach",  cfgMaKhach,  sizeof(cfgMaKhach));
  prefs.getString("mqtt_host", cfgMqttHost, sizeof(cfgMqttHost));
  prefs.getString("mqtt_user", cfgMqttUser, sizeof(cfgMqttUser));
  prefs.getString("mqtt_pass", cfgMqttPass, sizeof(cfgMqttPass));
  prefs.getString("mqtt_port", cfgMqttPort, sizeof(cfgMqttPort));
  prefs.end();
}

// ════════════════════════════════════════════════════════════
//  MQTT CALLBACK
// ════════════════════════════════════════════════════════════
void mqttCallback(char* topic, byte* payload, unsigned int len) {
  payload[len] = 0;
  String t(topic), msg((char*)payload);
  StaticJsonDocument<512> doc;
  if (msg.length() > 2) deserializeJson(doc, msg);

  if (t.endsWith("/dung")) { tatTatCaRelay(); return; }

  if (t.endsWith("/che_do/set")) {
    bool autoMode = (strcmp(doc["che_do"]|"","tu_dong") == 0);
    digitalWrite(PIN_LED_AUTO, autoMode);
    mqtt.publish(("aqua/"+deviceID+"/che_do").c_str(),
                 autoMode ? "tu_dong" : "thu_cong", true);
    return;
  }

  if (t.endsWith("/nguong/set")) {
    nguong.do_canh_bao   = doc["do_canh_bao"]  | nguong.do_canh_bao;
    nguong.do_nguy_hiem  = doc["do_nguy_hiem"] | nguong.do_nguy_hiem;
    nguong.do_khan_cap   = doc["do_khan_cap"]  | nguong.do_khan_cap;
    nguong.ph_min_cb     = doc["ph_min_cb"]    | nguong.ph_min_cb;
    nguong.ph_max_cb     = doc["ph_max_cb"]    | nguong.ph_max_cb;
    nguong.do_ngung_an   = doc["do_ngung_an"]  | nguong.do_ngung_an;
    saveConfig();
    return;
  }

  if (t.endsWith("/cho_an/lich/set")) {
    JsonArray la = doc["lich"];
    for (int i = 0; i < MAX_LICH_AN && i < (int)la.size(); i++) {
      lichAn[i].gio       = la[i]["gio"]       | 6;
      lichAn[i].phut      = la[i]["phut"]      | 0;
      lichAn[i].giay_chay = la[i]["giay_chay"] | 45;
    }
    saveConfig(); return;
  }

  if (t.endsWith("/cho_an/lenh")) {
    int giay = doc["giay"] | 30;
    for (int m = 0; m < SO_CHOAN; m++) {
      if (doc["may"][m] | false) setRelay(K_CHOAN1 + m, true);
    }
    delay(giay * 1000UL);
    for (int m = 0; m < SO_CHOAN; m++) setRelay(K_CHOAN1 + m, false);
    return;
  }

  if (t.endsWith("/bom_voi/lenh")) {
    int giay = doc["giay"] | 30;
    setRelay(K_BOM_VOI, true);
    delay(giay * 1000UL);
    setRelay(K_BOM_VOI, false);
    return;
  }
}

// ════════════════════════════════════════════════════════════
//  MQTT KẾT NỐI
// ════════════════════════════════════════════════════════════
void connectMQTT() {
  String lwt = "aqua/" + deviceID + "/status";
  while (!mqtt.connected()) {
    if (mqtt.connect(deviceID.c_str(), cfgMqttUser, cfgMqttPass,
                     lwt.c_str(), 1, true, "offline")) {
      mqtt.publish(lwt.c_str(), "online", true);
      String info = "{\"ma_khach\":\""+deviceID+"\",\"mac\":\""+macAddress+"\",\"fw\":\"1.0.0\"}";
      mqtt.publish(("aqua/"+deviceID+"/info").c_str(), info.c_str(), true);
      mqtt.subscribe(("aqua/"+deviceID+"/#").c_str());
    } else {
      esp_task_wdt_reset(); delay(3000);
    }
  }
}

// ════════════════════════════════════════════════════════════
//  SETUP
// ════════════════════════════════════════════════════════════
void setup() {
  Serial.begin(115200);
  esp_task_wdt_init(WDT_TIMEOUT, true);
  esp_task_wdt_add(NULL);

  // ── Khởi tạo I2C + Relay ──────────────────────────────────
  Wire.begin(PIN_SDA, PIN_SCL);
  if (!mcp1.begin_I2C(0x20)) Serial.println("ERR: MCP1");
  if (!mcp2.begin_I2C(0x21)) Serial.println("ERR: MCP2");
  for (int i = 0; i < 16; i++) {
    mcp1.pinMode(i, OUTPUT); mcp1.digitalWrite(i, LOW);
    mcp2.pinMode(i, OUTPUT); mcp2.digitalWrite(i, LOW);
  }

  // ── ĐỌC DO NGAY — TRƯỚC CẢ WiFi ─────────────────────────
  // Tôm/Cá không thể chờ WiFi kết nối khi DO thấp!
  ads.begin(0x48);
  ds18b20.begin();
  Serial.println("Doc DO ngay...");
  float doInit = docAtlasEZO(ADDR_DO);
  Serial.printf("DO boot: %.2f mg/L\n", doInit);
  if (doInit < 4.0f && doInit > 0) {
    Serial.println("DO THAP! Bat quat ngay!");
    for (int i = K_QUAT1; i <= K_BLOWER2; i++) setRelay(i, true);
    if (doInit < 3.0f) {
      setRelay(K_BOM_DP, true);
      setRelay(K_VAN_O2, true);
    }
  }

  // ── Init còn lại ─────────────────────────────────────────
  vlx.init();
  vlx.startContinuous();
  ms5837.setModel(MS5837::MS5837_30BA);
  ms5837.setFluidDensity(1025); // Nước biển
  ms5837.init();

  lcd.init(); lcd.backlight();
  lcd.setCursor(0,0); lcd.print("THUY HAI SAN v1.0");
  lcd.setCursor(0,1); lcd.print("Dang khoi dong...");

  pinMode(PIN_BTN,     INPUT_PULLUP);
  pinMode(PIN_LED_AUTO, OUTPUT);
  pinMode(PIN_LED_TAY,  OUTPUT);

  attachInterrupt(digitalPinToInterrupt(FLOW_CAP_PIN),
                  [](){pulseCap++;}, FALLING);
  attachInterrupt(digitalPinToInterrupt(FLOW_THAI_PIN),
                  [](){pulseThai++;}, FALLING);

  loadConfig();
  loadMqttConfig();

  // ── WiFiManager ──────────────────────────────────────────
  uint8_t m[6]; WiFi.macAddress(m);
  macAddress = "";
  for (int i = 0; i < 6; i++) { if(m[i]<16) macAddress+="0"; macAddress+=String(m[i],HEX); }

  WiFiManager wm;
  String apName = "AoNuoi_" + macAddress.substring(8);

  WiFiManagerParameter pMa("ma_khach",  "Ma ao / Ma khach (VD: TOM_001)", cfgMaKhach,  32);
  WiFiManagerParameter pHost("mqtt_host","MQTT Host", cfgMqttHost, 64);
  WiFiManagerParameter pUser("mqtt_user","MQTT User", cfgMqttUser, 32);
  WiFiManagerParameter pPass("mqtt_pass","MQTT Pass", cfgMqttPass, 32);

  wm.addParameter(&pMa);
  wm.addParameter(&pHost);
  wm.addParameter(&pUser);
  wm.addParameter(&pPass);

  wm.setSaveParamsCallback([&]() {
    prefs.begin("mqtt_cfg", false);
    prefs.putString("ma_khach",  pMa.getValue());
    prefs.putString("mqtt_host", pHost.getValue());
    prefs.putString("mqtt_user", pUser.getValue());
    prefs.putString("mqtt_pass", pPass.getValue());
    prefs.end();
    strlcpy(cfgMaKhach,  pMa.getValue(),   sizeof(cfgMaKhach));
    strlcpy(cfgMqttHost, pHost.getValue(), sizeof(cfgMqttHost));
    strlcpy(cfgMqttUser, pUser.getValue(), sizeof(cfgMqttUser));
    strlcpy(cfgMqttPass, pPass.getValue(), sizeof(cfgMqttPass));
  });

  wm.autoConnect(apName.c_str(), "12345678");

  deviceID = (strlen(cfgMaKhach) > 0) ? String(cfgMaKhach) : macAddress;
  Serial.println("Device ID: " + deviceID);

  configTzTime(TZ_VN, NTP_SERVER);
  int port = atoi(cfgMqttPort);
  mqtt.setServer(cfgMqttHost, port > 0 ? port : MQTT_PORT_DEFAULT);
  mqtt.setCallback(mqttCallback);
  mqtt.setBufferSize(512);
  connectMQTT();

  lcd.clear();
  lcd.setCursor(0,0); lcd.print("ID: " + deviceID);
  lcd.setCursor(0,1); lcd.print("MQTT: " + String(cfgMqttHost));
}

// ════════════════════════════════════════════════════════════
//  LOOP
// ════════════════════════════════════════════════════════════
void loop() {
  esp_task_wdt_reset();
  if (!mqtt.connected()) connectMQTT();
  mqtt.loop();

  // Cảm biến + điều khiển: mỗi 15 giây
  if (millis() - lastSensor > 15000) {
    lastSensor = millis();
    docCamBienNuoc();
    kiemTraDoVaDieuKhien();   // Ưu tiên số 1
    kiemTraPH();
  }

  // Lịch cho ăn: mỗi 60 giây
  if (millis() - lastSchedule > 60000) {
    lastSchedule = millis();
    kiemTraLichChoAn();
  }

  // LCD: mỗi 1 giây
  if (millis() - lastLcd > 1000) {
    lastLcd = millis();
    lcdBlink = !lcdBlink;
    capNhatLCD_BinhThuong();
  }

  // Lưu timestamp vào flash mỗi 60s (phục hồi ca ăn bị lỡ)
  if (millis() - lastLuuFlash > 60000) {
    lastLuuFlash = millis();
    prefs.begin("aqua_tt", false);
    prefs.putULong("t_off", (uint32_t)time(nullptr));
    prefs.end();
  }

  // Nút vật lý 2 giây
  if (millis() - lastBtn > 200) {
    lastBtn = millis();
    if (digitalRead(PIN_BTN) == LOW) {
      unsigned long t0 = millis();
      while (digitalRead(PIN_BTN) == LOW && millis()-t0 < 2000) esp_task_wdt_reset();
      if (millis()-t0 >= 2000) {
        bool autoMode = (digitalRead(PIN_LED_AUTO) == LOW);
        digitalWrite(PIN_LED_AUTO, autoMode ? LOW : HIGH);
        digitalWrite(PIN_LED_TAY,  autoMode ? HIGH : LOW);
        mqtt.publish(("aqua/"+deviceID+"/che_do").c_str(),
                     autoMode ? "thu_cong" : "tu_dong", true);
      }
    }
  }
}
```

---

*[← Relay Map](04_relay-channel-map.md) | [MQTT API →](07_mqtt-api.md)*
