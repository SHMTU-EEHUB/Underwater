# 通信与参数读写

**用途**：管理连接、收发 MAVLink、维护车辆状态并同步参数。

**源码入口**（QGC/）：
- `src/comm/LinkManager.cc`：链路管理；SerialLink / UDPLink / TCPLink：传输实现。
- `src/comm/MAVLinkProtocol.cc`：MAVLink 消息接收和协议处理。
- `src/uas/UAS.cc` 的 setExternalControlSetpoint：将手柄输入打包为 MANUAL_CONTROL，经 priorityLink 发送。
- `src/FactSystem/ParameterManager.cc`：参数下载、更新和保存流程；Fact 表示参数值及元数据。
- `src/Vehicle/Vehicle.cc`：车辆状态与指令；motorTest 发送 MAV_CMD_DO_MOTOR_TEST。

**配置要点**：UDP 内置默认端口 14550；地面站系统 ID 按目标通信网络统一规划。按实际网络填写 IP、端口、转发关系；使用串口时另核对 COM 口和波特率。参数修改后读回核对，涉及重启的项目按元数据提示处理。连接设置定义集中在 05。
