印娃终端接口文档
--------

### 命令
- `event` 事件指令
- `data`  事件详细信息(字符串格式. 注意, 原始格式必须为字符串, 不能为json对象, 若需要传递json对象需要二次序列化)
```
init               初始化
recv               接收消息
send               发送消息
printerstatus      文档打印机状态
printing           文档打印
photoprinterstatus 照片打印机状态
photoprinting      照片打印
multi_download     批量下载
```

### 返回
- `errcode` 错误代码
- `message` 详细信息

### 终端维护URL
```json
{"event": "qrcode", "data": ""}
```

- 成功
```json
{
    "event": "qrcode",
    "status": "ok",
    "data": {
        "errcode": 0,
        "message": "ok",
        "data": "https://backend.yinwow.com/terminal/init?token=065f3c67-e591-4a49-9d25-8490be5098f5"
    }
}
```

### 下载文件
- 不同的文件之间需要使用回车`\n`隔开
```json
 {"data":"http://www.baidu.com/test.pdf\nhttp://www.baidu.com/test.pdf","event":"multi_download"}
```
- 失败
```json
{"event":"multi_download","status":"error","data":{"errcode":6005,"message":"网络文件获取失败","data":null}}
```
- 成功
```json
{
    "event": "multi_download",
    "status": "ok",
    "data": {
        "errcode": 0,
        "message": "ok",
        "data": [
            {
                "url": "http://127.0.0.1:1603/test.pdf",
                "path": "F:\\go\\src\\inwow\\tmp\\test.pdf",
                "dir": "F:\\go\\src\\inwow\\tmp",
                "origin_url": "http://127.0.0.1:1603/test.pdf"
            }
        ]
    }
}
```

### 文档打印机状态
```json
{"data":"","event":"printerstatus"}
```

- 返回
    - `connedted` 是否连接
    - `normal` 是否可用
    - `printing` 是否正在打印
    - `status` 当前状态 `0`正常 `1`忙 `2`离线 `3`故障 `4`设备未知
    - `errors` 错误列表, 只有当`normal`为 `false` 的时候前端才会从此列表中取出错误进行展示
        - 格式为int数组
        - 错误列表:
            - 0:  `少纸`
            - 1:  `无纸`
            - 2:  `少粉`
            - 3:  `无粉`
            - 4:  `盖门开启`
            - 5:  `卡纸`
            - 6:  `设备离线`
            - 7:  `需要维护`
            - 8:  `进纸盒缺失`
            - 9:  `出纸盒缺失`
            - 10: `耗材缺失`
            - 11: `出纸盒接近最高容量`
            - 12: `出纸盒已满`
            - 13: `进纸盒已空`
            - 14: `定期保养已超时`
    - `serial` 打印机序列号
    - `paper_printed` 已打印纸张
    - `supplies` 耗材情况 `0`满 `1`少 `2`空 `3`未知
        - `tray` 纸盒(数组, 分别代表纸盒1 - 3)
            - 纸盒1
            - 纸盒2
            - 纸盒3
        - `toner` 粉盒
        - `drum` 感光鼓
        - `fixing` 维护套件（定影）
```json
{
    "event": "printerstatus",
    "status": "ok",
    "data": {
        "errcode": 0,
        "message": "ok",
        "data": {
            "connected": true,
            "normal": false,
            "printing": false,
            "status": 3,
            "errors": [0, 2, 8],
            "serial": "40636C6601XCH",
            "paper_printed": 796,
            "supplies": {
                "tray": [3, 0, 0],
                "toner": 0,
                "drum": 0,
                "fixing": 0
            }
        }
    }
}
```
### 打印文档
```json
{
    "data": "[{\"file\":\"F:\\\\go\\\\src\\\\inwow\\\\testfiles\\\\1.pdf\",\"copies\":1,\"copies\":1,\"duplex\":1,\"from\":1,\"to\":2}]",
    "event": "printing"
}
```

- 返回
```json
{
    "event": "printing",
    "status": "ok",
    "data": {
        "errcode": 0,
        "message": "ok",
        "data": {
            "connected": true,
            "normal": true,
            "printing": false,
            "status": 0,
            "serial": "40636C6601XCH",
            "paper_printed": 678,
            "supplies": {
                "tray": [3, 0, 0],
                "toner": 0,
                "drum": 0,
                "fixing": 0
            }
        }
    }
}
```

### 照片打印机状态
```json
{"data":"","event":"photoprinterstatus"}
```

- 返回
     - `connedted` 是否连接
     - `normal` 是否可用
     - `status` 当前状态 `0`正常 `1`忙 `2`离线 `3`故障 `4`设备未知
     - `errors` 当前错误状态
         - 格式为int数组
         - 查看`附录1照片打印机错误列表`
         - 列表
            - `0` 无错误
            - `1` 未知错误
            - `100` 打印机未找到
            - `101` 打印机未响应
            - `102` 打印机无法接收数据
            - `104` 无法打开端口
            - `106` 命令无效
            - `201` 错误的DLL执行方法
            - `202` DLL调用无法预置内存地址
            - `203` 搜索打印机超时
            - `1000` 参数错误
            - `1001` 墨盒丢失
            - `1002` Sheet丢失
            - `1003` 卡纸
            - `1004` 色带堵塞
            - `1006` 相纸和色带不匹配
            - `1008` 相纸非标准尺寸
            - `1011` 温度过高
            - `1013` 前门被打开
            - `1014` 非标准色带
            - `1015` 色带未能检测到
            - `1016` 相纸用完
            - `1017` 无墨
            - `1018` 错误的调用方法
            - `1103` 机械故障
            - `1106` 打印过程中店员被断开
            - `1109` 打印机故障
            - `1111` 废纸盒丢失
            - `1112` 打印过程中前门被打开
            - `1113` 色带错误
            - `1114` 相纸和色带数量不匹配
     - `paper_remain` 剩余打印数量
     - `paper_printed` 已打印数量
     - `serial` 打印机序列号

```json
{
    "event": "photoprinterstatus",
    "status": "ok",
    "data": {
        "errcode": 0,
        "message": "ok",
        "data": {
            "connected": true,
            "normal": true,
            "status": 0,
            "errors": [0],
            "paper_remain": 179,
            "paper_printed": 129,
            "serial": "218699"
        }
    }
}
```

### 打印照片
```json
{
    "data": "[{\"image\":\"F:\\\\go\\\\src\\\\inwow\\\\testfiles\\\\DSC01325.JPG\",\"copies\":1}]",
    "event": "photoprinting"
}
```

- 返回
```json
{
    "event": "photoprinting",
    "status": "ok",
    "data": {
        "errcode": 0,
        "message": "ok",
        "data": null
    }
}
```

### 附录1照片打印机错误列表
[下载](Errors.pdf)


