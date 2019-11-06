# 快速开始

假设你已经成功创建地图并绘制完成路网信息。下面的内容可以帮助你快速开始使用API控制调度系统。

所有API的URL都以`/api/v1`开头下面的表述中将省略此前缀。调度服务器默认端口号为24958

## 启动调度系统

不建议用API进行此操作。最好调度系统一直保持运行状态。
URL: /start

请求方式: GET

说明：启动调度系统，并使用对应的地图和路线数据。如果机器人没有采用当前设置的数据则自动同步和切换机器人地图数据，并启动机器人至目标地图的导航状态。

请求参数:

|参数|类型|说明|
|--|--|--|
|map_id|string|调度系统所采用的地图id|
|path_id|string|调度系统所采用的path_id|

返回参数
|参数|类型|说明|
|--|--|--|
|layout|object|成功返回的layout数据|

例子

```python
import requests
requests.get("http://192.168.0.128:24958/api/v1/start?map_id=7ac96843-d696-40ba-9807-0d4036d6824f&path_id=9613ce18-5fb8-4eab-8d37-2bb2724b790a")
```

其中一长串字符串为启动的目标地图和路径的ID，具体的地图和路径ID可以通过`http://192.168.0.128:24958/api/v1/layout/map`这个URL获取。

## 关闭调度系统

不建议用API进行此操作。最好调度系统一直保持运行状态。

URL: /stop

请求方式：GET

说明：停止调度系统，并停止当前所有任务，关闭机器人导航状态

请求参数：无

返回参数：

|参数|类型|说明
|--|--|--|
|status|string|关闭是否成功|

例子

```python
import requests
requests.get("http://192.168.0.128:24958/api/v1/stop")
```

## 发布调度任务

URL: /schedule
请求方式： POST
请求参数
|参数|类型|说明|
|--|--|--|
|location_id|string|机器人移动目标点ID，location_id和location参数只用设置其中一个。充电和待机任务不用设置。|
|location|Object,包含 x,y,theta|机器人移动目标点坐标，location_id和location参数只用设置其中一个。充电和待机任务不用设置。|
|task_id|string|机器人到达目标点后需要执行的任务id|
|priority|int|任务优先级。默认优先级如下回归待机点任务 优先级 0, 充电任务且当前机器人电量较高 优先级1, 一般移动任务 优先级2, 用户交互任务 优先级3, 低电量充电任务 优先级4|
|robot_id|string|可选参数，当指定此参数时，只有指定的机器人能够接此任务|
|callback_url|string|任务状态回调，当任务状态发生变化时服务会对对应url发送post请求，内容为schedule状态数据|

返回参数

|参数|类型|说明|
|--|--|--|
|schedule_id|string|调度任务id|
|schedule_status|string|调度任务状态|

调用例子

```python
import requests
# 移动到id为3b28c344-35b2-41c6-83e9-6449ebe886a0的点
requests.post("http://192.168.0.128:24958/api/v1/schedule", json={
    "location_id":"3b28c344-35b2-41c6-83e9-6449ebe886a0",
    "priority":2
})
# 移动到坐标为(2.7, 5.3)的目标点，同时角度为45°
requests.post("http://192.168.0.128:24958/api/v1/schedule", json={
    "location": {
        "x":2.7,
        "y":5.3,
        "theta":45
    },
    "priority":2
})
```

具体的点的ID和坐标值可以从调度客户端里面获取。可以手动发一个调度任务，然后查看目标点坐标。

## 获取调度任务状态

URL：/schedule

请求方式：GET

请求参数

|参数|类型|说明|
|--|--|--|
|schedule_id|string|调度任务ID|

返回值

|参数|类型|说明|
|--|--|--|
|schedule|object|schedule对象|

请求例子

```python
import requests
requests.get("http://192.168.0.128:24958/api/v1/schedule?id=56d8e596-db41-4bf0-b9a5-03a7e6a1dd1d")
# 返回参数例子

"""
{
  "create_time": 1573043104080,
  "start_time": 0,
  "end_time": 0,
  "id": "56d8e596-db41-4bf0-b9a5-03a7e6a1dd1d",
  "destination": {
    "x": 2.7591533837666145,
    "y": 5.355131029783591,
    "theta": 45
  },
  "destination_id": "3b28c344-35b2-41c6-83e9-6449ebe886a0",
  "priority": 2,
  "callback_url": "",
  "robot": null,
  "state": "CANCELLED",
  "task": null,
  "result": ""
}
"""
```

## 暂停，继续，取消调度任务

URL:

取消任务 /schedule/cancel

暂停任务 /schedule/pause

继续任务 /schedule/resume

请求参数

|参数|类型|说明|
|--|--|--|
|id|string|目标调度任务的ID|

返回参数

目标调度任务内容

例子

```python
import requests
# 暂停任务
requests.get("http://192.168.0.128:24958/api/v1/schedule/pause?id=56d8e596-db41-4bf0-b9a5-03a7e6a1dd1d")
# 继续任务
requests.get("http://192.168.0.128:24958/api/v1/schedule/resume?id=56d8e596-db41-4bf0-b9a5-03a7e6a1dd1d")
# 取消任务
requests.get("http://192.168.0.128:24958/api/v1/schedule/cancel?id=56d8e596-db41-4bf0-b9a5-03a7e6a1dd1d")
```

