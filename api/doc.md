# API文档

## 调度系统整体工作流程

用户向调度系统发起任务请求。任务包含移动机器人到某位置并完成某操作。
调度系统根据当前机器人资源分配任务至机器人
机器人根据任务计算出所需占用的道路，并将道路占用更新至调度系统

## 数据结构

### 地图数据结构

```javascript
{
    points : [
        {
            type: REST_POINT, // 可选类型 NORMAL_POINT, CHARGE_POINT, REST_POINT, TRAFFIC_POINT, TARGET_POINT
            location: {
                x: 0.0, // 机器人坐标X, 单位为米
                y: 0.0, // 机器人坐标Y, 单位为米
                theta: 0.0, // 机器人坐标角度，单位为度
            },
            name: "", // 点名称，可以为空
            id: "xxx-xxx-xxx", // 随机生成的字符串，点的唯一标识
            status: FREE, // 当前点状态，可选值为 FREE, BLOCK
            radius: 0.5, // 点半径大小
            tolerance: {
                xy: 0.1, // 坐标容差
                theta: 0.1 // 角度容差
            }
        }
    ],
    ways: [
        {
            id: "xxx-xxx-xxx", // 随机生成的字符串，线段的唯一标识
            points: ["xxxx-xxx-xxx", "xxx-xxx-xxx"], // 线段起点和终点id
            status: "FORWARD", // 当前路线状态，可选值为 FORWARD, BACKWARD, FREE, BLOCK
        }
    ],
}
```

### 调度任务状态

`ERROR` 任务出错

`DISPATCHING` 任务分配中，尚未分配到机器人

`EXECUTING` 任务执行中

`PAUSED` 任务暂停

`CANCELLED` 任务取消

`COMPLETE` 任务完成

## API 说明

### 调度中心控制接口

#### 启动调度系统

URL: `/start`

请求方式: `GET`

说明：启动调度系统，并使用对应的地图和路线数据。如果机器人没有采用当前设置的数据则自动同步和切换机器人地图数据，并启动机器人至目标地图的导航状态。

请求参数：

|参数|类型|说明|
|--|--|--|
|map_id|string|调度系统所采用的地图id|
|path_id|string|调度系统所采用的path_id|

返回参数:

|参数|类型|说明|
|--|--|--|
|layout|Object|启动成功后返回layout数据|

示例layout数据

