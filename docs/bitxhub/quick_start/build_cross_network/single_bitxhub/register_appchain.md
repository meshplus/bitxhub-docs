# 4. 注册应用链

需要注意的是：

- 在v1.6.0及以上的版本，注册应用链需要中继链BitXHub节点管理员进行投票，投票通过之后才能接入。这一步Ethereum和Fabric（包括其它类型应用链）的流程一样，只是注册信息有所不同；
- 在v1.18.0及以上的版本内置了一些验证，使用这些验证规则不需要进行额外的部署。

以下操作均是Pier命令行发起。

## 注册应用链

=== "Ethereum"

    ```shell
    #在进行应用链注册之前首先需要向目标地址进行转账，保障该地址可以进行应用链注册
    #获得admin的id
    pier --repo ~/bitxhub-v2.0.0/.pier/ id
    #向目标地址转账
    bitxhub client transfer --key $HOME/bitxhub/scripts/build/node1/key.json --to 0xFbB01EcDFFc6033e2aa205987696C3fd72f436B2 --amount 100000000000000000
    ```

    1. Pier命令行发起应用链注册

    ```shell
    # 以用户目录下的pier为例
    #注意：命令中的broker地址需要更改为remix编译部署的broker地址，以及admin需要修改为
    #之前转账的地址
    pier --repo $CONFIG_PATH/.pier appchain register --appchain-id "ethappchain" --name "ethereum" \
    --type "ETH" --trustroot "ethereum/ether.validators" --broker \
     0x857133c5C69e6Ce66F7AD46F200B9B3573e77582  --desc "desc" --master-rule \
     "0x00000000000000000000000000000000000000a2" --rule-url "http://github.com" \
     --admin 0xcb33b10104cd217aAB4b302e9BbeEd1957EDaA31 --reason "reason"
    # 0x00000000000000000000000000000000000000a2 HappyRuleAddr
    # 发起注册后会打印出提案id
    Register appchain successfully, wait for proposal 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-0 to finish.
    ```

    ！！！需要注意的是v1.18.0以上版本的网关注册时会将提供的验证规则地址注册为主验证规则，同时也支持配置多个应用链管理员。

    2.中继链节点依次投票

    ```shell
    # 进入bitxhub节点的安装目录，用上一步得到的提案id进行投票
    bitxhub --repo $CONFIG_PATH/bitxhub/scripts/build/node1 client governance vote --id 0xcb33b10104cd217aAB4b302e9BbeEd1957EDaA31-0 --info approve --reason approve
    # 投票完后会打印：vote successfully!
    # 如果是多个bitxhub节点组成的集群，依次指定各节点的安装目录进行投票
    ```

    **当BitXHub集群超过半数的管理员投票通过后，应用链注册成功（如果BitXHub是solo模式，则只需要一票同意即可）**，可以通过如下命令查询提案状态：

    ```shell
    bitxhub --repo  $HOME/bitxhub/scripts/build/node1 client governance appchain info --name "ethereum"
    ```

=== "Fabric"

    ```shell
    #在进行应用链注册之前首先需要向目标地址进行转账，保障该地址可以进行应用链注册
    #获得admin的id
    pier --repo ~/bitxhub-v2.0.0/.pier/ id
    #向目标地址转账
    bitxhub client transfer --key $HOME/bitxhub/scripts/build/node1/key.json --to 0xFbB01EcDFFc6033e2aa205987696C3fd72f436B2 --amount 100000000000000000
    ```
   
    1. Pier命令行发起应用链注册

    ```shell
    # 以用户目录下的pier为例
    #注意：admin需要修改为之前转账的地址
    pier --repo $CONFIG_PATH/.pier appchain register --appchain-id "fabappchain" \
    --name "fabric" --type "Fabric V1.4.3" --trustroot fabric/fabric.validators --broker-cid mychanncel \
    --broker-ccid broker --broker-v 1 --desc "desc" --master-rule "0x00000000000000000000000000000000000000a2" \
    --rule-url "http://github.com" --admin 0x321E49c58eC76a5cA02F3E801f303D1E8E0379d0 --reason "reason" 

    # 0x00000000000000000000000000000000000000a2 HappyRuleAddr
    # 发起注册后会打印出提案id
    Register appchain successfully, wait for proposal 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-0 to finish.
    ```

    ！！！需要注意的是v1.18.0以上版本的网关注册时会将提供的验证规则地址注册为主验证规则，同时也支持配置多个应用链管理员。

    2.中继链节点依次投票

    ```shell
    # 进入bitxhub节点的安装目录，用上一步得到的提案id进行投票
    bitxhub --repo $CONFIG_PATH/bitxhub/scripts/build/node1 client governance vote --id 0xcb33b10104cd217aAB4b302e9BbeEd1957EDaA31-0 --info approve --reason approve
    # 投票完后会打印：vote successfully!
    # 如果是多个bitxhub节点组成的集群，依次指定各节点的安装目录进行投票
    ```

    **当BitXHub集群超过半数的管理员投票通过后，应用链注册成功（如果BitXHub是solo模式，则只需要一票同意即可）**，可以通过如下命令查询提案状态：

    ```shell
    bitxhub --repo  $HOME/bitxhub/scripts/build/node1 client governance appchain info --name "fabric"
    ```

    

## 注册验证规则（可选）

应用链除了在注册应用链时，绑定主验证规则，在可用状态下也可以注册其他验证规则，即可以在应用链注册成功且中继链投票通过后进行规则部署。之前已经准备好了验证规则文件，接下来在Pier端发起部署验证规则的命令。

=== "Ethereum"

    ```shell
    # 以用户目录下的pier为例
    $ pier --repo $CONFIG_PATH/.pier rule deploy --path=$CONFIG_PATH/.pier/ether/validating.wasm
    # 部署验证规则后会打印：Deploy rule successfully: 0xed97cBf32C16551604f0017f9aA776bF96809b87

    $ pier --repo $CONFIG_PATH/.pier rule register --appchain-id ethappchain --rule \
    0xed97cBf32C16551604f0017f9aA776bF96809b87 --rule-url "http://github.com"
    # 注册成功后会打印: Register rule to bitxhub for appchain ethappchain successfully.
    ```
=== "Fabric"

    ```shell
    # 以用户目录下的pier为例
    $ pier --repo $CONFIG_PATH/.pier rule deploy --path=$CONFIG_PATH/.pier/fabric/validating.wasm
    # 部署验证规则后会打印：Deploy rule successfully: 0xed97cBf32C16551604f0017f9aA776bF96809b87

    $ pier --repo $CONFIG_PATH/.pier rule register --appchain-id fabappchain --rule \
    0xed97cBf32C16551604f0017f9aA776bF96809b87 --rule-url "http://github.com"
    # 注册成功后会打印: Register rule to bitxhub for appchain ethappchain successfully.
    ```

