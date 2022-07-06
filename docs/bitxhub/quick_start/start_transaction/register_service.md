# 2. 注册服务

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