```json
{
    "status": "ok",
    "layout": {
        "name": "path5",
        "points": [
            {
                "type": "TRAFFIC_POINT",
                "location": {
                    "x": -7.0625,
                    "y": -15.5625,
                    "theta": 0
                },
                "name": "",
                "id": "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                "status": "FREE",
                "radius": 0.5,
                "tolerance": {
                    "xy": 0.1,
                    "theta": 0.1
                }
            },
            ...
        ],
        "ways": [
            {
                "id": "b4a0cdaa-5f52-4191-9340-36d19a1cb0a6",
                "points": [
                    "29d8aea0-ea06-4b61-9b73-cea4ee6833a5",
                    "d8db564c-dede-4bfc-8456-a7dfee9e66bb"
                ],
                "status": "FREE",
                "robots": []
            }
            ...
        ]
    },
    "map": {
        "_id": {
            "$oid": "5dd34397869f0667d15799e1"
        },
        "id": "04f777b8-ff9d-4303-87ac-334dab2e0ffe",
        "name": "largemap",
        "create_time": 1574126487670,
        "origin": {
            "x": -29.764,
            "y": -33.38139
        },
        "image_width": 1366,
        "image_height": 778,
        "resolution": 0.05,
        "image_png": "/media/map/map_ET4FjhJ.png",
        "image_pgm": "/media/map/map_ET4FjhJ.pgm",
        "keyframedb_bson": "/media/map/keyframedb_hHK57gx.bson",
        "keyframes_bson": "/media/map/keyframes_FnNLIte.bson",
        "map_bson": "/media/map/map_T2J2lrF.bson",
        "mappoints_bson": "/media/map/mappoints_zFg5qGm.bson",
        "robot_trajectory": "/media/map/RobotTrajectory_4XjHdHI.txt",
        "map_yaml": "/media/map/largemap.yaml",
        "md5sum": "dd0c7a1c2da0194e66bbe8aee3b5d891",
        "paths": [
            {
                "_id": {
                    "$oid": "5dd3442c869f0667d15799e2"
                },
                "map_id": "04f777b8-ff9d-4303-87ac-334dab2e0ffe",
                "create_time": 1574126636820,
                "layout": {
                    "name": "path4",
                    "points": [
                        {
                            "type": "TRAFFIC_POINT",
                            "location": {
                                "x": -7.0625,
                                "y": -15.5625,
                                "theta": 0
                            },
                            "name": "",
                            "id": "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                            "status": "FREE",
                            "radius": 0.5,
                            "tolerance": {
                                "xy": 0.1,
                                "theta": 0.1
                            }
                        },
                        ...
                    ],
                    "ways": [
                        {
                            "id": "10d1d0b3-23d3-4b1d-a16e-0e2f86be6a4d",
                            "points": [
                                "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                                "e84adf2b-4c56-419a-beb0-945b60102772"
                            ],
                            "status": "FREE"
                        },
                        ...
                    ]
                },
                "id": "598c5ed2-cb2f-40de-b806-5c94b8200ccf",
                "name": "path4"
            },
            {
                "_id": {
                    "$oid": "5ddc8b3d65c125fbd33166d9"
                },
                "map_id": "04f777b8-ff9d-4303-87ac-334dab2e0ffe",
                "create_time": 1574734653138,
                "layout": {
                    "name": "path5",
                    "points": [
                        {
                            "type": "TRAFFIC_POINT",
                            "location": {
                                "x": -7.0625,
                                "y": -15.5625,
                                "theta": 0
                            },
                            "name": "",
                            "id": "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                            "status": "FREE",
                            "radius": 0.5,
                            "tolerance": {
                                "xy": 0.1,
                                "theta": 0.1
                            }
                        },
                        ...
                    ],
                    "ways": [
                        {
                            "id": "10d1d0b3-23d3-4b1d-a16e-0e2f86be6a4d",
                            "points": [
                                "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                                "e84adf2b-4c56-419a-beb0-945b60102772"
                            ],
                            "status": "FREE"
                        },
                        ...
                    ]
                },
                "id": "503ab888-ae67-4981-8840-25f48feeeb9f",
                "name": "path5"
            }
        ]
    },
    "path": {
        "_id": {
            "$oid": "5ddc8b3d65c125fbd33166d9"
        },
        "map_id": "04f777b8-ff9d-4303-87ac-334dab2e0ffe",
        "create_time": 1574734653138,
        "layout": {
            "name": "path5",
            "points": [
                {
                    "type": "TRAFFIC_POINT",
                    "location": {
                        "x": -7.0625,
                        "y": -15.5625,
                        "theta": 0
                    },
                    "name": "",
                    "id": "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                    "status": "FREE",
                    "radius": 0.5,
                    "tolerance": {
                        "xy": 0.1,
                        "theta": 0.1
                    }
                },
                ...
            ],
            "ways": [
                {
                    "id": "10d1d0b3-23d3-4b1d-a16e-0e2f86be6a4d",
                    "points": [
                        "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                        "e84adf2b-4c56-419a-beb0-945b60102772"
                    ],
                    "status": "FREE",
                    "robots": []
                },
                ...
            ]
        },
        "id": "503ab888-ae67-4981-8840-25f48feeeb9f",
        "name": "path5"
    },
    "lock_id": "",
    "robot_id": ""
}
```

#### 停止调度系统

URL: `/stop`

请求方式: `GET`

说明：停止调度系统，并停止当前所有任务，关闭机器人导航状态

