## Arduino

### 点灯

在接入一个设备之前，我们需要考虑这个设备需要实现哪些功能。  
在本示例中，点亮一个LED灯，意味着设备需要接收到客户端发送的**“开灯”**和**“关灯”**两个指令，同时接收硬件反馈回来的状态信息。
在酷易物联的物联思维中，意味着用户需要分别设置代表“开灯”和“关灯”两个指令的命令字段以及代表“状态”的通信字段。  
本示例中，将设置**open为开灯**，**close为关灯**和**ledsta为状态**。  
当然，您也可以在创建设备时，直接选择模板：示例——点灯


字段类型 | name |  说明  
-|-|-
命令字段 | open | 开板载灯 |
命令字段 | close | 关板载灯 |
通信字段 | ledsta | 板载灯状态 |

#### 代码

 - 可以通过设置模板后从代码生成中获取
 - CoolE库中的**Blink**示例，填好自己的DevelopKey

```cpp
#include <CoolE.h>

CoolE iot("{developkey}");    //!!!设备DevelopKey
String recv;

void handleGet();             // 处理GET指令更新通信字段
void handleCommand();         // 处理命令字段

void setup()
{
  // 功能部分
  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, HIGH);
  // IoT部分
  iot.setDebug(true);
  iot.start();
}

void loop()
{
  // IoT部分
  iot.loop();
  recv = iot.getRevContent();
  // Serial.println(recv);
  handleGet();
  handleCommand();
  delay(100);
}

void handleGet(){
  // 接收GET指令并更新通信字段数据
  String get = iot.decode(recv,GET);
  if (get == "__all__" || get == "ledsta") {
    iot.publish();
  }
}
void handleCommand(){
  // 接收命令字段数据
  String command = iot.decode(recv,COMMAND);
  if (command == "open") {
    digitalWrite(LED_BUILTIN, LOW);
    iot.updateReport("ledsta", "开");
    iot.publish("ledsta");
  } else if (command == "close") {
    digitalWrite(LED_BUILTIN, HIGH);
    iot.updateReport("ledsta", "关");
    iot.publish("ledsta");
  }
}
```

---

### 温湿度及显示

您可以在创建设备时，直接选择模板：示例——温湿度及显示


字段类型 | name |  说明  
-|-|-
命令字段 | open | 开板载灯 |
命令字段 | close | 关板载灯 |
通信字段 | ledsta | 板载灯状态 |
通信字段 | temp | 温度 |
通信字段 | humi | 湿度 |
内容字段 | line1 | OLED显示第一行 |
内容字段 | line2 | OLED显示第二行 |

#### 代码

 - 可以通过设置模板后从代码生成中获取
 - CoolE库中的**DHT11andSSD1306**示例，填好自己的DevelopKey

