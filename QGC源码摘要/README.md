# QGC 与水下机器人源码摘要
## 说明
作者：***A-Zealous***

供队友研究、配置交接和轻量分享的源码选读。按原路径保留代表实现，集中展示：

**手柄 → QGC 映射 → MAVLink 通信 → ArduSub 控制/混控 → 电调与推进器**

视频接收单列；常用连接设置、参数说明和队伍记录模板集中归档。


| 分类 | 研究重点 |
|---|---|
| [01_手柄与操控](01_%E6%89%8B%E6%9F%84%E4%B8%8E%E6%93%8D%E6%8E%A7/README.md) | 手柄采集、校准、轴和按钮映射 |
| [02_通信与参数读写](02_%E9%80%9A%E4%BF%A1%E4%B8%8E%E5%8F%82%E6%95%B0%E8%AF%BB%E5%86%99/README.md) | 串口/UDP/TCP、MAVLink、参数同步、电机测试指令 |
| [03_机器人配置与控制](03_%E6%9C%BA%E5%99%A8%E4%BA%BA%E9%85%8D%E7%BD%AE%E4%B8%8E%E6%8E%A7%E5%88%B6/README.md) | 布局/方向设置、ArduSub 控制模式、推进器混控 |
| [04_视频与图传](04_%E8%A7%86%E9%A2%91%E4%B8%8E%E5%9B%BE%E4%BC%A0/README.md) | 视频源、GStreamer 接收、显示和录像 |
| [05_配置速查](05_%E9%85%8D%E7%BD%AE%E9%80%9F%E6%9F%A5/README.md) | 默认设置、参数 XML、USB 接口规则、队伍记录模板 |

## 使用方式
先顺着 01 → 02 → 03 看操控链，再查 05 整理队伍配置；图传问题看 04。源码摘录供定点阅读；构建与全依赖研究可按清单中的上游仓库和版本定位完整工程。各分类摘要已原文拼接在下方，便于一份文档通读。
至于获取完整代码，请到官方仓库下载↓↓