请求参数：无

返回参数：

|参数|类型|说明|
|--|--|--|
|status|string|关闭是否成功|

### 调度任务相关

#### 发起任务

URL: `/schedule`

请求方式: `POST`

请求参数:

|参数|类型|说明|
|--|--|--|
|location_id|string|机器人移动目标点ID，location_id和location参数只用设置其中一个。充电和待机任务不用设置。|
|location|Object包含x,y,theta|机器人移动目标点坐标，location_id和location参数只用设置其中一个。充电和待机任务不用设置。|
|task_id|string|机器人到达目标点后需要执行的任务id|
|priority|int|任务优先级。默认优先级如下<br>回归待机点任务 优先级 0<br>充电任务且当前机器人电量较高 优先级1<br>一般移动任务 优先级2<br>用户交互任务 优先级3<br>低电量充电任务 优先级4<br>|
|robot_id|string|可选参数，当指定此参数时，只有指定的机器人能够接此任务|
|callback_url|string|任务状态回调，当任务状态发生变化时服务会对对应url发送post请求，内容为schedule状态数据|

返回参数: schedule json 数据对象

示例返回数据:

```json
{
    "create_time": 1576127957717,
    "start_time": 0,
    "end_time": 0,
    "id": "6f32ceeb-1a85-4ef6-9608-246098a4ac70",
    "destination": {
        "x": -5.29884054000141,
        "y": -2.7553970808007335,
        "theta": 0
    },
    "destination_id": "",
    "priority": 2,
    "callback_url": "",
    "robot": {
        "id": "16000CD479EEEFF2B29B8AC54E96F29802E50E724E95834F5935FE999BE8DB2816E01A482211",
        "mac": "00:e0:4c:ce:83:87",
        "ip": "192.168.0.196",
        "port": 3546,
        "version": "4.0.0",
        "galileo_status": {
            "mapStatus": 0,
            "controlSpeedX": 0.0,
            "currentSpeedX": 0.0,
            "targetNumID": -1,
            "power": 38.76279067993164,
            "navStatus": 1,
            "loopStatus": 0,
            "gbaStatus": 0,
            "targetDistance": -1.0,
            "controlSpeedTheta": 0.0,
            "currentSpeedTheta": 0.0006672566523775458,
            "header": {
                "stamp": {
                    "secs": 1576128007,
                    "nsecs": 241101980
                },
                "frame_id": "map",
                "seq": 943
            },
            "busyStatus": 0,
            "chargeStatus": 0,
            "currentAngle": 2.859792709350586,
            "currentPosX": -4.2341084480285645,
            "currentPosY": 0.9780586957931519,
            "gcStatus": 0,
            "angleGoalStatus": 1,
            "visualStatus": 1,
            "targetStatus": 0
        },
        "is_enabled": true,
        "is_online": true,
        "server_url": "http://192.168.0.196:3546/api/v1/",
        "current_schedule": null,
        "last_update_time": 1576127957812,
        "name": ""
    },
    "state": "EXECUTING",
    "task": null,
    "result": ""
}
```

#### 取消任务

URL: `/schedule/cancel`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|调度任务id|

返回： schedule json 数据对象

#### 暂停任务

URL: `/schedule/pause`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|调度任务id|

返回: schedule json 数据对象

#### 继续任务

URL: `/schedule/resume`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|调度任务id|

返回: schedule json 数据对象

#### 返回待机

URL: `/schedule/go_rest`

请求方式: `GET`

说明： 机器人从空闲的待机点中选择直线距离最近的点待机

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|返回待机的机器人ID|

返回参数： 返回待机的调度任务信息

#### 返回充电

URL: `/schedule/go_charge`

请求方式：`GET`

说明：机器人从空闲的充电点钟选择直线距离最近的点充电

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|返回充电机器人ID|

返回参数：返回充电的调度任务信息

### 地图状态更新相关

#### 锁定地图

URL: `/layout/lock`

