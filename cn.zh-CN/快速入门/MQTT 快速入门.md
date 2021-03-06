# MQTT 快速入门 {#concept_44867_zh .concept}

作为使用和熟悉产品功能的入门指导，本文提供如何快速使用微消息队列 MQTT 通过默认支持的 MQTT 协议收发消息的步骤。

对于非 MQTT 协议的接入，比如新能源、808 协议，您必须首先购买微消息队列 MQTT 企业铂金版，并通过专属通道享受对应的文档和技术支持服务。

## 背景信息 {#section_a08_wel_m9l .section}

微消息队列 MQTT 需搭配后端消息存储队列一起使用，具体说明如下：

-   微消息队列 MQTT 实例是一个无状态网关类型实例，用来完成 IoT 移动场景客户端连接维持和消息转发，本身并不提供消息数据持久化功能，具体的消息存储和消息数据持久化还需要配置一个消息存储实例。
-   现阶段要求每个微消息队列 MQTT 实例（网关实例）必须绑定一个存储实例（消息队列 MQ 实例）使用，后续会开放非持久化使用模式（直推模式，消息不持久化）。
-   消息存储实例目前只支持消息队列 MQ 类型的后端存储实例，后续会支持消息队列 Kafka 和消息队列 AMQP（RabbitMQ）等其他类型的存储实例。
-   目前您在同一个地域（Region）可以创建有限数量的微消息队列 MQTT 实例，且一个微消息队列 MQTT 实例仅可绑定一个相应的消息队列 MQ 实例。同一地域的实例数量上限参考控制台具体提示。

在使用微消息队列 MQTT 时，请注意以下网络访问限制：

只有在同一个地域下的同一个实例中的 Topic 和 Group ID 才能互通，例如，某 Topic 创建在**华北2（北京）**地域下的实例 A 中，那么该 Topic 只能被在**华北2（北京）**地域下的实例 A 中创建的 Group ID 对应的微消息队列 MQTT 客户端（下文简称客户端）访问。

## 流程 {#section_m4x_trp_hhb .section}

