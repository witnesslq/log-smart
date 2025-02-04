# 小象用户行为分析
#### 介绍
大数据技术已经迅速应用于商业并产生价值，通过数据分析来识别用户行为，建立以用户为中心的低成本快速增长，是一个企业必须具备的核心竞争力。
随着成本的大幅度增加，企业必须改变过去粗放型的营销和运营方式，特别是在市场营销、产品制造、销售以及未来的客户服务等各个方面向更加科学、高效的方向转变。

小象电商系统上线后，需要收集用户行为数据，通过大数据实时分析实现电商业务数字化运营。基于此强需求开发小象用户行为分析，兼容神策开源的埋点SDK完成终端行为上报，采用Nginx+Flume+kafka实现日志收集，采用Flink写入HDFS。

本开源项目内容包括nginx环境配置、Flume解密和日志格式处理、将明文数据存放到kafka的Topic下、Flink消费后将埋点数据存入HDFS的关键4步操作。为方便前期埋点的校验调优，在kafka环节，增加了埋点解析数据JSON格式存入MySQL。后续计划增加友盟和其他SDK厂商的埋点处理，以及业务系统日志的采集入库。

#### 项目主要内容
- 日志采集（Flume+kafka）
- 日志入库（Flink+HDFS）

#### 加微信获取演示环境  请注明：github 小象用户行为分析
- 微信：mzv988
- ![输入图片说明](https://images.gitee.com/uploads/images/2021/0201/105231_685d973a_5325125.png "xiaoxiangopen.png")

#### 工作流程
完成数据采集技术构建和业务设计，在App、小程序的系统供应商配合下完成用户行为数据采集埋点，并基于埋点的数据构建线上用户行为标签和画像。
 ![输入图片说明](https://images.gitee.com/uploads/images/2021/0504/115612_bbd97789_5325125.png "屏幕截图.png")

#### 架构设计思路
所谓“埋点”，是数据采集领域（尤其是用户行为数据采集领域）的术语，指的是针对特定用户行为或事件进行捕获、处理和发送的相关技术及其实施过程。比如用户某个icon点击次数、观看某个视频的时长等等。
![输入图片说明](https://images.gitee.com/uploads/images/2021/0504/115703_4013e7fa_5325125.png "屏幕截图.png") 

#### 业务设计思路
埋点业务设计，首先需要根据业务分析明确采集的目标行为，进一步搞清楚应该在哪些地方埋什么样的点。过程中建议使用“事件模型（ Event 模型）”来描述用户的各种行为，事件模型包括事件（ Event ）和用户（ User ）两个核心实体。
基于4W1H模型描述用户行为可将整个行为描述清楚，要点包括：是谁、什么时间、什么地点、以什么方式、干了什么。通过这两个实体结合在一起就可以清晰地描述清楚用户行为。
![输入图片说明](https://images.gitee.com/uploads/images/2021/0504/115753_e474aeec_5325125.png "屏幕截图.png")
 
#### 技术架构
SDK埋点采集行为数据来源终端包括iOS、安卓、Web、H5、微信小程序等。不同终端SDK采用对应平台和主流语言的SDK，埋点采集到的数据通过JSON数据以HTTP POST方式提交到服务端API。
服务端API由数据接入系统组成，采用Nginx来接收通过 API 发送的数据，并且将之写到日志文件上。使用Nginx实现高可靠性与高可扩展性。
对于Nginx打印到文件的日志，会由Flume的 Source 模块来实时读取Nginx日志，并由Channel模块进行数据处理，最终通过Sink模块将处理结果发布到 Kafka中。

![输入图片说明](https://images.gitee.com/uploads/images/2021/0504/120743_443b0e95_5325125.png "屏幕截图.png")

#### 软件完整架构
![输入图片说明](https://images.gitee.com/uploads/images/2021/0427/092847_e6c637d8_5325125.png "屏幕截图.png")

#### 第三方埋点SDK集成步骤
- 引入SDK：在终端应用配置文件添加 SDK 依赖，不同终端引入方式会有差异，具体操作步骤将在后续SDK技术文档中体现。
- 配置上报服务端API地址：用于设置SDK上报API的服务端地址。
- 开启全埋点：SDK 可以自动采集一些用户行为，如 App 启动、退出、浏览页面、控件点击。初始化 SDK 时，通过SDK提供的初始化方法可以配置开启全埋点。

#### API接入服务设计
不同渠道的埋点数据通过 HTTP API 发送给服务端API实现数据接入。
采用Nginx作为WEB容器接收客户端SDK发送的数据，并且将之写到日志文件上。使用 Nginx 主要是考虑到其高并发、高可靠性与高可扩展性。 

#### 用户行为采集场景
通过应用场景梳理，实现以场景规划埋点，用场景检验埋点。场景梳理可以抽象为三个层面：
- 通用基础场景：共性操作统一考虑
- 重要操作场景：重要操作整体归因
- 业务主流程场景：以业务线定义完整过程

![输入图片说明](https://images.gitee.com/uploads/images/2021/0504/121549_e747bcc1_5325125.png "屏幕截图.png")

#### 应用效果
![输入图片说明](https://images.gitee.com/uploads/images/2021/0504/123053_dbdac86e_5325125.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2021/0504/123137_fa582c65_5325125.png "屏幕截图.png")


#### 版权声明
小象数据行为分析 开源版遵循 GPL-3.0 开源协议发布，并提供免费使用，但 **绝不允许修改后和衍生的代码做为闭源的商业软件发布和销售 ！**

#### 捐赠支持
如果您觉得我们的开源项目 小象数据行为分析 对您有帮助，那就请项目开发者们来一杯喜茶吧！当前我们接受来自于微信、支付宝或者码云的捐赠，请在捐赠时备注自己的昵称或附言。

您的捐赠将用于支付该项目的一些费用支出，并激励开发者们以便更好的推动项目的发展，同时欢迎捐赠公网服务器用于提高在线演示系统体验。
|![输入图片说明](https://images.gitee.com/uploads/images/2021/0508/105255_19c5596f_5325125.png "屏幕截图.png")| ![输入图片说明](https://images.gitee.com/uploads/images/2021/0508/124019_cdd95732_5325125.png "屏幕截图.png")|
|---|---|

#### 长期捐赠
如果您是企业的经营者并且有计划将 小象数据行为分析 用在公司的经营产品中，欢迎进行长期捐赠。长期捐赠有商业上的益处有：

- 积极响应，快速维护，及时更新；
- 企业名称、Logo 及官网链接将长期展示在开源仓库、小象智慧 官网及宣发材料中；
- 捐赠金额同比例抵扣未来 小象数据行为分析 的付费产品价格。

如果您对长期赞助 小象智慧 团队感兴趣，或者有其他好想法，欢迎联系开发团队微信 mzv988，或发送邮件到 gitee@xiaoxiangai.com。