请求方式: `GET`

请求参数

|参数|类型|说明|
|--|--|--|
|robot_id|string|机器人id|

返回参数

|参数|类型|说明|
|--|--|--|
|lock_id|string|获取的锁的id|
|layout|object|当前地图状态数据|

返回示例数据:

```json
{
    "status": "ok",
    "lock_id": "3d1e6049-750d-483e-aa32-dd7b937373ec",
    "layout": {
        "name": "path5",
        "points": [
            {
                "type": "TRAFFIC_POINT",
                "location": {
                    "x": -7.0625,
                    "y": -15.5625,
                    "theta": 0
                },
                "name": "",
                "id": "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                "status": "FREE",
                "radius": 0.5,
                "tolerance": {
                    "xy": 0.1,
                    "theta": 0.1
                }
            },
            ...
        ],
        "ways": [
            {
                "id": "10d1d0b3-23d3-4b1d-a16e-0e2f86be6a4d",
                "points": [
                    "fb9b5da4-d3f8-46af-9f06-6740d18b1350",
                    "e84adf2b-4c56-419a-beb0-945b60102772"
                ],
                "status": "FREE",
                "robots": []
            },
            ...
        ]
    }
}
```

#### 解锁地图

URL: `/layout/unlock`

请求参数: `GET`

|参数|类型|说明|
|--|--|--|
|lock_id|string|地图锁id，如果id不能对应当前地图锁id，将无法解锁|
|robot_id|string|机器人id，如果id无法对应当前获得锁的机器人id，将无法解锁。|

返回参数

|参数|类型|说明|
|--|--|--|
|layout|object|当前地图状态数据|
|status|string|是否解锁成功|

#### 更新地图

URL: `/layout`

请求方式: `PUT`

请求参数:

|参数|类型|说明|
|--|--|--|
|robot_id|string|当前机器人id|
|lock_id|string|当前机器人获取到的锁id|
|layout|object|更改的地图状态信息。如果有未包含的地图状态，则认为没有改动。|
|unlock|bool|是否自动解锁地图|

返回参数:

|参数|类型|说明|
|--|--|--|
|layout|object|当前地图状态数据|

#### 获取地图

URL: `/layout`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|lock|bool|是否自动锁定地图|
|robot_id|string|当开启自动锁定地图时需要此参数，否则不需要|

返回参数:

|参数|类型|说明|
|--|--|--|
|layout|object|地图状态信息|

#### 获取地图数据

URL: `/layout/map`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|地图id,可以为空。为空则返回当前所有地图数据|

返回参数

|参数|类型|说明|
|--|--|--|
|id|string|地图id，唯一的字符串|
|name|string|地图名称|
|origin|object, 有x,y属性|地图左上角的坐标|
|resolution|float|1像素对应的实际距离|
|image|string|地图图片url，图片格式为png|
|keyframedb.bson|string|地图数据url格式为bson|
|keyframes.bson|string|地图数据url格式为bson|
|map.bson|string|地图数据url格式为bson|
|mappoints.bson|string|地图数据url格式为bson|
|image_pgm|string|地图图片url,格式为pgm|
|image_yaml|string|地图说明文件，格式为yaml|
|image_png|string|地图图片url,格式为png|

#### 上传地图数据

URL: `/layout/map`

请求方式: `POST`

请求参数:

|参数|类型|说明|
|--|--|--|
|name|string|地图名称|
|origin_x|float|地图左上角X坐标|
|origin_y|float|地图左上角Y坐标|
|resolution|float|1像素对应实际的距离|
|image_pgm|file|地图文件数据，图片格式为pgm|
|keyframedb_bson|file|地图数据文件，格式为bson|
|keyframes_bson|file|地图数据文件，格式为bson|
|map_bson|file|地图数据文件，格式为bson|
|mappoints_bson|file|地图数据文件,格式为bson|
|force|bool|遇到重名地图时是否覆盖以前地图。如果为true则覆盖，false则返回错误|