## 来源与分享
- 地面站：[QGroundControl v3.5.6](https://github.com/mavlink/qgroundcontrol/tree/v3.5.6)。
- 机器人：[ArduPilot / ArduSub-3.5.4](https://github.com/ArduPilot/ardupilot/tree/ArduSub-3.5.4)。
- 精选文件、版本与 SHA256 见 `来源与清单.json`；体积与完整性结果见 `校验结果.json`。
- 源文件保留上游版权及许可证；配置采用通用待填模板，适合公开分享。

---

# 手柄与操控

**用途**：读取手柄、校准轴范围、处理回中与反向，将轴和按钮转换为操控量。

**源码入口**（相对本目录 QGC/）：
- `src/Joystick/JoystickSDL.cc`：SDL 手柄采集。
- `src/Joystick/Joystick.cc`：轴归一化、油门模式、按钮位图；发出 manualControl 信号。
- `src/VehicleSetup/JoystickConfig.qml` 与 Controller：校准、轴映射和按钮配置界面。

**配置要点**：换手柄后检查轴号、正反向、回中和油门模式。按设备校准结果填写 Roll/Pitch/Yaw/Throttle 轴号及反向标记。操控信号随后进入 02 的 UAS 发送链路；按钮功能与 03 的机器人参数一起核对。


---

# 通信与参数读写

**用途**：管理连接、收发 MAVLink、维护车辆状态并同步参数。

**源码入口**（QGC/）：
- `src/comm/LinkManager.cc`：链路管理；SerialLink / UDPLink / TCPLink：传输实现。
- `src/comm/MAVLinkProtocol.cc`：MAVLink 消息接收和协议处理。
- `src/uas/UAS.cc` 的 setExternalControlSetpoint：将手柄输入打包为 MANUAL_CONTROL，经 priorityLink 发送。
- `src/FactSystem/ParameterManager.cc`：参数下载、更新和保存流程；Fact 表示参数值及元数据。
- `src/Vehicle/Vehicle.cc`：车辆状态与指令；motorTest 发送 MAV_CMD_DO_MOTOR_TEST。

**配置要点**：UDP 内置默认端口 14550；地面站系统 ID 按目标通信网络统一规划。按实际网络填写 IP、端口、转发关系；使用串口时另核对 COM 口和波特率。参数修改后读回核对，涉及重启的项目按元数据提示处理。连接设置定义集中在 05。


---

# 机器人配置与控制

**用途**：把地面站的参数/电机配置界面，与机器人端的输入处理、控制模式、推进器混控对应起来。

**QGC 源码入口**（QGC/）：
- `src/FirmwarePlugin/APM/ArduSubFirmwarePlugin.*`：ArduSub 模式及功能适配；APMParameterMetaData 解析参数说明。
- `src/AutoPilotPlugins/APM/APMSubFrameComponent.qml`：选择推进器布局。
- `APMSubMotorComponent.qml`：电机测试、方向设置；连接 02 的 Vehicle::motorTest。
- 同目录 Safety/Tuning/Lights/CameraSub/Sensors 相关文件：失效保护、调参、灯光、相机和传感器配置。

**机器人端源码入口**（ArduSub/ 下保留项目原路径）：
- `ArduSub/joystick.cpp`：解释 MANUAL_CONTROL 的轴和按钮。
- `ArduSub/GCS_Mavlink.cpp`：接收通信消息与指令。
- `ArduSub/control_*.cpp`、`motors.cpp`：手动/稳姿/定深控制及电机输出衔接。
- `libraries/AP_Motors/AP_Motors6DOF.*`：按布局将运动控制量混合成各推进器输出。
- Parameters、Sub.h、failsafe 相关文件：参数定义、模块组织和安全逻辑。

**配置要点**：将 FRAME_CONFIG、MOT_n_DIRECTION、SERVOx_* 与实机电机编号/接线表一一对应；再核对手柄按钮、深度基准和失联保护。先保存参数备份，修改后读回确认；推进器测试按设备安全要求在受控环境进行。

**版本**：QGC v3.5.6；机器人选读采用官方 ArduSub-3.5.4（2019-04-01），固定提交 `05fed49b8ee1db3c6bca924e0b334b2e0ece980d`。按队伍实际固件版本对照使用。详细文件来源与哈希见清单。


---

# 视频与图传

**用途**：接收机器人视频流，建立 GStreamer 管线，显示画面并处理录像。

**源码入口**（QGC/）：
- `src/FlightDisplay/VideoManager.cc`：视频源与接收器管理。
- `src/VideoStreaming/VideoReceiver.cc`：接收、解码与录像流程。
- `src/VideoStreaming/VideoSurface.*`：画面显示衔接。
- `src/Settings/VideoSettings.cc`：视频设置；对应 JSON 在 05。

**配置要点**：UDP 视频内置默认端口 5600；还可研究 RTSP/TCP 地址设置。队伍记录应包含发送端地址、编码/封装、接收端口与录像目录。视频与操控遥测分开排查：先确认网络可达，再检查流格式、端口及解码链路。


---

# 配置速查

**文件用途**：
- `队伍配置记录.example.json`：汇总连接、视频、手柄和机器人配置，填写后便于队友交接。
- `QGC/src/Settings/AutoConnect.SettingsGroup.json`：自动连接项目及默认值。
- `QGC/src/Settings/Video.SettingsGroup.json`：视频源、端口、RTSP/TCP、录像设置。
- `QGC/src/comm/USBBoardInfo.json`：USB 板卡识别信息。
- `QGC/src/FirmwarePlugin/APM/APMParameterFactMetaData.Sub.3.5.xml`：ArduSub 3.5 参数说明、单位、范围和枚举。
- `USB接口/*.inf`：PX4FMU / ChibiOS 的 Windows USB 串口匹配与服务配置，可查看 VID/PID 和端口绑定规则。

**参数检索顺序**：
| 项目 | 重点参数 |
|---|---|
| 推进器布局与方向 | FRAME_CONFIG、MOT_n_DIRECTION |
| 输出通道与范围 | SERVOx_FUNCTION、SERVOx_MIN/MAX/TRIM、MOT_PWM_* |
| 手柄与按钮 | BTNn_FUNCTION、BTNn_SFUNCTION、JS_GAIN_* |
| 失联保护 | FS_GCS_ENABLE、FS_PILOT_INPUT、FS_PILOT_TIMEOUT |
| 深度与传感器 | SURFACE_DEPTH、压力/IMU/电池相关参数 |

**配置要点**：先记录飞控型号、固件版本、推进器接线，再选择对应参数解释。把实机参数导出、按钮表和电机编号一起维护；默认端口用于初始核对，最终以队伍记录为准。示例中的待填项按目标设备和网络填写。