[图 1](#fig_dsf_ny4_hhb) 展示了如何使用微消息队列 MQTT 收发消息的流程。

 ![快速入门流程图](images/43300_zh-CN.png "快速入门流程")

如[图 1](#fig_dsf_ny4_hhb) 所示，使用客户端收发消息需要先创建相应资源，否则微消息队列 MQTT 服务器会拒绝非法的 Client ID 的连接。

## 前提条件 {#section_mxv_grp_hhb .section}

-   您已经开通母产品消息队列 MQ 的服务。如未开通，请先[开通](https://common-buy.aliyun.com/?spm=5176.ons.0.0.514e6c5c76HC2o&commodityCode=ons#/open)该服务。

-   您已经拥有了阿里云 AccessKey（AK）。详情请参见[创建AccessKey](../../../../../intl.zh-CN/通用参考/创建AccessKey.md#)。


## 步骤一：创建资源 {#section_kx4_3rp_hhb .section}

这里所说的资源包括：

-   微消息队列 MQTT 实例（用于客户端连接维持和消息转发）
-   消息存储实例（用于消息的存储，目前只支持消息队列 MQ 实例）
-   Topic（用于消息发送和订阅的消息一级主题，即父级 Topic）
-   Group ID（用于客户端识别）

1.  **选择地域**。

    请根据您的业务需求确定需要把资源创建在哪个地域。

    1.  登录[消息队列 MQ 控制台](http://ons.console.aliyun.com)。

    2.  在控制台左上角的产品名称处，单击其右边的下拉箭头，将默认显示的**消息队列 MQ** 切换为**微消息队列 MQTT**。

![切换产品](images/43307_zh-CN.png "切换产品")

    3.  在顶部导航栏选择地域（比如**华北2（北京）**地域），即您要将资源创建在哪个地域。

2.  **创建微消息队列 MQTT 实例**。

    首先，您需要创建微消息队列 MQTT 实例。创建前，请注意以下几点：

    -   您在每个地域下，所有类型的实例有总数量的限制，具体限制参考控制台的提示。

    -   请根据业务场景估算 TPS、连接数和订阅关系，选择合理的规格。对于预付费类型（包年包月）的实例，如果选择过小的规格会触发服务限流影响业务；后付费类型（按量付费）的实例默认也会有一个阈值，如果超过了阈值则需要提工单报备进行调配，具体阈值可以参考实例的默认报警水位。

    -   购买的基础版实例实时生效；购买的企业铂金版实例需要时间部署，实例可运行时会通知您。

    请按以下步骤创建微消息队列 MQTT 实例：

    1.  在左侧导航栏选择**概览**。

    2.  在实例列表页面右上角，单击**新建实例**。

    3.  在**新建实例**页面，按需选择微消息队列 MQTT 实例版本，跳转至购买页面后，按需选择相应的配置，然后根据页面提示完成购买。

    回到控制台**概览**页面的实例列表，您可以看到已购买（创建）的微消息队列 MQTT 实例。

3.  **创建并绑定数据存储实例**。

    创建完微消息队列 MQTT 实例后，您还需要创建用于存储 Topic 和消息的实例（目前仅支持消息队列 MQ 的实例），然后将创建的消息队列 MQ 的实例与微消息队列 MQTT 实例进行一对一的绑定。

    绑定关系有以下限制：

    -   一个微消息队列 MQTT 实例仅允许绑定一次，绑定成功后即不允许更改。

    -   一个存储型实例仅允许绑定到一个微消息队列 MQTT 实例，不允许一对多绑定。

    -   绑定的这两个实例，命名空间类型必须一致，即独享命名空间的实例不允许和非独享命名空间的实例建立绑定关系。

    -   如果提前删除微消息队列 MQTT 实例绑定的存储实例，则微消息队列 MQTT 实例会不可用。

    请按以下步骤创建并绑定存储实例：

    1.  在左侧导航栏选择**实例管理**。在默认显示的**实例管理**页签，选择您刚创建的微消息队列 MQTT 实例。
    2.  在**第二步：消息存储配置**区域，单击**消息队列 MQ \>\>** 框。

        ![绑定实例](images/47584_zh-CN.png "绑定实例")

    3.  在**消息存储配置**对话框，按实例情况和需求选择对应选项。
        -   如果您已购买消息队列 MQ 实例，请选择**选择已有实例**。选择该选项后显示的是您已创建（购买）的消息存储实例列表。然后单击您已有的消息队列 MQ 消息存储实例，再单击**确认**完成绑定。

            ![选择已有实例](images/47580_zh-CN.png "选择已有实例")

        -   如果您还未购买消息队列 MQ 的实例，请选择以下选项：

            -   **新建共享实例**：创建消息队列 MQ 标准版实例。请输入实例名和描述，然后单击**确认**，完成创建。

                ![新建共享实例](images/47581_zh-CN.png "新建共享实例")

            -   **购买铂金版实例**：创建消息队列 MQ 铂金版实例。单击**立即购买消息队列 MQ 铂金版**，然后按照页面提示完成购买（即创建）。

                ![新建铂金版实例](images/47582_zh-CN.png "新建铂金版实例")

            完成创建后，重复[步骤 i](#li_6va_ldc_986) 和[步骤 ii](#li_6yp_yg4_jom)，并选择**选择已有实例**，然后单击刚刚创建的消息队列 MQ 实例，再单击**确认**完成绑定。

4.  **创建 Topic**。

    使用 MQTT 协议收发消息，需要创建 MQTT 的父级 Topic（Parent Topic）。多级子 Topic 无需创建，直接在代码中使用即可。

    微消息队列 MQTT 实例和存储实例之间会建立一一绑定关系，因此，创建的 Topic 其实是创建到存储实例上，在微消息队列 MQTT 控制台仅仅做一层映射关系，所有的 Topic 操作都可以同时使用存储实例的操作习惯完成。

    如果之前在使用消息队列 MQ 时已经创建过 Topic，也可以直接使用。如果没有创建过，请按照以下步骤进行创建：

    1.  在左侧导航栏选择**消息存储**。

    2.  在**消息存储**页面，选择您刚创建的微消息队列 MQTT 实例，单击**新建 Topic**。

![创建 Topic](images/43326_zh-CN.png "创建 Topic")

    3.  在**创建 Topic** 对话框，输入 Topic 名称、选择该 Topic 用于存储和收发的消息类型、输入备注信息，然后单击**确认**。

    **说明：** 如果需要使用微消息队列 MQTT 客户端发送顺序消息，则此处需要选用顺序 Topic，但微消息队列 MQTT 客户端的消费场景暂时还不支持强顺序。

5.  **创建 Group ID**。

    微消息队列 MQTT 的 Group ID 用于指定一组逻辑功能完全一致的节点共用的组名，代表一类相同功能的设备。Group ID 和 Device ID 共同组成用于识别 MQTT 客户端的 Client ID。更多信息请参见[名词解释](../intl.zh-CN/产品简介/名词解释.md#)。

    1.  在左侧导航栏选择 **Group 管理**。

    2.  在 **Group 管理**页面，选择您刚创建的微消息队列 MQTT 实例，单击**新建 Group ID**。

        ![创建 Group ID](images/43327_zh-CN.png "创建 Group ID")

    3.  在**创建 Group ID**对话框中，输入需要创建的 Group ID，然后单击**确定**。

    创建完成后，在 **Group 管理**页面中可以看到创建的 Group ID。该页面显示当前地域下您拥有的所有 Group ID。

    **说明：** 

    -   如果 Group ID 不再使用，请及时删除。

    -   Group ID 仅限创建账号使用，主账号创建的 Group ID 子账号无法使用，子账号的 Group ID 必须单独创建。


## 步骤二：获取接入点 {#section_v2z_lbd_c42 .section}

在使用 SDK 收发消息时需要填写微消息队列 MQTT 实例接入点。微消息队列 MQTT 实例的接入点由“接入点域名 + 端口”组成。

在微消息队列 MQTT 实例和消息队列 MQ 实例被成功绑定后，接入点信息便立即显示在**获取接入点信息**区域，您可直接获取该接入点信息。

成功绑定微消息队列 MQTT 实例和消息队列 MQ 实例后，也可按照以下步骤获取接入点：

1.  在控制台顶部导航栏选择刚创建的资源所在地域，然后在左侧导航栏选择**实例管理**。

2.  在默认显示的**实例管理**页面，选择您创建的微消息队列 MQTT 实例名称，单击**实例信息**页签。

3.  在**实例信息**页签的**获取接入点信息**区域，查看所需的接入点域名。

![获取接入点](images/43316_zh-CN.png "获取接入点")


微消息队列 MQTT 同时提供了**公网接入点**和**内网接入点**。在物联网和移动互联网的场景中，客户端推荐使用公网接入点接入；内网接入点仅供一些特殊场景使用。因为一般而言，涉及部署在云端服务器上的应用的场景，建议使用服务端消息产品例如消息队列 MQ 实现。

**说明：** 客户端使用接入点连接服务时务必使用域名接入，不得直接使用域名背后的 IP 地址直接连接，因为 IP 地址随时会变化。由于直接使用 IP 地址导致的服务中断，产品方不负责。

**端口**

目前微消息队列 MQTT 除了支持标准的 MQTT on TCP 协议，还支持 MQTT SSL、WebSocket、WebScoket SSL/TLS、Flash。对应的服务端口如[表 1](#table_vr8_2u1_b68) 所示，请根据实际需求修改。

|标准协议端口|SSL 端口|WebSocket 端口|WebSocket SSL/TLS 端口|Flash 端口|
|------|------|------------|--------------------|--------|
|1883|8883|80|443|843|

## 步骤三：调用 SDK 发送和订阅消息 {#section_rsi_ewa_e31 .section}

1.  下载客户端的 SDK。关于各语言 SDK 的下载地址，请参见 [SDK 下载](../intl.zh-CN/SDK 参考/SDK 下载.md#)。

    由于微消息队列 MQTT 默认支持的是标准的 MQTT 协议，因此客户端 SDK 都是推荐开源的第三方 SDK，如果有其他语言没有覆盖，可以自行搜索 MQTT 兼容的 SDK 测试。

2.  下载 Demo 工程，然后运行 Demo 进行消息发送和订阅。关于 Demo 下载地址，请参见 [Demo 工程](../intl.zh-CN/SDK 参考/Demo 工程.md#)。

    目前的 Demo 程序库只覆盖了一部分主流语言，后续会陆续更新。如果没有覆盖对应的开发语言，可以参考 Java 语言的 Demo 进行修改。Demo 工程仅提供一个基本的功能展示，具体应用到线上环境，所有参数务必进行修改。


## 更多信息 {#section_c7u_uuk_ulw .section}

**通过控制台发送消息**

除了通过调用 SDK/API 发送消息，您可以在控制台发送消息来快速验证 Topic 的可用性，具体操作步骤如下：

1.  在控制台左侧导航栏，选择**消息存储**。

2.  在**消息存储**页面的 **Topic 列表**，找到您刚刚创建的 Topic，单击右侧**操作**列的**发送**。

![发送消息](images/43328_zh-CN.png "发送消息")

3.  在**发送消息**对话框中设置消息属性并输入消息内容，然后单击**确定**。

    控制台会返回消息发送成功通知和相应的 Message ID。

     ![消息发送成功](images/43329_zh-CN.png "发送成功")