```cpp
#include <CoolE.h>
#include <SimpleDHT.h>
#include <U8g2lib.h>
#include <Wire.h>

CoolE iot("{developkey}");    //!!!设备DevelopKey
String recv;
SimpleDHT11 dht11(14);        // dht11 pin
U8G2_SSD1306_128X64_NONAME_F_HW_I2C u8g2(U8G2_R0,U8X8_PIN_NONE);  //这里使用的是硬件I2C，如用软件I2C请参照U8G2库说明设置

void DisplayDisconnect();     // WIFI连接成功前的OLED显示
void updateDHT();             // 更新温湿度数据
void OLEDDisplay();           // OLED展示
void handleGet();             // 处理GET指令更新通信字段
void handleCommand();         // 处理命令字段
void handleContent();         // 处理内容字段

void setup()
{
  // 功能部分
  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, HIGH);
  u8g2.begin();
  updateDHT();
  DisplayDisconnect();
  // IoT部分
  iot.setDebug(true);
  iot.start();
}

void loop()
{
  // 显示部分
  OLEDDisplay();
  // IoT部分
  iot.loop();
  recv = iot.getRevContent();
  // Serial.println(recv);
  handleGet();
  handleCommand();
  handleContent();
  delay(100);
}

void handleGet(){
  // 接收GET指令并更新通信字段数据
  String get = iot.decode(recv,GET);
  if (get == "__all__") {
    updateDHT();
    iot.updateReport("ledsta",digitalRead(LED_BUILTIN) == HIGH ? "关" : "开");
    iot.publish();
  } else if (get == "temp") {
    updateDHT();
    iot.publish("temp");
  } else if (get == "humi") {
    updateDHT();
    iot.publish("humi");
  } else if (get == "ledsta") {
    iot.publish("ledsta");
  }
}
void handleCommand(){
  // 接收命令字段数据
  String command = iot.decode(recv,COMMAND);
  if (command == "open") {
    digitalWrite(LED_BUILTIN, LOW);
    iot.updateReport("ledsta", "开");
    iot.publish("ledsta");
  } else if (command == "close") {
    digitalWrite(LED_BUILTIN, HIGH);
    iot.updateReport("ledsta", "关");
    iot.publish("ledsta");
  }
}
void handleContent(){
  // 接收内容字段数据
  String content_name = iot.decode(recv,CONTENT_NAME);
  String content_data = iot.decode(recv,CONTENT_DATA);
  if (content_name == "line1") {
    iot.updateContent("line1",content_data);
  } else if (content_name == "line2") {
    iot.updateContent("line2",content_data);
  }
}

void DisplayDisconnect(){
  u8g2.clearBuffer();
  u8g2.setFont(u8g2_font_unifont_t_chinese2);
  u8g2.drawStr(0, 15,"AP:device_id");
  u8g2.drawStr(0, 30,"Pswd:cooleiot");
  u8g2.drawStr(0, 48,"To APP Set WiFi");
  u8g2.drawStr(0, 62,"or 192.168.4.1");
  u8g2.sendBuffer();
}

void updateDHT(){
  byte temperature = 0;
  byte humidity = 0;
  int err = SimpleDHTErrSuccess;
  if ((err = dht11.read(&temperature, &humidity, NULL)) != SimpleDHTErrSuccess) {
    // Serial.print("Read DHT11 failed, err="); Serial.println(err);
    delay(1000);
    return;
  }
  iot.updateReport("temp",temperature);
  iot.updateReport("humi",humidity);
}

void OLEDDisplay(){
  u8g2.clearBuffer();
  u8g2.setFont(u8g2_font_unifont_t_chinese2);
  u8g2.drawStr(0,10,"CoolE IoT Demo");
  u8g2.setCursor(0, 28);
  u8g2.print(iot.getContent("line1"));
  u8g2.setCursor(0, 43);
  u8g2.print(iot.getContent("line2"));
  u8g2.setCursor(0, 62);
  u8g2.print("Tem:"+iot.getReport("temp")+"C");
  u8g2.print("  Hum:"+iot.getReport("humi")+"%");
  u8g2.sendBuffer();
}
```

## Python3

### 测试例程

本测试例程不要求任何字段，同时在完成了准备工作后，也能在任何运行Python3的机器上运行

#### 代码

 - 请填好自己的DevelopKey

```python
from CoolEIoT import CoolE
import time

iot = CoolE("{developkey}")

iot.setDebug(True)
iot.start()

def handleGet():
    get = iot.decode(iot.recv,"GET")
    if get is not None:
        print("接收到[" + get + "]GET指令")

def handleCommand():
    command = iot.decode(iot.recv,"COMMAND")
    if command is not None:
        print("接收到[" + command + "]命令字段")

def handleContent():
    content = iot.decode(iot.recv,"CONTENT")
    if content is not None:
        print("接收到[" + content['n'] + "]内容字段，内容为：" + content['d'])

try:
    while True:
        iot.loop()
        handleGet()
        handleCommand()
        handleContent()
        time.sleep(0.1)
except KeyboardInterrupt:
    iot.stop()

```

---

### 系统信息监控

您可以在创建设备时，直接选择模板：示例——系统信息监控

字段类型 | name |  说明  
-|-|-
命令字段 | shutdown | 关机 |
命令字段 | reboot | 重启 |
通信字段 | cpuCount | CPU数量 |
通信字段 | cpuPercent | CPU利用率 |
通信字段 | sysBootTime | 系统开机时间 |
通信字段 | bytesSent | 上载流量 |
通信字段 | bytesRecv | 下载流量 |
通信字段 | CdiskFree | C盘剩余空间 |
通信字段 | memPercent | 内存利用率 |
通信字段 | memTotal | 内存总量 |
通信字段 | memUsed | 已用内存 |

#### 代码

 - 请填好自己的WIFI账户密码及DevelopKey

