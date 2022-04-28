# HTML5PlayRtsp

### 所用到的开源项目

#### AnyExeToService 
https://github.com/cxwl3sxl/AnyExeToService

#### AServer 
https://github.com/kklldog/AServer

### 技术原理
1. 用户安装浏览器插件VideoAdapter,安装完成之后会在客户机器监听8536端口.
1. 当用户想播放某个RTSP视频的时候.发送一个跨域请求到客户机器的8536端口,参数是某个rtsp播放地址
2. 收到播放地址之后.调用Process 执行rtsp转flv命令.
3. 返回flv播放地址


### RTSP转flv原理

nginx-rtmp 模块
