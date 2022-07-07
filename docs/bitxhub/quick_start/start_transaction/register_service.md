# 2. 注册服务

## 中继模式

通过pier命令行，将所部署业务合约对应的服务注册到BitXHub上。接下来，将以注册Ethereum上的服务为例，进行介绍：

1. Pier命令行进行服务注册

    ```shell
    # service-id 为业务合约地址
    pier --repo $CONFIG_PATH/.pier appchain service register --appchain-id ethappchain1 --service-id "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11" --name "ethServer1" --intro "" --type CallContract --permit "" --details "test" --reason "reason"
    # 发起注册服务后会打印应用链服务id与提案id
    Register appchain service for ethappchain1:0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 successfully, wait for proposal 0xcb33b10104cd217aAB4b302e9BbeEd1957EDaA31-1 to finish.
    ```

2. 中继链节点依次为应用链服务提案投票

    ```shell
    # 用上一步得到的提案id进行投票
    bitxhub --repo $CONFIG_PATH/bitxhub/scripts/build/node1 client governance vote --id 0xcb33b10104cd217aAB4b302e9BbeEd1957EDaA31-1 --info approve --reason approve
    # 当超过半数bitxhub集群节点投票后，该应用链服务注册成功。
    ```

## 直连模式

**注意！直连模式应用链注册和服务注册在1.18版本及以后，都是通过合约的方式进行管理**。

同时需要注册对方应用链的服务信息，其具体参数如下：

- `chainID`：与部署broker合约的appchainID一致；
- `serviceID`：业务合约地址；
- `whiteList`：黑名单，在黑名单内的服务无法调用该服务。

**举例说明：**

![!](../../../../assets/direct5.png)

通过`getLocalServiceList()`与`getRemoteServiceList()`可以查询到自己与对方应用链的serviceID。
