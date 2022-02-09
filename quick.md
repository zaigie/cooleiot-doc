## 使用APP

- [下载APK文件](https://www.cooleiot.tech/app/cooleiot-220.apk)
- [访问H5版本](https://www.cooleiot.tech)

> 酷易物联面向的用户群体中**非专业工程师**更多，故在设计之初便将📱APP设计地十分简洁易懂。  
作为用户的您在使用APP的过程中，很容易知道哪个UI是执行什么操作😊。  
所以在我们的文档中几乎是**不需要向您演示APP等界面操作截图**的。  
当然，关于具体的概念和功能说明，您还是需要先看下方的文档才能了解，在这里祝您使用愉快！

## 使用小程序

![](static/img/quick/miniprogram.png ':size=80%')

酷易物联的小程序并非将APP功能复刻，小程序**只提供设备I/O界面的功能。**  
所以，小程序诞生的目的便是**方便用户将开发好的设备分享给家人、朋友...**  
在APP的**设备详情页**即可设置开启或关闭设备分享，开启后会在“**设备-开放能力**”里提供一个密钥  
将该密钥分享给他人，填入小程序首页就可以啦！~

!> 设备开发人可以自由控制分享功能的开启，当分享关闭时，即使他人拥有该密钥，也无法进入到I/O界面;同样，当分享出去的设备实际已被删除时，也无法进入。

## 准备工作

### Arduino

#### （1）安装ArduinoIDE
- [ArduinoIDE下载安装教程](https://www.arduino.cn/thread-5838-1-1.html)
- [Arduino驱动安装方法](https://www.arduino.cn/thread-1008-1-1.html)

#### （2）安装ESP8266/ESP32开发板环境
- [ESP8266开发环境](https://www.arduino.cn/thread-76029-1-1.html "ESP8266开发环境")
- [ESP32开发环境](https://www.arduino.cn/thread-81194-1-1.html "ESP32开发环境")

#### （3）安装ArduinoSDK及必备库
- [ArduinoIDE使用库管理器添加引用库](https://www.arduino.cn/thread-17883-1-1.html "ArduinoIDE使用库管理器添加引用库")

> **必备库**  
1.ArduinoJSON(**v6.18.5**及以上)  
2.PubSubClient  
3.CoolE库及依赖库

**⭐CoolE库下载地址：[https://www.cooleiot.tech/source/libraries.zip](https://www.cooleiot.tech/source/libraries.zip)**

!> 打开ArduinoIDE，顶部菜单选择`项目—加载库—添加.ZIP库`，选择到**libraries.zip**即可
<!-- 2.将下载后的**libraries.zip**文件解压放入 `文档/Arduino/libraries` 中！ -->

![](static/img/quick/arduinoide.png ':size=80%')

 - [ArdinoSDK点灯例程](/demo?id=点灯)

 - [ArdinoSDK温湿度及显示例程](/demo?id=温湿度及显示)

?> ⭐推荐使用Visual Studio Code的[platformIO IDE](https://platformio.org/)插件进行Arduino开发  
对您来说，只需要将压缩包中的CoolE文件夹放在pio项目的lib目录中即可。

### Python3

#### （1）安装Python3(3.6.8及以上)

 - [Python3.9安装及环境配置](https://blog.csdn.net/qq_43146264/article/details/108943465)

#### （2）安装PythonSDK


在终端运行：

```bash
pip install cooleiot
```


接下来就可以进行物联开发了

 - [PythonSDK最简单测试例程](/demo?id=测试例程)

### MicroPython

在进行MPY开发前，您需要知道，目前用来开发MPY的IDE或工具较多，请在对MPY开发有一定了解并熟悉至少一种开发工具后再尝试使用MicroPythonSDK。  
若您使用的是ESP8266/ESP32，在没有技能或框架的限制时，依然建议您优先选择ArduinoSDK。  
**若您使用的非ESP系列的芯片，而是如K210等，使用MaixPy3框架的开发板，将暂时无法使用本SDK，但后续将进行适配**

#### （1）下载SDK文件

 - [CoolEIoT MicroPython SDK](https://www.cooleiot.tech/source/iot.zip)

#### （2）将文件解压并传输到开发板上

该过程有很多开发工具能够实现，如：

 - （推荐）[uPyCraft](https://blog.csdn.net/weixin_45020839/article/details/105807767)
 - [uPyLoader](https://github.com/BetaRavener/uPyLoader)
 - （推荐）[VSCode：RT-Thread MicroPython](https://marketplace.visualstudio.com/items?itemName=RT-Thread.rt-thread-micropython)


各工具详细的使用方法在网络上均丰富。

```tree
:
├─main_example.py
│
└─iot:
     ├─ CoolEIoT.mpy
     ├─ errno.mpy
     ├─ robust2.mpy
     └─ simple2.mpy
```

接下来就可以进行物联开发了

 - [MicroPythonSDK最简单测试例程](/demo?id=测试例程-1)