返回参数

|参数|类型|说明|
|--|--|--|
|id|string|地图id，唯一的字符串|
|name|string|地图名称|
|origin|object, 有x,y属性|地图左上角的坐标|
|resolution|float|1像素对应的实际距离|
|image|string|地图图片url，图片格式为png|
|keyframedb.bson|string|地图数据url格式为bson|
|keyframes.bson|string|地图数据url格式为bson|
|map.bson|string|地图数据url格式为bson|
|mappoints.bson|string|地图数据url格式为bson|
|image_pgm|string|地图图片url,格式为pgm|
|image_yaml|string|地图说明文件，格式为yaml|
|image_png|string|地图图片url,格式为png|

#### 删除地图数据

URL: `/layout/map`

请求方式: `DELETE`

请求参数：

|参数|类型|说明|
|--|--|--|
|id|string|地图id|

返回参数

|参数|类型|说明|
|--|--|--|
|status|string|表明是否删除成功|

#### 获取保存的路径数据

URL: `/layout/path`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|Path id，可选参数。若没有此参数则返回所有path信息|

返回参数

|参数|类型|说明|
|--|--|--|
|map_id|string|所属地图id|
|create_time|string|创建时间|
|layout|object|layout数据结构|
|name|string|路径名称|
|id|string|路径id|

#### 上传路径数据

URL: `/layout/path`

请求方式: `POST`

请求参数:

|参数|类型|说明|
|--|--|--|
|name|string|路径名称|
|map_id|string|地图id|
|layout|object|layout数据结构|

返回数据:

|参数|类型|说明|
|--|--|--|
|map_id|string|所属地图id|
|create_time|string|创建时间|
|layout|object|layout数据结构|
|name|string|路径名称|
|id|string|路径id|

#### 修改路径数据

URL: `/layout/path`

请求方式: `PUT`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|path id，唯一的字符串|
|map_id|string|所属地图的id|
|layout|object|layout数据结构|
|name|string|路径名称|

返回参数

|参数|类型|说明|
|--|--|--|
|map_id|string|所属地图id|
|create_time|string|创建时间|
|layout|object|layout数据结构|
|name|string|路径名称|
|id|string|路径id|

#### 删除路径数据

URL： `/layout/path`

请求方法: `DELETE`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|path id, 唯一字符串|

### 机器人状态相关

#### 获取机器人状态1

URL: `/robot`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|robot_id|string|机器人id，当此参数未指定时返回所有机器人状态。当指定时返回指定的机器人状态|

返回参数：

|参数|类型|说明|
|--|--|--|
|status|string|是否成功获取状态|
|robot|object|机器人状态信息|

返回示例信息

```json
{
    "status": "ok",
    "robots": [
        {
            "id": "16000CD479EEEFF2B29B8AC54E96F29802E50E724E95834F5935FE999BE8DB2816E01A482211",
            "mac": "00:e0:4c:ce:83:87",
            "ip": "192.168.0.196",
            "port": 3546,
            "version": "4.0.0",
            "galileo_status": {
                "mapStatus": 0,
                "controlSpeedX": 0.0,
                "currentSpeedX": -1.4528632164001465e-07,
                "targetNumID": -1,
                "power": 38.7115478515625,
                "navStatus": 1,
                "loopStatus": 0,
                "gbaStatus": 0,
                "targetDistance": -1.0,
                "controlSpeedTheta": 0.0,
                "currentSpeedTheta": -0.00039996925625018775,
                "header": {
                    "stamp": {
                        "secs": 1576128308,
                        "nsecs": 894376993
                    },
                    "frame_id": "map",
                    "seq": 9989
                },
                "busyStatus": 0,
                "chargeStatus": 0,
                "currentAngle": -0.04512203484773636,
                "currentPosX": -5.170620441436768,
                "currentPosY": -2.721562385559082,
                "gcStatus": 0,
                "angleGoalStatus": 1,
                "visualStatus": 2,
                "targetStatus": 0
            },
            "is_enabled": true,
            "is_online": true,
            "server_url": "http://192.168.0.196:3546/api/v1/",
            "current_schedule": null,
            "last_update_time": 1576128259367,
            "name": ""
        },
        {
            "id": "66D23E4AC785F675EB3615D0B1C1CA63AF80B6F6B285751CCC4A075B10FF7E2C2BD26B89D75A",
            "mac": "00:e0:4c:13:4f:21",
            "ip": "192.168.0.121",
            "port": 3546,
            "version": "4.0.0",
            "galileo_status": null,
            "is_enabled": false,
            "is_online": false,
            "server_url": "http://192.168.0.121:3546/api/v1/",
            "current_schedule": null,
            "last_update_time": 1576122896046,
            "name": ""
        }
    ]
}
```

