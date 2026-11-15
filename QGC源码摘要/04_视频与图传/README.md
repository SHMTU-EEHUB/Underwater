# 视频与图传

**用途**：接收机器人视频流，建立 GStreamer 管线，显示画面并处理录像。

**源码入口**（QGC/）：
- `src/FlightDisplay/VideoManager.cc`：视频源与接收器管理。
- `src/VideoStreaming/VideoReceiver.cc`：接收、解码与录像流程。
- `src/VideoStreaming/VideoSurface.*`：画面显示衔接。
- `src/Settings/VideoSettings.cc`：视频设置；对应 JSON 在 05。

**配置要点**：UDP 视频内置默认端口 5600；还可研究 RTSP/TCP 地址设置。队伍记录应包含发送端地址、编码/封装、接收端口与录像目录。视频与操控遥测分开排查：先确认网络可达，再检查流格式、端口及解码链路。
