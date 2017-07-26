---
translated_page: https://github.com/PX4/Devguide/blob/master/en/flight_controller/intel_aero.md
translated_sha: 95b39d747851dd01c1fe5d36b24e59ec865e323e
---

# Intel® Aero Ready to Fly Drone

The Intel® Aero Ready to Fly Drone 是一个无人机开发平台。它搭载了在四核CPU上运行Linux的 [Intel Aero](https://software.intel.com/en-us/aero/dev-kit) 计算板。还搭载了与Intel Aero相连的，在NuttX下运行PX4的STM32微控制器。它们和视觉配件包一起集成在 [Intel® Aero Ready to Fly Drone](https://software.intel.com/en-us/aero/drone-dev-kit) 上, 


![](../../assets/hardware/hardware-intel-aero-rtf.jpg)

## 介绍

主要文档在[官方wiki](https://github.com/intel-aero/meta-intel-aero/wiki)上。介绍了如何设置，更新，和连接开发板。由于部分指令与历史版本不同，故更新到最新版本是有必要的。

你可以通过连接开发板并运行以下命令来查看BIOS和分发版本：

```
get_aero_version.py
```

以下是特定版本的实际测试指令的输出:

```C
BIOS_VERSION = Aero-01.00.12_Prod
OS_VERSION = v01.00.04
```

官方文档也解释了如何在Linux下开发，此时这些指令用于从版本树上更新微控制器的固件。

## 烧写

在设置好PX4开发环境后，以下步骤用于更新PX4软件：

1. 对Aero上的所有软件进行更新 (https://github.com/intel-aero/meta-intel-aero/wiki/Upgrade-To-Latest-Software-Release)

2. 升级 [固件](https://github.com/PX4/Firmware)

3. 用指令 `make aerofc-v1_default` 进行编译

4. 配置目标宿主名

   ​

如果你的系统不用连接本地名字，可以跳过这一步。你可以在用WiFi或USB与开发板相连后，用ssh连接intel-aero.local来进行测试：

```
ssh root@intel-aero.local
```

如果以上指令没用，你可以试着上传以下脚本来给出具体IP：

```
export AERO_HOSTNAME=192.168.1.1`
```

5. 升级  `make aerofc-v1_default upload`


## 通过网络连接QGroundControl

1. 确认你通过WIFI或USB连接上了开发板。

2. 用ssh连接到开发板并确认mavlink已在运行。mavlink是默认开机启动的，可用以下指令手动启动:
```
/etc/init.d/mavlink-routerd.sh start
```

3. 启动 QGroundControl，它应该自动连接.

4. 如果不启动 QGroundControl, 你可以用以下指令打开 [NuttX shell](../debug/system_console.md#mavlink-shell) :
```
./Tools/mavlink_shell.py 0.0.0.0:14550
```

## 连接 Lidar Lite 测距仪

以下说明用于通过I2C接口连接Lidar Lite V3。Aero上的I2C接口（标有compass）被用于额外的磁力计（GPS的一部分）。因此需要I2C分线器来连接Lidar Lite (见图).

![](../../assets/hardware/Aero_I2C_splitter.JPG)

 Lidar Lite V3 引脚图如下：

| pin  | Aerofc I2C | Lidar Lite V3    |
| ---- | ---------- | ---------------- |
| 1    | VCC        | VCC              |
| 2    | SCL        | - (Power enable) |
| 3    | SDA        | - (Mode control) |
| 4    | GND        | SCL              |
| 5    | -          | SDA              |
| 6    | -          | GND              |

![](../../assets/hardware/Aero_LidarLite.JPG)
