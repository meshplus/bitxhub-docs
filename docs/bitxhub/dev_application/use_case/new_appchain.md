# 接入应用链

如果您需要参与到跨链网络之中，与其它应用链进行跨链交易，需接入自己的应用链。

在接入之前，您还需要准备应用链插件和跨链合约，目前已有以下5种应用链的插件和合约：

- [Fabric](https://github.com/meshplus/pier-client-fabric)
- [Ethereum](https://github.com/meshplus/pier-client-ethereum)
- BCOS(商业版)
- [CITA](https://github.com/meshplus/pier-client-cita)
- Hyperchain(商业版)

另外，您也可以开发适配其它种类应用链的插件和合约，具体参考[应用链插件编写](../../../dev/plugin)和[应用链跨链合约编写](../../../dev/cross_contract)。

当应用链插件和跨链合约准备好之后，可参照以下步骤进行应用链接入：

1. 部署broker跨链合约，用于与Pier跨链网关对接，具体步骤可以参考[应用链跨链合约部署](../../../quick_start/build_cross_network/single_bitxhub/deploy_broker/)。
2. 部署Pier跨链网关，用于与BitXHub中继链对接，具体步骤可以参考[跨链网关部署](../../../quick_start/build_cross_network/single_bitxhub/deploy_pier/)。
3. 注册应用链，通过Pier向BitXHub注册应用链信息，具体步骤可以参考[注册应用链](../../../quick_start/build_cross_network/single_bitxhub/register_appchain/)。

至此，应用链就算成功接入了。当然，此时还不能发起跨链交易，您可以参考[开始第一笔跨链交易](../../../quick_start/start_transaction/usage_description)进行下一步。