#### 修改机器人名称

URL: `/robot`

请求方式: `PUT`

请求参数:

|参数|类型|说明|
|--|--|--|
|name|string|机器人名称|
|id|string|机器人ID|

返回参数: 机器人状态信息

#### 获取机器人状态2

URL: `/robot/status`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|robot_id|string|机器人id，当此参数未指定时返回所有机器人状态。当指定时返回指定的机器人状态|

返回参数

|参数|类型|说明|
|--|--|--|
|robots|list|机器人状态列表|

#### 上传机器人状态

URL: `/robot/status`

请求方式: `POST`

请求参数:

|参数|类型|说明|
|--|--|--|
|robot_id|string|机器人id|
|robot_status|object|机器人状态信息|

返回参数

|参数|类型|说明|
|--|--|--|
|robot_id|string|机器人id|
|robot_status|object|机器人状态信息|

返回参数

|参数|类型|说明|
|--|--|--|
|robot_id|string|机器人id|
|robot_status|object|更新后的机器人状态信息|

#### 使能机器人

URL: `/robot/enable`

请求方式: `PUT`

说明: 使能机器人或禁用机器人。只有使能后的机器人系统才会为其分配任务

|参数|类型|说明|
|--|--|--|
|is_enabled|bool|是否使能机器人|
|robot_id|string|机器人ID|

返回参数

|参数|类型|说明|
|--|--|--|
|robot|object|机器人状态信息|

#### 调用某机器人API

URL: `/robot/< ID >/< API_URL >`

说明：

ID为机器人id，API_URL为对应的调用机器人URL。如获取机器人状态信息可以使用
`http://xxx.xxx.xxx.xxx/AABBCCDD/api/v1/syste/status`
进行访问。其中xxx.xxx.xxx.xxx为调度中心ip。AABBCCDD为机器人id。

请求方式：`GET`，`POST`，`PUT`，`DELETE`

请求参数：和对应机器人API一致

返回参数：和机器人API返回参数一致

### Action 和 Task 相关 API

Action代表机器人执行的一个动作，比如等待5秒，或播放一段语音，或左转30°。当一系列action编排成一组动作的时候就是一个Task。通过action和task相关的API我们可以方便的控制机器人实现我们想要的动作。

#### 支持的Action类型和对应属性

|类型|说明|属性|
|--|--|--|
|callback_action|回调action，执行此action时会向指定的url发送http请求|url:http请求的地址<br/>method:http 请求的方式<br/>data:http请求的数据<br/>|
|sleep_action|等待动作，机器人等待对应时间后执行之后的action|wait_time: 机器人等待时间|
|charge_action|自动充电动作， 只有在机器人在充电桩附近时才可以执行此动作|x: 充电桩x坐标<br>y: 充电桩y坐标<br>theta: 充电桩角度, 单位为弧度<br>robot_id: 需要执行此动作的机器人id|
|local_move_action|局部移动动作。控制机器人小范围移动，适用于精准对接场景|robot_id: 需要执行此动作的机器人id<br>distance: 机器人向前移动距离，当为负值时则机器人后退对应距离<br>angle:机器人转动对应角度，单位为弧度<br>method:定位方式。0使用纯惯性导航定位。1采用激光雷达辅助定位|

