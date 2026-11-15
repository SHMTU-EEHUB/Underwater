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
