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