#### 获取Action

URL: `/action`

请求方式： `GET`

请求参数：

|参数|类型|说明|
|--|--|--|
|id|string|action id, 唯一字符串。id为可选参数，如果有id则返回目标action信息，如果没有id则返回所有action信息|

返回参数：目标action信息

示例返回数据

```json
{
    "type": "charge_action",
    "id": "45affe13-37de-4041-b592-70db27ec4e38",
    "x": -3.799302718853892,
    "y": 0.9755894865773105,
    "theta": 1.48352986419518,
    "robot": {
        "id": "16000CD479EEEFF2B29B8AC54E96F29802E50E724E95834F5935FE999BE8DB2816E01A482211",
        "mac": "00:e0:4c:ce:83:87",
        "ip": "192.168.0.196",
        "port": 3546,
        "version": "4.0.0",
        "galileo_status": {
            "mapStatus": 0,
            "controlSpeedX": 0,
            "currentSpeedX": -2.3283064365386963e-7,
            "targetNumID": -1,
            "power": 38.631343841552734,
            "navStatus": 1,
            "loopStatus": 0,
            "gbaStatus": 0,
            "targetDistance": 1.2436540126800537,
            "controlSpeedTheta": 0,
            "currentSpeedTheta": 3.206077963113785e-7,
            "header": {
            "stamp": {
                "secs": 1576128895,
                "nsecs": 762253046
            },
            "frame_id": "map",
            "seq": 27595
            },
            "busyStatus": 0,
            "chargeStatus": 0,
            "currentAngle": 2.205643653869629,
            "currentPosX": -5.04252815246582,
            "currentPosY": 1.0082411766052246,
            "gcStatus": 0,
            "angleGoalStatus": 1,
            "visualStatus": 1,
            "targetStatus": 2
        },
        "is_enabled": true,
        "is_online": true,
        "server_url": "http://192.168.0.196:3546/api/v1/",
        "current_schedule": null,
        "last_update_time": 1576128846285,
        "name": ""
    },
    "state": "WAITTING",
    "result": "",
    "progress": 0
}
```

#### 创建Action

URL: `/action`

请求方式： `POST`

请求参数:

|参数|类型|说明|
|--|--|--|
|type|string|action类型, 目前可选的action类型为 callback_action, sleep_action, charge_action, local_move_action|
|task_id|string|action 所属task的id，如果没有task_id则会自动创建一个包含目标action的task|
|其余参数||其余参数根据不同的action类型要求添加|

返回参数：新创建的action信息。如果task_id不存在则返回自动创建的task的信息

***注意对于自动创建的task，系统将至作为临时task不会长期保存在数据库中。当调度系统重启之后对应的task和action都会消失***

#### 修改Action

URL: `/action`

请求方式: `PUT`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|action id|
|其他需要修改的action属性|||

返回参数： 修改后的action对象信息

#### 删除Action

URL: `/action`

请求方式: `DELETE`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|action的id，唯一字符串|

#### 查找Task

URL: `/task`