其中的调度任务ID即为创建调度任务时返回的调度ID

## 返回充电

URL: /schedule/go_charge

请求方式

请求方式: GET

说明: 当机器人收到返回充电请求时，机器人会自动选择一个最近的空闲充电桩进行充电。根据电量的高低，充电任务可能会抢占其他低优先级的任务。

请求参数

|参数|类型|说明|
|--|--|--|
|id|string|需要返回充电的机器人|

返回参数

返回充电调度任务数据

例子

```python
import requests
requests.get("http://192.168.0.128:24958/api/v1/schedule/go_charge?id=01E1FCAEC7F4A53451E58261EB3DE9A7BDC8C70E367C7102E081F1A65F6970600896D07A95EB")

"""
返回数据例子
{
    "create_time": 1573045014873,
    "start_time": 0,
    "end_time": 0,
    "id": "8288c2c4-b2e5-4661-b38a-a73e88455c0a",
    "destination": {
        "x": -2.951997437470733,
        "y": -0.1631757377496385,
        "theta": 90
    },
    "destination_id": "365e8060-76b2-416b-9eb5-7ad537c5aa1a",
    "priority": 0,
    "callback_url": "",
    "robot": {
        "id": "01E1FCAEC7F4A53451E58261EB3DE9A7BDC8C70E367C7102E081F1A65F6970600896D07A95EB",
        "mac": "00:e0:4c:68:00:dc",
        "ip": "192.168.0.158",
        "port": 3546,
        "version": "4.0.0",
        "galileo_status": {
            "mapStatus": 0,
            "controlSpeedX": 0.0,
            "currentSpeedX": 0.0,
            "targetNumID": -1,
            "power": 12.0,
            "navStatus": 1,
            "loopStatus": 0,
            "gbaStatus": 0,
            "targetDistance": -1.0,
            "controlSpeedTheta": 0.0,
            "currentSpeedTheta": 0.0,
            "header": {
                "stamp": {
                    "secs": 1573045014,
                    "nsecs": 810050964
                },
                "frame_id": "map",
                "seq": 1039738
            },
            "busyStatus": 0,
            "chargeStatus": 0,
            "currentAngle": 0.9793111085891724,
            "currentPosX": 1.5362364053726196,
            "currentPosY": 3.550684690475464,
            "gcStatus": 0,
            "angleGoalStatus": 1,
            "visualStatus": 0,
            "targetStatus": 0
        },
        "is_enabled": true,
        "is_online": true,
        "server_url": "http://192.168.0.158:3546/api/v1/",
        "current_schedule": null,
        "last_update_time": 1573045014864,
        "name": ""
    },
    "state": "EXECUTING",
    "task": null,
    "result": ""
}
"""
```

机器人的电压信息和ID信息可以通过`/api/v1/robot/status`接口获取

## 返回待机

URL: /schedule/go_rest

请求方式: GET

说明: 机器人收到返回待机的任务请求后，机器人会自动选择离自己最近的一个空闲待机点返回待机。

请求参数

|参数|类型|说明|
|--|--|--|
|id|string|需要返回待机的机器人|

返回待机调度任务数据

例子

```python
import requests
requests.get("http://192.168.0.128:24958/api/v1/schedule/go_rest?id=01E1FCAEC7F4A53451E58261EB3DE9A7BDC8C70E367C7102E081F1A65F6970600896D07A95EB")

"""
返回数据例子
{
    "create_time": 1573045014873,
    "start_time": 0,
    "end_time": 0,
    "id": "8288c2c4-b2e5-4661-b38a-a73e88455c0a",
    "destination": {
        "x": -2.951997437470733,
        "y": -0.1631757377496385,
        "theta": 90
    },
    "destination_id": "365e8060-76b2-416b-9eb5-7ad537c5aa1a",
    "priority": 0,
    "callback_url": "",
    "robot": {
        "id": "01E1FCAEC7F4A53451E58261EB3DE9A7BDC8C70E367C7102E081F1A65F6970600896D07A95EB",
        "mac": "00:e0:4c:68:00:dc",
        "ip": "192.168.0.158",
        "port": 3546,
        "version": "4.0.0",
        "galileo_status": {
            "mapStatus": 0,
            "controlSpeedX": 0.0,
            "currentSpeedX": 0.0,
            "targetNumID": -1,
            "power": 12.0,
            "navStatus": 1,
            "loopStatus": 0,
            "gbaStatus": 0,
            "targetDistance": -1.0,
            "controlSpeedTheta": 0.0,
            "currentSpeedTheta": 0.0,
            "header": {
                "stamp": {
                    "secs": 1573045014,
                    "nsecs": 810050964
                },
                "frame_id": "map",
                "seq": 1039738
            },
            "busyStatus": 0,
            "chargeStatus": 0,
            "currentAngle": 0.9793111085891724,
            "currentPosX": 1.5362364053726196,
            "currentPosY": 3.550684690475464,
            "gcStatus": 0,
            "angleGoalStatus": 1,
            "visualStatus": 0,
            "targetStatus": 0
        },
        "is_enabled": true,
        "is_online": true,
        "server_url": "http://192.168.0.158:3546/api/v1/",
        "current_schedule": null,
        "last_update_time": 1573045014864,
        "name": ""
    },
    "state": "EXECUTING",
    "task": null,
    "result": ""
}
"""
```
