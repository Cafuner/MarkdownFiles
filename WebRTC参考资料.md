# WebRTC参考资料

### 参考资料

- [移动边缘计算——MEC](http://www.uml.org.cn/xjs/202003114.asp?artid=23048)

- [边缘计算如何赋能视频行业](https://cloud.tencent.com/developer/article/1456145)

- [一文盘点直播技术中的编解码、直播协议、网络传输与简单实现](https://segmentfault.com/a/1190000016819686)

- ###### [WEBRTC三种类型（Mesh、MCU 和 SFU）的多方通信架构](https://www.cnblogs.com/yiyi17/p/12076657.html)

- ###### [webrtc笔记(3): 多人视频通讯常用架构Mesh/MCU/SFU](https://www.cnblogs.com/yjmyzz/p/webrtc-multiparty-call-architecture.html)

- [SFU (Selective Forwarding Unit)](https://webrtcglossary.com/sfu/)

- [架构设计：基于Webrtc、Kurento的一种低延迟架构实现](https://www.jianshu.com/p/ac307371def4)

- [RTP/RTCP/RTMP/RTSP 的区别](https://www.jianshu.com/p/1565efb1ca6f)

- [如何搭建一个完整的视频直播系统？](https://www.zhihu.com/question/42162310)

- [No Delay RTSP streaming](https://www.cctvforum.com/topic/41590-no-delay-rtsp-streaming/)

- [音视频算法在淘宝中的应用](https://cloud.tencent.com/developer/article/1871247)

- 智能会议系统（7）---实时音视频技术难点及解决方案https://blog.csdn.net/zhangbijun1230/article/details/81749055

- 语音视频社交中回声消除技术是如何实现的https://blog.csdn.net/zego_0616/article/details/78793924

- [Zoom如何为用户提供稳定的、海量的视频容量](https://zoomcloud.cn/2438.html)

- [漫谈 WebRTC 一: 何谓Simulcast， WebRTC中的Simulcast](https://blog.csdn.net/volvet/article/details/78451850)

- ICE  https://www.imweb.io/topic/5a4a6cb2a192c3b460fce37f

- SVC（Scalable Video Coding）分级编码https://baike.baidu.com/item/H.264%20SVC/263248

- Introduction to WebRTC https://www.slideshare.net/alexpiwi5/streaming-media-west-webrtc-the-future-of-low-latency-streaming

  > WebRTC: P2P，需要借助signal server交换control、media metadata等信息

- [WebRTC直播技术(一)-初探WebRTC](https://cloud.tencent.com/developer/article/1009489)

- [WebRTC直播技术(二)-ICE/STUN/TURN](https://cloud.tencent.com/developer/article/1547638)

- SIP（Session initiate protocol，会话发起协议）

- [在 WebRTC 应用中增加录制功能前，该优先考虑的难点](https://webrtc.org.cn/20210428-paas-e2ee/)

- [WebRTC与4K视频](https://webrtc.org.cn/4kwebrtc/)

  [![4k](http://webrtc.org.cn/wp-content/uploads/2017/04/4k.png)](http://webrtc.org.cn/wp-content/uploads/2017/04/4k.png)

  [1080P、2K、4K含义](https://new.qq.com/omn/20210513/20210513A03JJK00.html)  

  > K：视频像素的总列数，4K=3840\*2160或4096\*2160，2K=2048*1080
  >
  > P(Progressive)：视频像素的总行数，1080P=1920\*1080，720P=1280\*720
  >
  > 1080P归属于2K的类别（但通常区别对待）

- [如何着手学习WebRTC开发](https://www.sohu.com/a/146536246_458408) 



# Mediasoup

- 项目地址https://github.com/versatica/mediasoup、

  Build DOC：https://github.com/versatica/mediasoup/blob/v3/doc/Building.md

- 文档https://mediasoup.org/documentation/v3/

  中文翻译：https://www.yuque.com/u239523/kw98np/phc9wp

  > A **device** represents an endpoint that connects to a mediasoup [Router](https://mediasoup.org/documentation/v3/mediasoup/api/#Router) to send and/or receive media. This is the <u>entry point</u> for JavaScript client side applications (such as web applications).

- 官方demohttps://github.com/versatica/mediasoup-demo

  启动方式：

  ```bash
  # sever & app
  export MEDIASOUP_LISTEN_IP="0.0.0.0"
  export MEDIASOUP_ANNOUNCED_IP="your.local.ip"
  npm start
  ```

  **示例URL参数**：https://x.x.x.x:3000/?info=true&roomId=xxxxxx&throttleSecret=foo&svc=S3T3&externalVideo=false&faceDetection=true

- Demo example（GitHub projects）https://mediasoup.org/documentation/examples/