请求方式：`GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|task id, task唯一字符串。可选参数，如果没有id则返回最后创建的20个task。如果有id则返回目标task信息|

返回参数： task 信息

示例返回信息:

```json
{
  "id": "50d79436-50c6-43da-9117-d569684f6205",
  "name": "navigation task",
  "loop_flag": false,
  "current_task": null,
  "state": "WAITTING",
  "sub_tasks": [
    {
      "type": "charge_action",
      "x": -3.799302718853892,
      "y": 0.9755894865773105,
      "theta": 1.48352986419518,
      "robot": {
        "id": "16000CD479EEEFF2B29B8AC54E96F29802E50E724E95834F5935FE999BE8DB2816E01A482211",
        "mac": "00:e0:4c:ce:83:87",
        "ip": "192.168.0.196",
        "port": 3546,
        "version": "4.0.0",
        "galileo_status": {
          "mapStatus": 0,
          "controlSpeedX": 0,
          "currentSpeedX": -2.0489096641540527e-7,
          "targetNumID": -1,
          "power": 38.68146896362305,
          "navStatus": 1,
          "loopStatus": 0,
          "gbaStatus": 0,
          "targetDistance": 1.0949629545211792,
          "controlSpeedTheta": 0,
          "currentSpeedTheta": -0.0005335922469384968,
          "header": {
            "stamp": {
              "secs": 1576128494,
              "nsecs": 861792087
            },
            "frame_id": "map",
            "seq": 15568
          },
          "busyStatus": 0,
          "chargeStatus": 0,
          "currentAngle": 1.8335497379302979,
          "currentPosX": -4.688186168670654,
          "currentPosY": 0.3361913859844208,
          "gcStatus": 0,
          "angleGoalStatus": 1,
          "visualStatus": 1,
          "targetStatus": 2
        },
        "is_enabled": true,
        "is_online": true,
        "server_url": "http://192.168.0.196:3546/api/v1/",
        "current_schedule": null,
        "last_update_time": 1576128445446,
        "name": ""
      },
      "state": "WAITTING",
      "result": "",
      "progress": 0
    },
    {
      "id": "90575557-f993-4146-96d6-3450d84457ba",
      "type": "simple_action",
      "state": "WAITTING",
      "progress": 0
    }
  ],
  "progress": 0
}
```

#### 创建Task

URL: `/task`

请求方式: `POST`

请求参数

|参数|类型|说明|
|--|--|--|
|name|string|task的名称，可选参数|
|loop_flag|bool|是否自动循环执行,可选参数|
|sub_tasks|list|子任务的id列表，子任务可以是action也可以是task，可选参数|

返回参数：创建后的task信息

#### 修改Task

URL: `/task`

请求方式: `PUT`

请求参数

|参数|类型|说明|
|--|--|--|
|id|string|目标task id，task唯一字符串|
|name|string|task的名称，可选参数|
|loop_flag|bool|是否自动循环执行,可选参数|
|sub_tasks|list|子任务的id列表，子任务可以是action也可以是task，可选参数|

返回参数: 修改后的task信息

#### 删除Task

URL: `/task`

请求方式: `DELETE`

请求参数

|参数|类型|说明|
|--|--|--|
|id|string|目标task id，task唯一字符串|

返回参数

|参数|类型|说明|
|--|--|--|
|status|string|task是否被成功删除|

#### 启动Task

URL: `/task/start`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|task id， task唯一字符串标识|

返回参数: 启动后的task信息

#### 暂停Task

URL: `/task/pause`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|task id， task唯一字符串标识|

返回参数: 暂停后的task信息

#### 继续Task

URL: `/task/resume`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|task id， task唯一字符串标识|

返回参数: 继续后的task信息

#### 停止Task

URL: `/task/stop`

请求方式: `GET`

请求参数:

|参数|类型|说明|
|--|--|--|
|id|string|task id， task唯一字符串标识|

返回参数: 停止后的task信息

### 证书相关

#### 注册证书

URL: `/cert/register`

请求方式: `GET`

请求参数

|参数|类型|说明|
|--|--|--|
|cert_id|string|证书注册序列号|

返回参数

|参数|类型|说明|
|--|--|--|
|result|bool|证书注册结果|

#### 获取注册后的证书

URL: `/cert`

请求方式: `GET`

请求参数： 无

返回参数

|参数|类型|说明|
|--|--|--|
|cert_data|string|注册证书内容|

#### 验证证书

URL: `/cert/check`

请求方式: `GET`

请求参数: 无

返回参数:

|参数|类型|说明|
|--|--|--|
|check_res|bool|证书验证结果|
