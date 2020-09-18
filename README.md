# --openVPNtest
个人测试代码
2020-08-20 testVPN 框架 完成testVPN中关于vpn配置的若干操作。

2020-08-21 testVPN 测试用APP：MakePost 完成名为MakePost的测试用APP，主要用于简单化网络请求发送，有利于后续的功能测试工作。

2020-08-23 testVPN 服务代码完成 完成testVPN 中服务性质代码的编写。 testVPNServices中的util.swift、Tunnel.swift、ClientTunnel.swift、Connection.swift util.swift是公用代码，内涵普遍用到的工具类、工具方法等 Tunnel.swfit是隧道基类，定义了最为基础的隧道 ClientTunnel.swift是对客户端隧道的定义 Connection.swift是对连接的定义

2020-08-24 晚 开始着手packetTunnelProvider类的实现。 实现完成，下一步应该对VPN链接进行测试，完成：

搞清楚整个VPN流量流程 2 确定实现过程有没有失误 3 为下一步APP Proxy实现做铺垫

2020-08-25 着手VPN链接测试

测试过程中发现链接被拒绝，尝试重写tunnel_server程序 2 经检查，问题应该出现在ClientTunnel.swift和util.swift中。 3 完成了针对MakePost的PerAppProxy测试配置，下一步应该是针对数据流流程的分析。包括如何封包解包，如何进行数据处理等等。

2020-08-27 数据流分析阶段

首先规整整个项目代码，各个功能按钮分工明确，debug输出清晰 2 debug信息输出梳理，调整tag，调整debug输出内容，调整debug信息主要面向数据流处理过程。 3 下一步，记录网络流日志。确定本机ip，确定服务端代码处理过程。如果需要在服务器端进行抓包，如何进行。

2020-08-28 服务器数据流分析阶段

1 DEBUG 服务端完成

2020-08-30

bundle id 获取，采用js爬取app store存储到数据库，采用php进行数据库读取展示。 Todo: log流量，内容：时间、长度（包含头不包含头）、源ip-port、目的ip-port、应用名称 2. 服务端代码翻译为C

2020-08-31

log流量，内容：时间、长度（包含头不包含头）、源ip-port、目的ip-port、应用名称 倾向使用CoreData(SQLite数据库实现)进行数据存储。 表：data_flow_log。 列：data_flow_id, src_ip, src_port, dst_ip, dst_port, length, time, protocol。 ### 关于CoreData使用： 添加CoreData 使用CoreData 使用CoreData TODO: Server段代码分析不明了，再议 没有搞清Server是怎么将流量转发出去的，也不知道log应该在什么地方log

2020-09-02 Question:

如何确定源/目的IP和PORT，如何优雅的确定包长度? 服务端如何进行转发？

Answer: > NEAppTCPFlow.remoteEndpoint, 但是UDPFlow又当如何？ > 见尾部

2020-09-03

发现CoreData使用场景与我的需求不符，考虑直接使用SQLite SQLite使用

2020-09-04

iOS data sharing 数据库相关 利用了shared container来做日志存放。 但是如何查看相应的sqlite3文件？/private下应该看不了 要解析Endpoint 这个解析起来非常简单 要解析本机地址 可以自备服务器，请求服务器确定ip地址，同时在请求过程中上报自己的信息。 要解析数据包来源端口 unknown for the present time 要解析数据包的协议格式

TODO: > 1. TCP flow的localEndpoint，UDP flow的remoteEndpoint > 2. flow 的协议确定 > 3. 本机外网地址确定

2020-09-05

通过http://192.168.43.137/test/checkin/checkin.php完成了信息提交和ip确定，应该考虑在日后的实现中定期上传信息，定期确认ip，但是如何调用函数，如何定期上传，如何确定系统全局ip变量仍待考虑。 packetTunnel似乎有每一个packet的协议。 利用completionHandler解决了http请求返回值的问题 完成向log中插入本机ip

packetTunnel readPacketResult返回两个参数，packetData和packetProtocolNumber，通过packetProtocolNumber可以获取相应的协议，但是appProxy和packetTunnel具体是什么关系？Plus, readPacket是在Connection上进行的 在NWTCPConnection上提供远端地址和本地地址的获取。 TCP 本地ip地址也许还有点用，但是port地址似乎没什么用？但是还是完成了记录 iOS接口查询

TODO: 1. 确定流量协议 2. 确定UDPFlow的目标地址ip-port 3. 初步考虑UI实现 4. 记录需要优化的部分，主要是代码的模块化以及各模块之间的设计。

2020-09-06

确定UDP远端ip、port信息获取方式。看函数里就有了。不过暂时没有对UDP流量log进行记录。

2020-09-07

app proxy 只能够通过configuration profile进行配置，里面的ip:port是写死了的，但是可以考虑在load 配置的时候根据vpn的ip:port对配置进行修改？

2020-09-13

已经将全部项目移植到testVPNUIDesign中，下一步做日志可视化。 日志可视化已经完成，下一步考虑本机已装app发现功能。 已装app检测比较有难度，无法通过代码在客户端进行，但似乎通过MDM可以进行，但MDM配置起来似乎比较麻烦。

