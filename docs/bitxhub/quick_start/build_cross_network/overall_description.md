# 整体说明

这篇文档是对BitXHub跨链系统部署的整体说明，主要是介绍BitXHub跨链系统的一般部署流程和部署架构。

## 1. 环境准备

环境准备是部署和使用BitXHub跨链平台的第一步，需准备以下环境：

- 满足配置要求的硬件设施
- 操作系统
- 软件依赖：
    - Go环境
    - Docker
    - 已部署的应用链（业务所在的区块链，以下简称应用链）

具体信息请查看[环境准备](./env)文档。

## 2. 单中继链部署架构

一般来说，单中继链架构适用于大多数部署场景，建议您使用此种部署架构来体验BitXHub跨链系统。如下图所示，部署完bitxhub节点集群（也可以是solo模式的单机节点），两条或多条应用链上部署好跨链合约，然后通过各自的跨链网关接入到中继链中，完成跨链系统的搭建。

![!](../../../assets/single_bitxhub.png)

在明晰了单中继架构之后，这里再说明下部署的一般流程：

1. **[中继链部署](../single_bitxhub/deploy_bitxhub)**：BitXHub中继链是搭建跨链系统的基础；
2. **[应用链跨链合约部署](../single_bitxhub/deploy_broker)**：在应用链上部署broker跨链合约，用于与跨链网关对接；
3. **[跨链网关部署](../single_bitxhub/deploy_pier)**：Pier网关是连接应用链与中继链的必要组件；
4. **[注册应用链](../single_bitxhub/register_appchain)**：通过Pier网关将应用链注册到中继链，这一步将应用链与中继链成功对接。

![!](../../../assets/deploy_flow.png)

## 3. 跨链网关直连部署架构

跨链网关直连部署架构是指不使用中继链，两方的应用链通过跨链网关与对方直接连接，部署结构如下图所示：

![!](../../../assets/direct_pier.png)

除了无需部署中继链节点之外，部署的流程与上一章基本一致，具体的部署流程如下：

1. **[应用链跨链合约部署](../direct_mode/deploy_broker)**；
2. **[跨链网关部署](../direct_mode/pier_direct_mode_deploy)**；
3. **[注册应用链](../direct_mode/register_appchain)**。
