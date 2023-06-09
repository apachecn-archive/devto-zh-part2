# WebRTC SDP(会话描述协议)详细信息

> 原文：<https://dev.to/lucpattyn/webrtc-sdp-session-description-protocol-details--5593>

WebRTC

草案的 SDP-Nanda Kumar-RTC web-SDP-01

# 
[摘要](#rfc.abstract)

web 实时通信(WebRTC)[【WebRTC】](#WEBRTC)工作组负责为在两个对等体的 Web 浏览器之间使用音频、视频和数据的直接交互式丰富通信提供协议支持。在 WebRTC 框架中，会话描述协议(SDP)[【RFC 4566】](#RFC4566)用于在对等体之间协商会话能力。这种协商基于 RFC 3264[【RFC 3264】](#RFC3264)中描述的 SDP 提供/应答交换机制进行。

本文旨在介绍 SDP 在最常见的 WebRTC 用例中的作用。

IETF 已被告知与本文档中包含的部分或全部规范相关的知识产权声明。欲了解更多信息，请查阅要求权利的在线列表。

# [该备忘录的状态](#rfc.status)

该网络草案完全符合 BCP 78 和 BCP 79 的规定。

互联网草案是互联网工程任务组(IETF)的工作文件。请注意，其他小组也可能将工作文档作为互联网草稿分发。当前互联网草案的列表在 http://datatracker.ietf.org/drafts/current/.

网络草稿是有效期最长为六个月的草稿文档，可以随时被其他文档更新、替换或废弃。使用网络草稿作为参考资料或引用它们而不是“正在进行的工作”是不合适的。

该网络草案将于 2013 年 8 月 27 日到期。

# [版权声明](#rfc.copyrightnotice)

版权所有(c) 2013 IETF Trust 和文档作者。保留所有权利。

本文档受 BCP 78 和 IETF Trust 关于 IETF 文档(http://trustee.ietf.org/license-info)的法律规定的约束，这些法律规定在本文档发布之日有效。请仔细阅读这些文档，因为它们描述了您对本文档的权利和限制。从本文档中提取的代码组件必须包括信托法律条款第 4.e 节中所述的简化 BSD 许可证文本，并按照简化 BSD 许可证中所述提供无担保。

* * *

# [目录](#rfc.toc)

*   1.[简介](#rfc.section.1)
*   2.[术语](#rfc.section.2)
*   3. [SDP 和 WebRTC](#rfc.section.3)
*   4.[提供/回答和 WebRTC](#rfc.section.4)
*   5. [WebRTC 会话描述示例](#rfc.section.5)
*   5.1.[通过 RTCP 反馈确保双向音频、视频和数据的安全](#rfc.section.5.1)
*   5.2.[保护双向音频、视频、数据并移除数据流](#rfc.section.5.2)
*   5.3.[安全的双向音频、视频，支持未知的捆绑包](#rfc.section.5.3)
*   5.4.[安全的双向音频、视频，支持已知的捆绑包](#rfc.section.5.4)
*   5.5.[不受支持的安全双向音频、视频捆绑包](#rfc.section.5.5)
*   5.6.[与 2 个视频流成功进行单向会话](#rfc.section.5.6)
*   5.7.[添加新媒体(视频)](#rfc.section.5.7)
*   5.8.[用 2 个编码、2 个摄像机成功联播](#rfc.section.5.8)
*   5.9.[成功的 SVC 视频流](#rfc.section.5.9)
*   6. [WebRTC < - >遗留互操作示例](#rfc.section.6)
*   6.1.[安全的双向音频、视频和反馈- WebRTC < - >传统互操作](#rfc.section.6.1)
*   7. [IANA 的考量](#rfc.section.7)
*   8.[更改日志](#rfc.section.8)
*   9.[参考文献](#rfc.references)
*   9.1.[标准参考文献](#rfc.references.1)
*   9.2.[参考资料](#rfc.references.2)
*   [作者地址](#rfc.authors)

# [1。](#rfc.section.1)简介

Javascript 会话交换协议(JSEP)[【JSEP】](#JSEP)指定生成[【RFC 3264】](#RFC3264)所需的通用协议，用于在 WebRTC 对等体之间协商建立、更新和拆除多媒体会话。为此，SDP 用于构建[【RFC 3264】](#RFC3264)提议/回答，用于描述(媒体和非媒体)流，以适合会话描述的接收者参与会话。

本文的其余部分组织如下:第 3 和第 4 节提供了 SDP 和提议/回答交换机制的概述。第 5 节和第 6 节为最常见的 WebRTC 用例提供了示例 SDP 用法。

# [2。](#rfc.section.2)术语

本文件中的关键词“必须”、“不得”、“要求”、“应该”、“不应该”、“推荐”、“可以”和“可选”应按照[【RFC 2119】](#RFC2119)中的描述进行解释。

# [3。](#rfc.section.3) SDP 和 WebRTC

本节旨在提供 SDP 及其组件的概述。更深入的了解，建议读者参考[【RFC 4566】](#RFC4566)。

会话描述协议(SDP)[【RFC 4566】](#RFC4566)描述多媒体会话，可以是音频、视频、白板、传真、调制解调器和其他流。它提供了通用的标准表示，以传输不可知的方式描述多媒体会话的各个方面，例如媒体能力、传输地址和相关元数据，用于会话通告、会话邀请和参数协商。

迄今为止，SDP 广泛用于会话发起协议、实时传输协议和实时流协议的环境中。

下图介绍了 SDP 到组件的高级分解，这些组件在语义上描述了一个多媒体会话，在我们的例子中，比方说一个 WebRTC 会话[【WebRTC】](#WEBRTC)。它并没有涵盖关于 SDP 的所有内容，因此仅用于提供信息。

[【WEBRTC】](#WEBRTC)按照 JSEP 规范[【JSEP】](#JSEP)的描述，提出 JavaScript 应用完全控制多媒体会话的信令平面。JSEP 提供了创建会话特征和媒体定义信息的机制，以基于 SDP 交换进行会话。

在这种情况下，SDP 有两个目的:

*   从语法上提供语法结构
*   从语义上传达参与者的意图和能力。

# [4。](#rfc.section.4)提供/回答和 WebRTC

本节介绍由 WebRTC 授权的 SDP 提供/应答交换机制，用于在建立、更新和拆除 WebRTC 会话时协商会话功能。本节内容有意简短，感兴趣的读者可向[【RFC 3264】](#RFC3264)了解协议操作的具体细节。

提议/应答[【RFC 3264】](#RFC3264)模型规定了用于创建多媒体流的会话描述协议(SDP)消息的双边交换的规则。它定义了一种协议，其中所涉及的参与者根据 SDP 模型从彼此的角度交换期望的会话特征，以协商它们之间的会话。

在最基本的形式中，协议操作开始于参与者之一发送描述其开始多媒体通信会话的意图的初始 SDP 提议。接收该提议的参与者可以生成 SDP 应答，接受该提议或者拒绝该提议。如果会话被接受，则提议应答模型保证参与者之间对多媒体会话的共同看法。

任何时候，任一参与者都可以生成新的 SDP 提议，以更新正在进行的会话。

在 WebRTC 的上下文中，提供/应答模型为 WebRTC 对等体定义了状态机，以便在初始设置阶段和最终会话更新期间在它们之间协商会话描述。用于 WebRTC 的 Javascript 会话建立协议规范[【JSEP】](#JSEP)提供了用于生成[【RFC 3264】](#RFC3264)SDP 提议和应答的机制，以便会话双方就细节达成一致，例如要发送/接收的媒体格式列表、带宽信息、加密参数、传输参数。

以下部分提供了最常见的 WebRTC 用例的 SDP 消息细节和交换的示例。

# [5。](#rfc.section.5) WebRTC 会话描述示例

典型的基于网络的实时多媒体通信会话的特征如下:

*   它具有零个或多个纯音频、纯视频或音频/视频媒体流
*   可以包含零个或多个非媒体数据流
*   所有的溪流都由 DTLS/SRTP 公司担保
*   NAT 穿越的 ICE 处理
*   基于 IPv4、IPv6 和双栈的客户端上的会话。

如前所述，[【WEBRTC】](#WEBRTC)提议使用基于 SDP 的提议/应答模式来协商对等方浏览器之间的多媒体会话。基于前面章节的概念，以下小节试图描述 SDP 在最常见的 WEBRTC 用例中的使用。

在所有用例中，除非另有说明，否则 Alice 和 Bob 都被假定为 WebRTC 对等体。必要时，针对 SDP 行提供适当 RFC 和注释的指针。

# [5.1。](#rfc.section.5.1) [利用 RTCP 反馈保护双向音频、视频和数据](#successful-2way-savpf)

这个用例允许两个用户在他们支持 WebRTC 的 Web 浏览器上安全地参与双向通信会话。

```
title WebRTC Session - 2-Way Secure Audio,Video with RTCP Feedback
Alice->Bob: Offer(Audio:G.711,Opus,iLBC Video:H.264,VP8)
Bob->Alice: Answer(Audio:Opus,DTMF Video:H.264)
Alice->Bob: Two-way Opus Audio, H.264 Video
note right of Alice
  Session also suports RTP/RTCP Mux, RTCP feedback (nack,pli) 
end note

```

更具体地说，这个用例演示了 WebRTC 会话的以下方面

*   基于 DTLS 加密的 SRTP
*   RTP 和 RTCP 慕星
*   基于 RTCP 的反馈和缩减尺寸支持
*   NAT 穿越的 ICE 处理
*   音频编解码器提供:PCMU，Opus，iLBC
*   音频编解码器应答:Opus
*   提供的视频编解码器:H.264、VP8
*   视频编解码器回答:H.264
*   数据通道支持

下面的表(5.1 和 5.2)详细记录了交换的初始 SDP 提议和应答消息。

为数据通道指定的确切 SDP 参数仍在工作组讨论中，预计将在做出决定后更新。

5.1 SDP Offer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20518 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566)-会话起源信息 |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245)-会话级 ICE 参数 |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245)-会话级 ICE 参数 |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245)-SRTP 的会话 DTLS 指纹 |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506)-Alice 打算在本次会议中使用尺寸缩小的 RTCP |
| m =音频 54609 RTP/SAVPF 0 109 98 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:0 PCMU/8000 | [【RFC 3551】](#RFC3551) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=rtpmap:98 iLBC/8000 a=fmtp:98 模式=20 | [【RFC 3952】](#RFC3952) |
| a=sendrecv | [【RFC 3264】](#RFC3264)-爱丽丝可以发送和接收音频 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761)-Alice 可以在端口 54609 上执行 RTP/RTCP 复用 |
| b=AS:256 | [【RFC 4566】](#RFC4566) |
| b =卢比:0 | [【RFC 3556】](#RFC3556) |
| b=RR:0 | [【RFC 3556】](#RFC3556) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245)-宿主 ICE 候选 |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245)-上述候选主机的服务器自反 ICE 候选 |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245)-第二候选主机 |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245)-第二候选主机的服务器自反候选 |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104)-表示 NACK RTCP 反馈支持 |
| m =视频 62537 RTP/SAVPF 99 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:99 H264/90000 | [【RFC 3984】](#RFC3984) |
| a = fmtp:99 profile-level-id = 4d 0028；打包模式=1 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264)-爱丽丝可以发送和接收视频 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761)-Alice 可以在端口 62537 上执行 RTP/RTCP 复用 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 62537 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 62537 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 54721 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54721 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:99 nack pli | [【RFC 5104】](#RFC5104)-表示支持画面丢失指示和 NACK |
| a=rtcp-fb:99 ccm | [【RFC 5104】](#RFC5104)-全帧内请求-编解码器控制消息支持 |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104)-表示支持画面丢失指示和 NACK |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104)-全帧内请求-编解码器控制消息支持 |
| m =申请 56966 DTLS/SCTP 5000 | [【草案-IETF-RTC web-数据-信道】](#draft-ietf-rtcweb-data-channel) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a = sctpmap:5000 webrtc-数据通道 1 | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp)-一个聊天类型的数据流 |
| a = webrtc-data channel:5000 stream = 1；label= "频道 1 "；子协议= "聊天"； | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| a=sendrecv | [【RFC 3264】](#RFC3264)-Alice 可以发送和接收非媒体数据 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761)-Alice 可以在端口 56966 上执行 RTP/RTCP 复用 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 56966 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 56966 典型值 srflx raddr 192 . 168 . 1 . 7 r 端口 56966 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 51641 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 51641 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 51641 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:5000 纳克 | [【RFC 5104】](#RFC5104)-表示数据通道的 NACK 反馈支持 |

5.1 SDP Answer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566)-会话起源信息 |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245)-会话级 ICE 用户名片段 |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245)-会话级 ICE 密码 |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245)-SRTP 的会话 DTLS 指纹 |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506)-Alice 打算在本次会议中使用尺寸缩小的 RTCP |
| m =音频 49203 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264)-Bob 可以发送和接收音频 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761)-Bob 可以在端口 49203 上执行 RTP/RTCP 复用 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245)-Opus 流的主机 ICE 候选 |
| a = ccndidate:1 1 UDP 1694302207 98 . 248 . 92 . 77 49203 Sr LX rad dr 192 . 168 . 1 . 7 r rt 49203 型 | [【RFC 5245】](#RFC5245)-上述候选主机的服务器自反 ICE 候选 |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245)-第二候选主机 |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245)-第二候选主机的服务器自反候选 |
| m =视频 63130 RTP/SAVPF 99 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:99 H264/90000 | [【RFC 3984】](#RFC3984) |
| a = fmtp:99 profile-level-id = 4d 0028；打包模式=1 | [【RFC 3984】](#RFC3984) |
| a=sendrecv | [【RFC 3264】](#RFC3264)-Bob 可以发送和接收视频 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761)-Bob 可以在端口 63130 上执行 RTP/RTCP 复用 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63130 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 63130 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 63130 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56607 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 56607 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 56607 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:99 nack pli | [【RFC 5104】](#RFC5104)-表示支持画面丢失指示和 NACK |
| a=rtcp-fb:99 ccm | [【RFC 5104】](#RFC5104)-全帧内请求-编解码器控制消息支持 |
| m =应用程序 55700 DTLS/SCTP 5000 | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a = sctpmap:5000 webrtc-数据通道 1 | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| a = webrtc-data channel:5000 stream = 1；label= "频道 1 "；子协议= "聊天"； | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| a=sendrecv | [【RFC 3264】](#RFC3264)-Bob 可以发送和接收非媒体数据 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761)-Bob 可以在端口 55700 上执行 RTP/RTCP 复用 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 55700 典型主机 | [【RFC 5245】](#RFC5245)-参考 4.1 SDP 报价 |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 55700 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 55700 | 参考 4.1 SDP 报价 |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 58137 典型主机 | 参考 4.1 SDP 报价 |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 58137 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 581371 | 参考 4.1 SDP 报价 |
| a=rtcp-fb:5000 纳克 | [【RFC 5104】](#RFC5104)-表示数据通道的 NACK 反馈支持 |

# 5.2。 [保护双向音频、视频、数据并移除数据流](#sec.successful-session-remove-datachannel)

该场景基于第 5.1 节中的用例，它通过 Alice 在会话进行中移除数据流来扩展。

```
title WebRTC Session (Audio,Video,Datachannel) - Drop Datachannel
note right of Alice
    Alice & Bob are in a two-way audio,video and datachannel session.
   Alice decides to stop the datachannel stream
end note
Alice->Bob: Offer(Audio:Opus Video:VP8, Application: Drop)
Bob->Alice: Answer(Audio:Opus Video:VP8, Application:Drop)
Alice->Bob: Two-way Opus Audio and VP8 Video

```

作为先决条件，已经建立了双向音频、视频和数据会话。

5.2 SDP Updated Offer w/DataChannel Drop

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20519 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 54609 RTP/SAVPF 0 109 98 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:0 PCMU/8000 | [【RFC 3551】](#RFC3551) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=rtpmap:98 iLBC/8000 a=fmtp:98 模式=20 | [【RFC 3952】](#RFC3952) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 62537 RTP/SAVPF 99 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:99 H264/90000 | [【RFC 3984】](#RFC3984) |
| a = fmtp:99 profile-level-id = 4d 0028；打包模式=1 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 62537 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 62537 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 54721 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54721 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:99 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:99 ccm | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |
| m =应用程序 0 DTLS/SCTP 5000 | [【draft-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp)-端口 0 表示丢弃数据流 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a = SCT map:5000 webrtc-数据通道 1 | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| a = webrtc-data channel:5000 stream = 1；label= "频道 1 "；子协议= "聊天"； | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 56966 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 56966 典型值 srflx raddr 192 . 168 . 1 . 7 r 端口 56966 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 51641 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 51641 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 51641 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:5000 纳克 | [【RFC 5104】](#RFC5104) |

5.2 SDP Updated Answer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 49203 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a = ccndidate:1 1 UDP 1694302207 98 . 248 . 92 . 77 49203 Sr LX rad dr 192 . 168 . 1 . 7 r rt 49203 型 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |
| m =视频 63130 RTP/SAVPF 99 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:99 H264/90000 | [【RFC 3984】](#RFC3984) |
| a = fmtp:99 profile-level-id = 4d 0028；打包模式=1 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63130 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 63130 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 63130 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56607 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 56607 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 56607 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:99 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:99 ccm | [【RFC 5104】](#RFC5104) |
| m =应用程序 0 DTLS/SCTP 5000 | 鲍勃接受丢弃数据流 |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a = sctpmap:5000 webrtc-数据通道 1 | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| a = webrtc-data channel:5000 stream = 1；label= "频道 1 "；子协议= "聊天"； | [【草案-IETF-MMU sic-SCTP-SDP】](#draft-ietf-mmusic-sctp-sdp) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 55700 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 55700 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 55700 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 58137 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 58137 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 581371 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:5000 纳克 | [【RFC 5104】](#RFC5104) |

# 5.3。 [安全双向音频、视频 w/捆绑支持未知](#successful-2way-bundle)

此使用案例展示了当 Alice 不确定 Bob 的捆绑包支持时，SDP 捆绑包协商成功的音频和视频复用场景。下表还说明了 Alice 认为可以建立双向捆绑会话后的附加 SDP 提议/回答交换。

```
title WebRTC Session - 2-Way Secure Audio,Video with BUNDLE
Alice->Bob: Offer(Audio:Opus Video:VP8) with BUNDLE support and different port numbers
Bob->Alice: Answer(Audio:Opus Video:VP8) indicating its support for BUNDLE
Alice->Bob: Updated Offer(Audio:Opus Video:VP8) with BUNDLE support and same port number
Bob->Alice: Answer(Audio:Opus Video:VP8) accepts the updated Offer
Alice->Bob: Two-way Opus Audio, H.264 Video over a single 5-tuple

```

5.3 SDP Offer w/BUNDLE

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20518 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| a =组:捆绑 foo 栏 | [【draft-IETF-MMU sic-SDP-bundle-negotiation】](#draft-ietf-mmusic-sdp-bundle-negotiation)Alice 支持 m =行的分组 |
| m =音频 54609 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| a=mid:foo | [【RFC 5888】](#RFC5888)音频 m =具有唯一端口号的捆绑组的线路部分 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 62537 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| a=mid:bar | [【RFC 5888】](#RFC5888)视频 m =具有唯一端口号的捆绑组的线路部分 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b=AS:1000 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 62537 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 62537 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 54721 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54721 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

5.3 SDP Answer w/BUNDLE

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| a =组:捆绑 foo 栏 | [【draft-IETF-MMU sic-SDP-bundle-negotiation】](#draft-ietf-mmusic-sdp-bundle-negotiation)Bob 支持 m =行的分组，并表示他也对此感兴趣 |
| m =音频 49203 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| a=mid:foo | [【RFC 5888】](#RFC5888)音频 m =线束组的线路部分 |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 49203 典型 Sr flx raddr 192 . 168 . 1 . 7 r 端口 49203 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |
| m =视频 49203 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| a=mid:bar | [【RFC 5888】](#RFC5888)视频 m =线束组的线部分，音频线的端口重复 |
| b=AS:1000 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 49203 典型 Sr flx raddr 192 . 168 . 1 . 7 r 端口 49203 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |

5.3 SDP Updated Offer w/Bundle

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20518 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| a =组:捆绑 foo 栏 | [【draft-IETF-MMU sic-SDP-bundle-negotiation】](#draft-ietf-mmusic-sdp-bundle-negotiation)Alice 支持 m =行的分组 |
| m =音频 54609 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| a=mid:foo | [【RFC 5888】](#RFC5888)音频 m =具有唯一端口号的捆绑组的线路部分 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 54609 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| a=mid:bar | [【RFC 5888】](#RFC5888)视频 m =捆绑组的线路部分，带有来自音频线路的重复端口 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b=AS:1000 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

5.3 SDP Answer w/BUNDLE

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| a =组:捆绑 foo 栏 | [【draft-IETF-MMU sic-SDP-bundle-negotiation】](#draft-ietf-mmusic-sdp-bundle-negotiation)Bob 支持 m =行的分组，并表示他也对此感兴趣 |
| m =音频 49203 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| a=mid:foo | [【RFC 5888】](#RFC5888)音频 m =线束组的线路部分 |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 49203 典型 Sr flx raddr 192 . 168 . 1 . 7 r 端口 49203 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |
| m =视频 49203 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| a=mid:bar | [【RFC 5888】](#RFC5888)视频 m =线束组的线部分，音频线的端口重复 |
| b=AS:1000 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 49203 典型 Sr flx raddr 192 . 168 . 1 . 7 r 端口 49203 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |

# 5.4。 [安全双向音频、视频 w/捆绑支持已知](#successful-2way-bundle-one-exchange)

此使用案例展示了一个成功的音频和视频多路复用场景，其中 SDP 捆绑包协商 Alice(提议者)了解 Bob(回答者)的捆绑包支持。

```
title WebRTC Session - 2-Way Secure Audio,Video with BUNDLE Support Known
Alice->Bob: Offer(Audio:Opus Video:VP8) with identical port numbers
Bob->Alice: Answer(Audio:Opus Video:VP8) with identical port numbers
Alice->Bob: Two-way Opus Audio, H.264 Video over a single 5-tuple

```

5.4 SDP Offer w/BUNDLE

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20518 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| a =组:捆绑 foo 栏 | [【draft-IETF-MMU sic-SDP-bundle-negotiation】](#draft-ietf-mmusic-sdp-bundle-negotiation)Alice 支持 m =行的分组 |
| m =音频 10000 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| a=mid:foo | [【RFC 5888】](#RFC5888)音频 m =具有唯一端口号的捆绑组的线路部分 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 10000 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 10000 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 10000 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 10000 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| a=mid:bar | [【RFC 5888】](#RFC5888)视频 m =音频端口重复的束组的线部分 |
| c = 24.23.204.141 IP4 | [【RFC 4566】](#RFC4566) |
| b=AS:1000 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 10000 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 10000 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 10000 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

5.4 SDP Answer w/BUNDLE

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| a =组:捆绑 foo 栏 | [【draft-IETF-MMU sic-SDP-bundle-negotiation】](#draft-ietf-mmusic-sdp-bundle-negotiation)Bob 支持 m =行的分组，并表示他也对此感兴趣 |
| m =音频 20000 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| a=mid:foo | [【RFC 5888】](#RFC5888)音频 m =线束组的线路部分 |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 20000 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 20000 typ srflx raddr 192 . 168 . 1 . 7 r port 20000 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |
| m =视频 20000 RTP/SAVPF 120 | a=mid:bar |
| [【RFC 5888】](#RFC5888)视频 m =线束组的线部分，音频线的端口重复 | b =阿斯:200 |
| [【RFC 4566】](#RFC4566) | c=在 IP4 98.248.92.77 |
| [【RFC 4566】](#RFC4566) | a=rtpmap:120 VP8/90000 |
| [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) | a=sendrecv |
| [【RFC 3264】](#RFC3264) | a=rtcp-mux |
| [【RFC 5761】](#RFC5761) | a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 20000 典型主机 |
| [【RFC 5245】](#RFC5245) | a =候选人:1 1 UDP 1694302207 98.248.92.77 20000 typ srflx raddr 192 . 168 . 1 . 7 r port 20000 |
| [【RFC 5245】](#RFC5245) | a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 |
| [【RFC 5245】](#RFC5245) | a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 |
| [【RFC 5245】](#RFC5245) |

# [5.5。](#rfc.section.5.5) [安全双向音频、视频，不支持捆绑](#successful-2way-bundle-unsupported)

此使用案例说明了当远端(Bob)不支持媒体捆绑或不想在单个 5 元组上分组 m =行时的 SDP 提供/应答交换。

```
title WebRTC Session - 2-Way Secure Audio,Video with BUNDLE Unsupported
Alice->Bob: Offer(Audio:Opus Video:VP8)  with BUNDLE support, unique port numbers
Bob->Alice: Answer(Audio:Opus Video:VP8) with no BUNDLE support, unique port numbers
Alice->Bob: Two-way Opus Audio, H.264 Video over 2 different RTP sessions.

```

5.5 SDP Offer w/BUNDLE

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20518 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| a =组:捆绑 foo 栏 | [【draft-IETF-MMU sic-SDP-bundle-negotiation】](#draft-ietf-mmusic-sdp-bundle-negotiation)Alice 支持 m =行的分组 |
| m =音频 55232 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| a=mid:foo | [【RFC 5888】](#RFC5888)音频 m =具有唯一端口号的捆绑组的线路部分 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 55232 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 55232 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 55232 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 54332 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| a=mid:bar | [【RFC 5888】](#RFC5888)视频 m =具有唯一端口号的捆绑组的线路部分 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b=AS:1000 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54332 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 54332 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54332 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 54721 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54721 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

5.5 SDP Answer w/BUNDLE

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 53214 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| b =阿斯:200 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 53214 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 53214 典型值 srflx raddr 192 . 168 . 1 . 7 r 端口 53214 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |
| m =视频 58679 RTP/SAVPF 120 | b =阿斯:200 |
| [【RFC 4566】](#RFC4566) | c=在 IP4 98.248.92.77 |
| [【RFC 4566】](#RFC4566) | a=rtpmap:120 VP8/90000 |
| [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) | a=sendrecv |
| [【RFC 3264】](#RFC3264) | a=rtcp-mux |
| [【RFC 5761】](#RFC5761) | a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 58679 典型主机 |
| [【RFC 5245】](#RFC5245) | a =候选人:1 1 UDP 1694302207 98.248.92.77 58679 典型值 srflx raddr 192 . 168 . 1 . 7 r 端口 58679 |
| [【RFC 5245】](#RFC5245) | a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56607 典型主机 |
| [【RFC 5245】](#RFC5245) | a =候选人:1 2 UDP 1694302206 98.248.92.77 56607 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 56607 |
| [【RFC 5245】](#RFC5245) |

# 5.6。 [与 2 个视频流成功进行单向会话](#sec.successful-session-two-video-streams)

在这种情况下，爱丽丝和鲍勃进行单向多媒体会话，鲍勃接收两个视频流，一个对应于爱丽丝的视频，另一个对应于她的演示幻灯片。

```
title 1 Way Audio & Video w/2 Video Streams
note right of Alice
Alice offers 2 sendonly video streams
one for her video feed and other for her presentation slides.
end note
Alice->Bob: Offer(Audio:Opus, Video1,2: VP8)
note right of Bob
Bob accepts Alice's offer
end note
Bob->Alice: Answer(Audio:Opus, Video1,2: VP8)
Alice->Bob: One-way Opus Audio, VP8 Video
note right of Alice
Bob can hear Alice and see her video feed as well
as her presentation slides.
end note

```

5.6 SDP Offer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20519 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 54609 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送音频流 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 62537 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a =内容:扬声器 | [【RFC 4796】](#RFC4796)-爱丽丝视频的流 1 |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |
| m =视频 62539 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a =内容:幻灯片 | [【RFC 4796】](#RFC4796)-爱丽丝幻灯片的流 2 |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62539 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54723 典型主机 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

5.6 SDP Answer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 49203 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅接收音频流 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| m =视频 63130 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a =内容:扬声器 | [【RFC 4796】](#RFC4796)-爱丽丝视频的流 1 |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅接收视频流 1 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63130 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56607 典型主机 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |
| m =视频 63133 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a =内容:幻灯片 | [【RFC 4796】](#RFC4796)-爱丽丝幻灯片的流 2 |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅接收视频流 2 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63133 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56609 典型主机 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

# [5.7。](#rfc.section.5.7) [添加新媒体(视频)](#sec.successful-session-add-new-media)

此场景描述了当 Alice 决定将视频添加到现有的纯音频会话时的消息交换

```
title Add New Media(Video)
Alice->Bob: Offer(Audio:G.711,Opus,iLBC)
Bob->Alice: Answer(Audio:Opus)
Alice->Bob: Two-way Opus Audio
note right of Alice
Alice decides to add Video
end note
Alice->Bob: Offer(Audio:G.711,Opus,iLBC Video:VP8)
Bob->Alice: Answer(Audio:Opus, Video:VP8)
Alice->Bob: Two-way Opus Audio, VP8 Video

```

5.7 SDP Initial Audio Only Offer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20519 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 54609 RTP/SAVPF 0 109 98 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:0 PCMU/8000 | [【RFC 3551】](#RFC3551) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=rtpmap:98 iLBC/8000 a=fmtp:98 模式=20 | [【RFC 3952】](#RFC3952) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| b=AS:256 | [【RFC 4566】](#RFC4566) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |

5.7 SDP Answer- Audio Only

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 49203 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a = ccndidate:1 1 UDP 1694302207 98 . 248 . 92 . 77 49203 Sr LX rad dr 192 . 168 . 1 . 7 r rt 49203 型 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |

Alice 决定将视频添加到当前会话

5.7 SDP Updated Offer w/Video

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o = IP4 0 . 0 . 0 . 0 中的 alice 20520 0 | [【RFC 4566】](#RFC4566)-版本号增加 |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 54609 RTP/SAVPF 0 109 98 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:0 PCMU/8000 | [【RFC 3551】](#RFC3551) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=rtpmap:98 iLBC/8000 a=fmtp:98 模式=20 | [【RFC 3952】](#RFC3952) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 62537 RTP/SAVPF120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 62537 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 62537 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 54721 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54721 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

5.7 SDP Updated Answer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 49203 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a = ccndidate:1 1 UDP 1694302207 98 . 248 . 92 . 77 49203 Sr LX rad dr 192 . 168 . 1 . 7 r rt 49203 型 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |
| m =视频 63130 RTP/SAVPF 99 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63130 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 63130 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 63130 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56607 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 56607 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 56607 | [【RFC 5245】](#RFC5245) |

# 5.8。 [用 2 个编码、2 个摄像机成功联播](#sec.successful-session-simulcast)

SDP 提议/回答交流展示了同播用例，其中 Alice 有 2 台摄像机，每台摄像机有 2 个编码选项。

```
title 2 Way Successful Simulcast
note right of Alice
Alice offers 2 sendonly video streams with 2 simulcast encodings per stream
end note
Alice->Bob: Offer(Video1,2: H.264)
note left of Bob
Bob accepts Alice's offer and 1 encoding per stream
end note
Bob->Alice: Answer(Video1,2: H.264)
Alice->Bob: One-Way H.264 Video with Bob selected video stream encodings

```

5.8 SDP Offer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20519 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| m =视频 62537 RTP/SAVPF 96 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:96 H264/90000 | [【RFC 3984】](#RFC3984) |
| a = ssrc:12345 cname:axzo 1278 npdlazm 73 | [【RFC 5576】](#RFC5576)[【draft-rescorla-avt core-6222 bis】](#draft-rescorla-avtcore-6222bis)Camera-1，Encoding-1 SSRC with Session CNAME |
| a=ssrc:12345 imageattr:* [x=720，y=576] | [【RFC 5576】](#RFC5576)相机-1，编码-1 分辨率 |
| a=ssrc:12345 帧速率:30 | [【RFC 5576】](#RFC5576)摄像机-1，编码-1 帧率 |
| a = ssrc:67890 cname:1 尼克隆人 897HnrtU | [【RFC 5576】](#RFC5576)[【draft-rescorla-avt core-6222 bis】](#draft-rescorla-avtcore-6222bis)Camera-1，Encoding-2 SSRC with Session CNAME |
| a=ssrc:67890 imageattr:* [x=176，y=144] | [【RFC 5576】](#RFC5576)相机-1，编码-2 分辨率 |
| a=ssrc:67890 帧速率:15 | [【RFC 5576】](#RFC5576)摄像机-1，编码-2 帧率 |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| m =视频 62539 RTP/SAVPF 97 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:97 H264/90000 | [【RFC 3984】](#RFC3984) |
| a = ssrc:54321 cname:o 1278 npdlopzlazm 7 | [【RFC 5576】](#RFC5576)[【draft-rescorla-avt core-6222 bis】](#draft-rescorla-avtcore-6222bis)Camera-2，Encoding-1 SSRC with Session CNAME |
| a=ssrc:54321 imageattr:* [x=352，y=288] | [【RFC 5576】](#RFC5576)摄像头-2，编码-1 分辨率 |
| a=ssrc:54321 帧速率:30 | [【RFC 5576】](#RFC5576)摄像机-2，编码-1 帧率 |
| a = SSR:98760 cname:yt 54 jut 99 jgfvb 23q | [【RFC 5576】](#RFC5576)[【draft-rescorla-avt core-6222 bis】](#draft-rescorla-avtcore-6222bis)Camera-2，Encoding-2 SSRC with Session CNAME |
| a=ssrc:98760 imageattr:* [x=176，y=144] | [【RFC 5576】](#RFC5576)摄像头-2，编码-2 分辨率 |
| a=ssrc:98760 帧速率:15 | [【RFC 5576】](#RFC5576)摄像机-2，编码-2 帧率 |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62539 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54723 典型主机 | [【RFC 5245】](#RFC5245) |

5.8 SDP Answer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| m =视频 63130 RTP/SAVPF 96 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:96 H264/90000 | [【RFC 3984】](#RFC3984) |
| a =远程-ssrc:12345 接收:开 | [【draft-lennox-MMU sic-SDP-source-selection】](#draft-lennox-mmusic-sdp-source-selection)流-1，编码-1 接受 |
| a =远程-ssrc:67890 接收:关闭 | [【RFC 5576】](#RFC5576)流 1，编码 2 不被接受 |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅接收视频流 1 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63130 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56607 典型主机 | [【RFC 5245】](#RFC5245) |
| m =视频 63133 RTP/SAVPF 97 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:97 H264/90000 | [【RFC 3984】](#RFC3984) |
| a =远程-ssrc:54321 接收:关闭 | [[draft-lennox-MMU sic-SDP-source-selection]](#draft-lennox-mmusic-sdp-source-selection)流-2，编码-1 不被接受 |
| a =远程-ssrc:67890 接收:开 | [【draft-lennox-MMU sic-SDP-source-selection】](#draft-lennox-mmusic-sdp-source-selection)流-2，编码-2 接受 |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅接收视频流 2 |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63133 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56609 典型主机 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

# [5.9。](#rfc.section.5.9) [成功 SVC 视频流](#sec.successful-session-svc)

在下面的示例提供/回答交换机制中，Alice 提供了 3 种视频类型的媒体描述以及它们之间的分层解码依赖关系。另一方面，Bob 只接受了 Alice 提供的 3 个操作点中的 2 个。

```
title 2 way SVC Video
note right of Alice
Alice offers 3 sendonly video stream with 3 layers of SVC
end note
Alice->Bob: Offer(Video: H.264)
note left of Bob
Bob accepts Alice's Offer with 2 Operation Points of the 3
offered
end note
Bob->Alice: Answer(Video: H.264)
Alice->Bob: One-Way H.264 Video with encodings per instructed by Bob

```

5.9 SDP Offer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20519 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a =集团:DDP L1 L2 L3 | [【RFC 5583】](#RFC5583)流 L1、L2、L3 按照层解码相关性分组 |
| m =视频 62537 RTP/SAVPF 96 97 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b=AS:90 | [【RFC 4566】](#RFC4566) |
| b =帧速率:15 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:96 H264/90000 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:97 H264/90000 | [【RFC 3984】](#RFC3984) |
| a =中部:L1 | [【RFC 5888】](#RFC5888) |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| m =视频 62539 RTP/SAVPF 98 99 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| base64 | [【RFC 4566】](#RFC4566) |
| b =帧速率:15 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:98 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:99 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a =中部:L2 | [【RFC 5888】](#RFC5888) |
| a =依赖:98 雷 L1:96，97；99 雷·L1:97 | [【RFC 5583】](#RFC5583) |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62539 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54723 典型主机 | [【RFC 5245】](#RFC5245) |
| m =视频 62890 RTP/SAVPF 100 101 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| b=AS:128 | [【RFC 4566】](#RFC4566) |
| b =帧速率:30 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:100 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:101 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a=mid:L3 | [【RFC 5888】](#RFC5888) |
| a =依赖:100 雷 L1:96，97；101 雷 L1:97 L2:99 | [【RFC 5583】](#RFC5583) |
| a =仅发送 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62890 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54723 典型主机 | [【RFC 5245】](#RFC5245) |

5.9 SDP Answer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a =集团:DDP L1 L2 L3 | [【RFC 5583】](#RFC5583)流 L1、L2、L3 按照层解码相关性分组 |
| m =视频 62537 RTP/SAVPF 96 97 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.7711 中 | [【RFC 4566】](#RFC4566) |
| b=AS:90 | [【RFC 4566】](#RFC4566) |
| b =帧速率:15 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:96 H264/90000 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:97 H264/90000 | [【RFC 3984】](#RFC3984) |
| a =中部:L1 | [【RFC 5888】](#RFC5888) |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| m =视频 62539 RTP/SAVPF 98 99 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| base64 | [【RFC 4566】](#RFC4566) |
| b =帧速率:15 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:98 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:99 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a =中部:L2 | [【RFC 5888】](#RFC5888) |
| a =依赖:98 雷 L1:96，97；99 雷·L1:97 | [【RFC 5583】](#RFC5583) |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62539 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54723 典型主机 | [【RFC 5245】](#RFC5245) |
| m =视频 0 RTP/SAVPF 100 101 | [【RFC 4566】](#RFC4566)端口设置为 0 |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| b=AS:128 | [【RFC 4566】](#RFC4566) |
| b =帧速率:30 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:100 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a=rtpmap:101 H264-SVC/90000 | [【RFC 3984】](#RFC3984) |
| a=mid:L3 | [【RFC 5888】](#RFC5888) |
| a =依赖:100 雷 L1:96，97；101 雷 L1:97 L2:99 | [【RFC 5583】](#RFC5583) |
| a =仅接收 | [【RFC 3264】](#RFC3264)-仅发送视频流 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62890 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 54723 典型主机 | [【RFC 5245】](#RFC5245) |

# [6。](#rfc.section.6) WebRTC < - >遗留互操作示例

在本节中，我们试图提供展示 WebRTC 端点和传统 VOIP 端点之间互操作性的会话描述。这里包含的想法并没有完全融入到标准中，本质上可能会有争议。这里的希望是展示一个合理的 SDP 组合，以增强上述通信系统之间的无缝互操作性。

# [6.1。](#rfc.section.6.1) [安全双向音频、视频 w/反馈- WebRTC < - >传统互操作](#successful-2way-webrtc-voip)

在下面描述的场景中，Alice 发送[【RFC 3264】](#RFC3264)Offer，其中包含每种媒体类型的两组媒体描述。

*   一组对应于[【WEBRTC】](#WEBRTC)兼容基于 RTP/SAVPF 的音频和视频描述。
*   另一组是基于 RTP/AVP 的音频和视频描述，用于传统互操作目的。
*   还要注意，Alice 包括会话级 DTLS 信息和媒体级 RTCP 反馈信息，适用于这两组媒体描述

另一方面，Bob 是一个传统的 VOIP 端点，只识别以 RTP/AVP 作为应用协议的媒体描述。会话的安全和反馈要求或者由中间网关处理，或者由 Bob 的能力和中间网关的某种组合来处理。

```
title Successful 2-Way WebRTC <-> VOIP Interop
note right of Alice
Alice is on a WebRTC end-point & Bob is behind a legacy VOIP system
end note
Alice->Bob: Offer(Audio:Opus Video:VP8)
note right of Alice
 Alice includes 2 copies of media descriptions
 1\. WebRTC compliant media description (RTP/SAVPF)
 2\. Legacy compliant media description (RTP/AVP)
end note
Bob->Alice: Answer(Audio:Opus Video:VP8)
note right of Bob
 Bob recognizes"legacy compliant" media description from Alice.
 and accepts the same.
end note
Alice->Bob: Two-way Opus Audio, VP8 Video
note right of Alice
  Session also suports RTP/RTCP Mux, RTCP feedback (nack,pli) 
end note

```

6.1 SDP Offer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=alice 20518 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:074c6550 | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:a28a 397 a4 C3 f 31747 D1 ee 3474 af 08 a 068 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtcp-rsize | [【RFC 5506】](#RFC5506) |
| m =音频 54609 RTP/SAVPF 109 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54609 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54609 典型值 srflx raddr 192 . 168 . 1 . 4 r 端口 54609 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | [【RFC 5104】](#RFC5104) |
| m =视频 62537 RTP/SAVPF 120 | [【RFC 4566】](#RFC4566) |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | [【RFC 5761】](#RFC5761) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62537 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 62537 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 62537 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 54721 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54721 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | [【RFC 5104】](#RFC5104) |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |
| - | 这些媒体描述集用于传统互操作目的 |
| m =音频 54732 RTP/AVP 109 | [【RFC 4566】](#RFC4566)爱丽丝包括 RTP/AVP 音频流描述 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:7f:7d:F9:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | Alice 仍然包括 RTP/RTCP 多路复用器支持 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 54732 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 694302207 24.23.204.141 54732 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54732 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 4 64678 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 64678 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 64678 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:109 nack | 她添加了她对 NACK·RTCP 反馈支持的意向 |
| m =视频 62445 RTP/AVP 120 | [【RFC 4566】](#RFC4566)Alice 包含 RTP/AVP 视频流描述 |
| c=在 IP4 24.23.204.141 | [【RFC 4566】](#RFC4566) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:7d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a=rtcp-mux | 爱丽丝打算执行 RTP/RTCP 多路复用 |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 4 62445 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 24.23.204.141 62537 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 62445 | [【RFC 5245】](#RFC5245) |
| a =候选人:0 2 2113667326 192.168.1.4 54721 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 24.23.204.141 54721 典型 srflx raddr 192 . 168 . 1 . 4 r 端口 54721 | [【RFC 5245】](#RFC5245) |
| a=rtcp-fb:120 nack pli | 爱丽丝表示支持图像丢失指示和 NACK RTCP 反馈 |
| a=rtcp-fb:120 ccm | [【RFC 5104】](#RFC5104) |

6.1 SDP Answer

| SDP 内容 | RFC #/注释 |
| --- | --- |
| v=0 | [【RFC 4566】](#RFC4566) |
| o=bob 16833 0 IN IP4 0.0.0.0 | [【RFC 4566】](#RFC4566) |
| s= | [【RFC 4566】](#RFC4566) |
| t=0 0 | [【RFC 4566】](#RFC4566) |
| a=ice-ufrag:c300d85b | [【RFC 5245】](#RFC5245) |
| a = ice-pwd:4 和 99 BD 291 c 3225921d 5d 47 efbabd 9 至 2 | [【RFC 5245】](#RFC5245) |
| a =指纹:sha-1 99:41:49:83:4a:97:0e:1f:ef:6d:F7:C9:C7:70:9d:1f:66:79:A8:07 | [【RFC 5245】](#RFC5245) |
| m =音频 49203 RTP/AVP 109 | [【RFC 4566】](#RFC4566)鲍勃接受基于 RTP/AVP 的音频流 |
| c=在 IP4 98.248.92.77 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:109 opus/48000 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a =最佳:20 | [【草稿-spit ka-有效载荷-RTP-opus】](#draft-spittka-payload-rtp-opus) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 49203 典型主机 | [【RFC 5245】](#RFC5245) |
| a = ccndidate:1 1 UDP 1694302207 98 . 248 . 92 . 77 49203 Sr LX rad dr 192 . 168 . 1 . 7 r rt 49203 型 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 60065 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 60065 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 60065 | [【RFC 5245】](#RFC5245) |
| m =视频 63130 RTP/SAVP 120 | 鲍勃接受基于 RTP/AVP 的视频流 |
| c=在 IP4 98.248.92.771 中 | [【RFC 4566】](#RFC4566) |
| a=rtpmap:120 VP8/90000 | [【草案-IETF-有效载荷-vp8】](#draft-ietf-payload-vp8) |
| a=sendrecv | [【RFC 3264】](#RFC3264) |
| a =候选:0 1 UDP 2113667327 192 . 168 . 1 . 7 63130 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 1 UDP 1694302207 98.248.92.77 63130 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 63130 | [【RFC 5245】](#RFC5245) |
| a =候选:0 2 UDP 2113667326 192 . 168 . 1 . 7 56607 典型主机 | [【RFC 5245】](#RFC5245) |
| a =候选人:1 2 UDP 1694302206 98.248.92.77 56607 典型 srflx raddr 192 . 168 . 1 . 7 r 端口 56607 | [【RFC 5245】](#RFC5245) |

# 7 .[。](#rfc.section.7) IANA 的注意事项

这份文件不需要 IANA 采取任何行动。

# [8。](#rfc.section.8)更改日志

[RFC 编辑注意:发布时请删除此部分]

对草稿-nandakumar-rtcweb-sdp-00 的更改

*   修正了邮件列表上的编辑评论。
*   基于 draft-ietf-mmusic-sctp-sdp 更新了数据通道 SDP 信息。
*   基于 draft-IETF-m music-SDP-BUNDLE-negotiation 更新了捆绑示例。
*   添加了更多捆绑包变体的示例
*   增加了联播和 SVC 的新示例

# [9。](#rfc.references)参考文献

# 9.1。标准参考文献

| **【RFC 3264】** |  和  ，“[带有会话描述协议(SDP)](http://tools.ietf.org/html/rfc3264) 的提议/回答模型”，RFC 3264，2002 年 6 月。 |
| **【RFC 4566】** |  ， 和 ，“ [SDP:会话描述协议](http://tools.ietf.org/html/rfc4566)”，RFC 4566，2006 年 7 月。 |
| **【RFC 2119】** | [Bradner，S.](mailto:sob@harvard.edu "Harvard University") ，“[RFC 中用于表示要求等级的关键词](http://tools.ietf.org/html/rfc2119)”，BCP 14，RFC 2119，1997 年 3 月。 |

# 9.2。参考资料

| **【RFC 5245】** |  ，“[交互式连接建立(ICE):提供/应答协议](http://tools.ietf.org/html/rfc5245)的网络地址转换器(NAT)遍历协议”，RFC 5245，2006 年 7 月。 |
| **【web TC】** | 、 、【http://dev.w3.org/2011/webrtc/editor/webrtc.html】T4>、。 |
| **【jsep】** |  和  ，“ [Javascript 会话建立协议](http://tools.ietf.org/html/draft-ietf-rtcweb-jsep-01)”，互联网-草案草稿-ietf-rtcweb-jsep-01，2012 年 12 月。 |
| **【RFC 5506】** |  ，“[支持精简实时传输控制协议(RTCP):机遇与后果](http://tools.ietf.org/html/rfc5506)”，RFC 5506，2009 年 4 月。 |
| **【RFC 3551】** |  和 ，“[最小控制的音频和视频会议 RTP 配置文件](http://tools.ietf.org/html/rfc3551)”，RFC 3551，2003 年 7 月。 |
| **【RFC 3952】** |  和 ，“[互联网低比特率编解码器(iLBC)语音的实时传输协议(RTP)有效载荷格式](http://tools.ietf.org/html/rfc3952)”，RFC 3952，2004 年 12 月。 |
| **【RFC 4796】** |  和  ，[会话描述协议(SDP)内容属性](http://tools.ietf.org/html/rfc4796)，RFC 4796，2007 年 2 月。 |
| **【RFC 5761】** |  和 ，“[在单个端口](http://tools.ietf.org/html/rfc5761)上复用 RTP 数据和控制数据包”，RFC 5761，2010 年 4 月。 |
| **【RFC 3556】** |  ，“[用于 RTP 控制协议(RTCP)带宽](http://tools.ietf.org/html/rfc3556)的会话描述协议(SDP)带宽修改器”，RFC 3556，2003 年 7 月。 |
| **【RFC 5104】** |  ，  ，  和  ，[带反馈的 RTP 视听简档中的编解码器控制消息(AVPF)](http://tools.ietf.org/html/rfc5104) ，RFC 5104，2008 年 2 月。 |
| **【RFC 5888】** |  和  ，“【H.264 视频的 RTP 有效载荷格式”，RFC 3984，2010 年 6 月。 |
| **【草稿-spit ka-有效载荷-RTP-opus】** | 斯皮特卡、j .斯皮特卡、T2、沃斯、k .斯皮特卡、T4 和 JM。吕林燕，“[Opus 语音和音频编解码器的 RTP 有效载荷格式](http://tools.ietf.org/html/draft-spittka-payload-rtp-opus-01)”，互联网-草稿草稿-spit ka-有效载荷-rtp-opus-01，2012 年 7 月。 |
| **【草案-IETF-有效载荷-vp8】** |  ， ， ， 和 ，“[VP8 视频的 RTP 有效载荷格式](http://tools.ietf.org/html/draft-ietf-payload-vp8-05)”，互联网-草案草稿-IETF-有效载荷-vp8-05，2012 年 8 月。 |
| **【RFC 3984】** |  ， ， ， 和 ，“[用于 H.264 视频的 RTP 有效载荷格式](http://tools.ietf.org/html/rfc3984)”，RFC 3984，2005 年 2 月。 |
| **【RFC 5583】** |  和  ，“[会话描述协议(SDP)](http://tools.ietf.org/html/rfc5583) 中的信令媒体解码依赖性”，RFC 5583，2009 年 7 月。 |
| **【RFC 5576】** |  ， 和 ，“[会话描述协议(SDP)](http://tools.ietf.org/html/rfc5576) 中的源特定媒体属性”，RFC 5576，2009 年 6 月。 |
| **【草案-IETF-RTC web-数据-信道】** |  ， 和 ，“ [RTCWeb 数据报连接](http://tools.ietf.org/html/draft-ietf-rtcweb-data-channel-01)”，互联网-草案草稿-ietf-rtcweb-data-channel-01，2012 年 9 月。 |
| **【草案-IETF-MMU sic-SCTP-SDP】** |  和  ，“[会话描述协议(SDP)](http://tools.ietf.org/html/draft-ietf-mmusic-sctp-sdp-03) 中基于流控制传输协议(SCTP)的媒体传输”，互联网-草案草稿-ietf-mmusic-sctp-sdp-03，2012 年 9 月。 |
| **【草案-IETF-MMU sic-SDP-捆绑-协商】** |  ， 和 ，“[使用会话描述协议(SDP)端口号的复用协商](http://tools.ietf.org/html/draft-ietf-mmusic-sdp-bundle-negotiation-03)，互联网-草案草稿-IETF-MMU sic-SDP-bundle-Negotiation-03，2013 年 2 月。 |
| **【草稿-lennox-MMU sic-SDP-来源-选择】** |  和  ，“[使用会话描述协议(SDP)端口号的复用协商](http://tools.ietf.org/html/draft-lennox-mmusic-sdp-source-selection-05)”，互联网-草稿草稿-Lennox-MMU sic-SDP-source-selection-05，2012 年 10 月。 |
| **【draft-rescorla-avt core-6222 bis】** |  和  ，“[选择 RTP 控制协议(RTCP)规范名称(CNAMEs)](http://tools.ietf.org/html/draft-rescorla-avtcore-6222bis-00) 指南”，互联网-草案草稿-rescorla-avtcore-6222bis-00，2012 年 10 月。 |

# [作者地址](#rfc.authors)

```
<span>
  <span>Suhas Nandakumar</span> 
  <span>
    <span>Nandakumar</span>
  </span>
</span>
<span>Cisco</span>
<span>
  <span>170 West Tasman Drive</span>

  <span>
    <span>San Jose</span>,  
    <span>CA</span> 
    <span>95134</span>
  </span>
  <span>USA</span>
</span>
<span>EMail: <a href="mailto:snandaku@cisco.com">snandaku@cisco.com</a></span>

<span>
  <span>Cullen Jennings</span> 
  <span>
    <span>Jennings</span>
  </span>
</span>
<span>Cisco</span>
<span>
  <span>170 West Tasman Drive</span>

  <span>
    <span>San Jose</span>,  
    <span>CA</span> 
    <span>95134</span>
  </span>
  <span>USA</span>
</span>
<span>Phone: +1 408 421-9990</span> 
```

Enter fullscreen mode Exit fullscreen mode

邮箱:【fluffy@cisco.com】T2