```cpp
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "Your_WiFi_Name";
const char* password = "Your_WiFi_Password";

WebServer server(80);
float temperature = 0.0;
float humidity = 0.0;
int soil_moisture = 0;

void handleRoot() {
  String html = "<html><body>";
  html += "<h1>کشاورزی هوشمند با اینترنت اشیا</h1>";
  html += "<p>دما: " + String(temperature) + "°C</p>";
  html += "<p>رطوبت: " + String(humidity) + "%</p>";
  html += "<p>رطوبت خاک: " + String(soil_moisture) + "</p>";
  html += "</body></html>";

  server.send(200, "text/html", html);
}

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("در حال اتصال به WiFi...");
  }

  Serial.println("اتصال موفقیت‌آمیز!");

  server.on("/", handleRoot);
  server.begin();
}

void loop() {
  temperature = random(20, 35); // شبیه‌سازی داده‌ها
  humidity = random(30, 80);
  soil_moisture = random(200, 800);
  
  server.handleClient();
  delay(5000);
}```
