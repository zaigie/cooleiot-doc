
?> HTTPAPI的MQTT接口基于API密钥鉴权  
设备相关接口基于developkey鉴权，所以，请务必将两种密钥保管好

## 接口测试

- [在线测试工具](http://runapi.showdoc.cc/#/ "在线测试接口")
- 【国内工具】[ApiPost](https://www.apipost.cn/)
- 【国外工具】[Postman](https://www.postman.com/downloads/)

## MQTT

### 发布消息

```http
POST https://api.cooleiot.tech/mqtt/publish?key={apikey}
```

#### 参数

|参数名|必需|位置|说明|
|:----    |:---|:----- |-----   |
|key |是  |params |设备的API密钥   |
|payload |否  |bodyData | 发布内容    |
|command     |否  |bodyData | 发布命令   |
|delay     |否  |bodyData | 发布延时(单位:秒)    |

?> payload可填任意内容（数字、字符串、JSON等），但仍然建议为JSON格式<br />
当bodyData中同时存在payload和command时，payload会被忽略<br />
delay参数默认为0，若您想要在请求接口后延时3秒发布消息，请填入3

#### 返回数据

```json
{
    "code": 1000,
    "msg": "设备消息发布成功！",
    "data": {
        "code": 0
    }
}
```

---

### 运行场景

```http
POST https://api.cooleiot.tech/mqtt/runscene?key={apikey}
```

#### 参数

|参数名|必需|位置|说明|
|:----    |:---|:----- |-----   |
|key |是  |params |场景的API密钥   |


#### 返回数据

```json
{
    "code": 1000,
    "msg": "运行场景动作成功！",
    "data": {
        "data": [],
        "code": 0
    }
}
```

## 设备

### 设备详情

```http
GET https://api.cooleiot.tech/device/develop/{developkey}
```

|参数名|必需|位置|说明|
|:----    |:---|:----- |-----   |
|developkey |是  |path |设备的DevelopKey   |

#### 返回数据

```json
{
    "code": 1000,
    "msg": "获取设备详情成功！",
    "data": {
        "device_id": "D0CasoDY",
        "username": "zaigie",
        "device_name": "sdk测试",
        "share": false,
        "status": 0,
        "last_online": "2021-10-21T15:27:05.317405+08:00",
        "last_offline": "2021-10-21T15:30:04.068230+08:00",
        "created": "2021-10-16T19:46:38.515923+08:00"
    }
}
```

---

### 云配置列表

```http
GET https://api.cooleiot.tech/device/develop/{developkey}/configs
```

|参数名|必需|位置|说明|
|:----    |:---|:----- |-----   |
|developkey |是  |path |设备的DevelopKey   |

#### 返回数据

```json
{
    "code": 1000,
    "msg": "获取云配置成功！",
    "data": [
        {
            "device": "DyZhXFA8",
            "config_name": "amapKey",
            "config_alias": "高德Key",
            "type": 1,
            "config_content": {
                "data": "asdfghjklzxcvbnmqwertyuiop",
                "type": "string"
            }
        },
        {
            "device": "DyZhXFA8",
            "config_name": "citycode",
            "config_alias": "城市代码",
            "type": 2,
            "config_content": {
                "data": 114514,
                "type": "number"
            }
        }
    ]
}
```

---

### 云配置详情

```http
GET https://api.cooleiot.tech/device/develop/{developkey}/config/{config_name}
```

|参数名|必需|位置|说明|
|:----    |:---|:----- |-----   |
|developkey |是  |path |设备的DevelopKey   |
|config_name |是  |path |获取的云配置名称   |

#### 返回数据

```json
{
    "code": 1000,
    "msg": "获取amapKey配置成功！",
    "data": {
        "device": "DyZhXFA8",
        "config_name": "amapKey",
        "config_alias": "高德Key",
        "type": 1,
        "config_content": {
            "data": "jsfbnfudfesisa316754",
            "type": "string"
        }
    }
}
```

---

### 字段列表

```http
GET https://api.cooleiot.tech/device/develop/{developkey}/fields
```

|参数名|必需|位置|说明|
|:----    |:---|:----- |-----   |
|developkey |是  |path |设备的DevelopKey   |

#### 返回数据

```json
{
    "code": 1000,
    "msg": "获取设备字段成功！",
    "data": [
        {
            "device": "D0CasoDY",
            "field_name": "open",
            "field_alias": "开板载灯",
            "field_unit": "",
            "show_gateway": false,
            "weight": 2,
            "type": 1,
            "icon": "lightbulb-line",
            "icon_color": "#00cc00"
        },
        {
            "device": "D0CasoDY",
            "field_name": "close",
            "field_alias": "关板载灯",
            "field_unit": "",
            "show_gateway": false,
            "weight": 1,
            "type": 1,
            "icon": "shut-down-line",
            "icon_color": "#ff0033"
        },
        {
            "device": "D0CasoDY",
            "field_name": "ledsta",
            "field_alias": "灯状态",
            "field_unit": "",
            "show_gateway": false,
            "weight": 1,
            "type": 2,
            "icon": "lightbulb-flash-line",
            "icon_color": "#336600"
        }
    ]
}
```