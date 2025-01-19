# ApiaryLink 蜂联智 - Monitor, Protect, Grow. 检测，保护，成长
**Personal Project by Ash**

Legend预览: 
1. *Brief overview of what my project is about对于我的项目的一个小介绍:*
2. *Moving onto the more technical part接下来讲解一些工程部分的内容:*

**Brief overview of what my project is about对于我的项目的一个小介绍:**

**ApiaryLink** is my personal project in which I explore the potential of the Internet of Things (IoT) to create a smarter and more sustainable way to monitor beehives. Through this project, I aimed to develop a beehive monitoring system that tracks key factors such as temperature, humidity, and weight of the hives. By monitoring these variables, I seek to identify patterns and trends that environmental factors, such as climate change could influence. Moreover, my goal is for the system to provide real-time alerts for immediate threats to the beehive, including dangers like wildfires or drastic temperature changes that could harm the bees.
ApiaryLink 是我的个人项目，在该项目中，我探索了物联网（IoT）的潜力，旨在创建一种更智能、更可持续的方式来监控蜂巢。通过这个项目，我的目标是开发一个蜂巢监控系统，跟踪蜂巢的关键因素，如温度、湿度和重量。通过监控这些变量，我希望能够识别出环境因素（如气候变化）可能影响的模式和趋势。此外，我的目标是让系统能够提供实时警报，提醒蜂巢面临的即时威胁，包括像野火或剧烈温度变化等可能危害蜜蜂的危险。

The project is dedicated to addressing climate change through adaptive strategies, rather than attempting to resolve the issue directly. In 2025, it is an unrealistic claim to fully reverse or stop climate change. Therefore, it is essential to focus on mitigating the humanitarian and ecological impacts it causes. **ApiaryLink** aims to reduce these effects by offering a solution that allows farmers and beekeepers to monitor the health of their hives and respond to potential threats in a timely manner.
该项目致力于通过适应性策略应对气候变化，而非直接解决这一问题。到了2025年，完全扭转或停止气候变化是一个不现实的目标。因此，关键是要集中精力减轻气候变化带来的社会和生态影响。ApiaryLink 旨在通过为农民和养蜂人提供一种解决方案，使他们能够及时监控蜂巢健康并应对潜在威胁，从而减少这些影响。

To bring this project to life, I employed an **Arduino Uno board** and a variety of sensors to collect physical data from the hives. The Arduino board is responsible for gathering real-time information on environmental conditions, such as humidity, temperature, and hive weight, which are crucial for understanding hive health. The data collected is then processed and transmitted to an online system.
为了实现这个项目，我使用了 Arduino Uno 板 和多种传感器来收集蜂巢的物理数据。Arduino 板负责实时收集环境条件的数据，如湿度、温度和蜂巢重量，这些数据对了解蜂巢健康至关重要。收集到的数据随后会被处理并传输到在线系统中。

For the web interface, I used **Django**, a Python framework, to build a user-friendly dashboard that allows beekeepers to view the data and receive notifications. The platform provides insights into hive conditions and can issue alerts for immediate threats, such as abnormal temperature changes or significant weight shifts that might indicate problems like a colony collapse or wildfire risks. 在网页界面方面，我使用了 Django，一个 Python 框架，构建了一个用户友好的仪表盘，让养蜂者可以查看数据并接收通知。该平台提供蜂巢状况的见解，并能够发布关于即时威胁的警报，例如异常的温度变化或重量变化，这些可能表明蜂巢问题，如蜂群崩溃或野火风险。

For thehe entire system, which includes both the hardware and the online platform, I will aim for it to be set up for just 20 RMB per hive, making it an affordable solution for beekeepers around the world. The real-time feedback provided by **ApiaryLink** empowers users to take proactive measures, ultimately helping them manage their hives more efficiently and safely.
整个系统，包括硬件和在线平台，我将尝试把成本降低到可以以每个蜂巢仅20元人民币的价格进行设置，使其成为全球养蜂人的一个经济实惠的解决方案。ApiaryLink 提供的实时反馈使用户能够采取积极的措施，最终帮助他们更高效、安全地管理蜂巢。

My system, physical and codes, is far from perfection and the ability to be actually implicated. Thus, any advice or suggestions would be greatly appreciated!!!
我的系统，无论是在硬件还是代码方面，距离完美和实际应用还有很大差距。因此，任何建议或反馈将不胜感激！

**Moving onto the more technical part接下来讲解一些工程部分的内容:**