```python
import ctypes
import os
import platform
import time

import psutil
from CoolEIoT import CoolE

iot = CoolE("{developkey}")

def get_free_space_mb(folder="C:"):
    if platform.system() == "Windows":
        free_bytes = ctypes.c_ulonglong(0)
        ctypes.windll.kernel32.GetDiskFreeSpaceExW(
            ctypes.c_wchar_p(folder), None, None, ctypes.pointer(free_bytes)
        )
        return round(free_bytes.value / 1024 / 1024 / 1024, 2)
    else:
        st = os.statvfs(folder)
        return round(st.f_bavail * st.f_frsize / 1024 / 1024, 2)


def updateData():
    iot.updateReport("cpuCount", psutil.cpu_count())
    iot.updateReport("cpuPercent", psutil.cpu_percent())
    iot.updateReport(
        "sysBootTime", time.strftime("%m/%d-%H:%M", time.localtime(psutil.boot_time()))
    )
    iot.updateReport("CdiskFree", get_free_space_mb())
    iot.updateReport("bytesSent", round(psutil.net_io_counters()[0] / 1024 / 1024, 2))
    iot.updateReport("bytesRecv", round(psutil.net_io_counters()[1] / 1024 / 1024, 2))
    iot.updateReport("memPercent", psutil.virtual_memory()[2])
    iot.updateReport(
        "memTotal", round(psutil.virtual_memory()[0] / 1024 / 1024 / 1024, 2)
    )
    iot.updateReport(
        "memUsed", round(psutil.virtual_memory()[3] / 1024 / 1024 / 1024, 2)
    )


def handleGet():
    """接收GET指令并更新通信字段数据"""
    get = iot.decode(iot.recv, "GET")
    if get == "__all__":
        updateData()
        iot.publish()
    elif get == "cpuCount":
        iot.updateReport("cpuCount", psutil.cpu_count())
        iot.publish("cpuCount")
    elif get == "cpuPercent":
        iot.updateReport("cpuPercent", psutil.cpu_percent())
        iot.publish("cpuPercent")
    elif get == "sysBootTime":
        iot.updateReport(
            "sysBootTime",
            time.strftime("%m/%d-%H:%M", time.localtime(psutil.boot_time())),
        )
        iot.publish("sysBootTime")
    elif get == "bytesSent":
        iot.updateReport(
            "bytesSent", round(psutil.net_io_counters()[0] / 1024 / 1024, 2)
        )
        iot.publish("bytesSent")
    elif get == "bytesRecv":
        iot.updateReport(
            "bytesRecv", round(psutil.net_io_counters()[1] / 1024 / 1024, 2)
        )
        iot.publish("bytesRecv")
    elif get == "CdiskFree":
        iot.updateReport("CdiskFree", get_free_space_mb())
        iot.publish("CdiskFree")
    elif get == "memPercent":
        iot.updateReport("memPercent", psutil.virtual_memory()[2])
        iot.publish("memPercent")
    elif get == "memTotal":
        iot.updateReport(
            "memTotal", round(psutil.virtual_memory()[0] / 1024 / 1024 / 1024, 2)
        )
        iot.publish("memTotal")
    elif get == "memUsed":
        iot.updateReport(
            "memUsed", round(psutil.virtual_memory()[3] / 1024 / 1024 / 1024, 2)
        )
        iot.publish("memUsed")


def handleCommand():
    """接收命令字段数据"""
    command = iot.decode(iot.recv, "COMMAND")
    if command == "shutdown":
        os.system("shutdown -s -t 3")
    elif command == "reboot":
        os.system("shutdown -r -t 3")


def main():
    iot.setDebug(True)
    iot.start()
    try:
        while True:
            iot.loop()
            handleGet()
            handleCommand()
            time.sleep(0.1)
    except KeyboardInterrupt:
        iot.stop()


if __name__ == "__main__":
    main()
```

---

### 点灯(树莓派)

整理开发中

## MicroPython

### 测试例程

本测试例程不要求任何字段，开始前请务必确认将SDK文件上传至开发板

#### 代码

 - 请填好自己的WIFI账户密码及DevelopKey

```python
from iot.CoolEIoT import CoolE
import utime as time

ssid = "{wifi_ssid}"
pswd = "{wifi_pswd}"
iot = CoolE("{developkey}")

get_count = 0
command_count = 0

def handleGet():
    global get_count
    get = iot.decode(iot.recv,"GET")
    if get is not None:
        if get != '__all__':
            get_count+=1
            iot.updateReport(get,get_count)
            iot.publish(get)
            print("GET: " + get)
        else:
            iot.publish()

def handleCommand():
    global command_count
    command = iot.decode(iot.recv,"COMMAND")
    if command is not None:
        command_count+=1
        iot.updateReport(command,command_count)
        iot.publish(command)
        print("COMMAND: " + command)

def handleContent():
    content = iot.decode(iot.recv,"CONTENT")
    if content is not None:
        print("CONTENT_NAME: " + content['n'] + ", CONTENT_DATA: " + content['d'])

def main():
    iot.setDebug(True)
    iot.init(ssid,pswd)
    iot.connect()
    try:
        while True:
            iot.loop()
            handleGet()
            handleCommand()
            handleContent()
            time.sleep_ms(100)
    except KeyboardInterrupt:
        iot.stop()

if __name__ == '__main__':
    main()
```

---

### 温湿度及显示

整理开发中