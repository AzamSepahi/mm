```cpp
#include <WiFi.h>
#include <HTTPClient.h>
#include <DHT.h>

#define DHTPIN 14      // پین اتصال سنسور DHT
#define DHTTYPE DHT22  // نوع سنسور DHT (DHT11 یا DHT22)
#define SOIL_SENSOR_PIN 34  // سنسور رطوبت خاک به A0 متصل است

const char* ssid = "Your_WiFi_Name";  
const char* password = "Your_WiFi_Password";
const char* server = "http://api.thingspeak.com/update?api_key=YOUR_API_KEY";

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("در حال اتصال به WiFi...");
  }
  
  Serial.println("اتصال موفقیت‌آمیز!");
  dht.begin();
}

void loop() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();
  int soil_moisture = analogRead(SOIL_SENSOR_PIN);

  if (isnan(temp) || isnan(hum)) {
    Serial.println("خطا در خواندن داده از سنسور DHT!");
    return;
  }

  Serial.print("دما: "); Serial.print(temp); Serial.println("°C");
  Serial.print("رطوبت: "); Serial.print(hum); Serial.println("%");
  Serial.print("رطوبت خاک: "); Serial.println(soil_moisture);

  // ارسال داده‌ها به Thingspeak
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    String url = server + "&field1=" + String(temp) + "&field2=" + String(hum) + "&field3=" + String(soil_moisture);
    
    http.begin(url);
    int httpCode = http.GET();
    
    if (httpCode > 0) {
      Serial.println("داده‌ها ارسال شدند!");
    } else {
      Serial.println("خطا در ارسال داده!");
    }
    
    http.end();
  }

  delay(15000);  // ارسال داده‌ها هر ۱۵ ثانیه
}```