While creating the actual prototype, I opted to use an ESP32 board instead of an Arduino board. This decision was made because I didn’t need a breadboard to connect all my components, making the process more efficient and easier to execute. However, I still wish to use an Arduino board in the future, as the use of a breadboard would make it easier to add more sensors and functions to the system. Additionally, the ESP32 has built-in Wi-Fi, allowing it to transmit data wirelessly, which makes it ideal for projects that require remote communication or monitoring. Below is the code for my technical system:
在创建实际原型时，我选择使用ESP32开发板而非Arduino开发板。这个决定是因为我无需使用面包板来连接所有组件，从而使得整个过程更加高效且易于实现。然而，我仍希望在未来使用Arduino开发板，因为使用面包板可以更容易地添加更多传感器和功能。此外，ESP32具有内置Wi-Fi功能，可以无线传输数据，这使其非常适合需要远程通信或监控的项目。以下是我的技术系统代码：
```

  #include <WiFi.h>

  #include <PubSubClient.h>

  // WiFi Credentials

  #include "HX711.h"

  #include <SimpleDHT.h>


int pinDHT11 = 4;

SimpleDHT11 dht11(pinDHT11);

HX711 scale;

//  adjust pins if needed

uint8_t dataPin = 18;

uint8_t clockPin = 19;

uint32_t start, stop;

volatile float f;

// MQTT Broker Settings

const char *mqtt_broker = "broker.emqx.io";

const char *mqtt_topic = "bee";

const char *mqtt_username = "emqx";

const char *mqtt_password = "public";

const int mqtt_port = 1883;

// WiFi

const char* ssid = "beepersonalproject";

const char* password = "beepersonalproject";

WiFiClient espClient;

PubSubClient mqtt_client(espClient);

// Function Declarations

void connectToWiFi();

void connectToMQTT();

void mqttCallback(char *mqtt_topic, byte *payload, unsigned int length);

void setup() {

    Serial.begin(115200);
    
    connectToWiFi();
    
    mqtt_client.setServer(mqtt_broker, mqtt_port);
    
    mqtt_client.setKeepAlive(60);
    
    mqtt_client.setCallback(mqttCallback); // Corrected callback function name
    
    connectToMQTT();
    
    Serial.begin(115200);
    
  Serial.println(__FILE__);
  
  Serial.print("HX711_LIB_VERSION: ");
  
  Serial.println(HX711_LIB_VERSION);
  
  Serial.println();

  scale.begin(dataPin, clockPin);


  //  load cell factor 5 KG
  
  scale.set_scale(420.0983);
  
  //  reset the scale to zero = 0
  
  scale.tare();

  Serial.println("\nPERFORMANCE");
  
  start = micros();
  
  f = 0;
  
  for (int i = 0; i < 100; i++)
  
  {
  
    f = scale.read_median(7);
    
  }
  
  stop = micros();
  
  Serial.print("100x read_median(7) = ");
  
  Serial.println(stop - start);
  
  Serial.print("  VAL: ");
  
  Serial.println(f, 2);
  
}

void connectToWiFi() {

    WiFi.begin(ssid, password);
    
    Serial.print("Connecting to WiFi");
    
    while (WiFi.status() != WL_CONNECTED) {
    
        delay(500);
        
        Serial.print(".");
        
    }
    
    Serial.println("\nConnected to WiFi");
    
}

void connectToMQTT() {

    while (!mqtt_client.connected()) {
    
        String client_id = "esp32-client-" + String(WiFi.macAddress());
        
        Serial.printf("Connecting to MQTT Broker as %s.....\n", client_id.c_str());
        
        if (mqtt_client.connect(client_id.c_str(), mqtt_username, mqtt_password)) {
        
            Serial.println("Connected to MQTT broker");
            
            mqtt_client.subscribe(mqtt_topic);
            
            mqtt_client.publish(mqtt_topic, "Hi EMQX I'm ESP32 ^^"); // Publish message upon successful connection
        } else {
        
            Serial.print("Failed, rc=");
            
            Serial.print(mqtt_client.state());
            
            Serial.println(" try again in 5 seconds");
            
            delay(5000);
            
        }
        
    }
    
}

void mqttCallback(char *mqtt_topic, byte *payload, unsigned int length) {

    Serial.print("Message received on mqtt_topic: ");
    
    Serial.println(mqtt_topic);
    
    Serial.print("Message: ");
    
    for (unsigned int i = 0; i < length; i++) {
    
        Serial.print((char) payload[i]);
        
    }
    
    Serial.println("\n-----------------------");
    
}



void loop() {

  if (!mqtt_client.connected()) {
  
        connectToMQTT();
        
    }
    
    byte temperature = 0;
    
  byte humidity = 0;
  
  int err = SimpleDHTErrSuccess;
  
  if ((err = dht11.read(&temperature, &humidity, NULL)) != SimpleDHTErrSuccess) {
    Serial.print("Read DHT11 failed, err="); Serial.print(SimpleDHTErrCode(err));
    Serial.print(","); Serial.println(SimpleDHTErrDuration(err)); delay(1000);
    return;
    
  }
  
  Serial.print((int)temperature); Serial.print(" *C, "); 
  
  Serial.print((int)humidity); Serial.println(" H");
  
  f = scale.read_median(7);
  
  Serial.print("weight: ");
  
  Serial.println(f);

  
  delay(2000);
  
  mqtt_client.loop();
  
  String temperatureString = String(temperature)+" "+String(humidity)+" "+String(f);
  
  mqtt_client.publish(mqtt_topic, temperatureString.c_str());
  
  //mqtt_client.publish(mqtt_topic, humidity);
  
  //mqtt_client.publish(mqtt_topic, f);
  
}

```
