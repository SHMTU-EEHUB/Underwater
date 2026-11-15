# 手柄与操控

**用途**：读取手柄、校准轴范围、处理回中与反向，将轴和按钮转换为操控量。

**源码入口**（相对本目录 QGC/）：
- `src/Joystick/JoystickSDL.cc`：SDL 手柄采集。
- `src/Joystick/Joystick.cc`：轴归一化、油门模式、按钮位图；发出 manualControl 信号。
- `src/VehicleSetup/JoystickConfig.qml` 与 Controller：校准、轴映射和按钮配置界面。

**配置要点**：换手柄后检查轴号、正反向、回中和油门模式。按设备校准结果填写 Roll/Pitch/Yaw/Throttle 轴号及反向标记。操控信号随后进入 02 的 UAS 发送链路；按钮功能与 03 的机器人参数一起核对。
