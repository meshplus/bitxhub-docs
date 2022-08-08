# 2. 应用链跨链合约部署

当前步骤是要在应用链上部署跨链合约broker。

我们提供了针对不同应用链的跨链合约，下面以Ethereum和Fabric为例进行介绍，其它类型的应用链部署跨链合约的步骤基本上是一致的。

## Ethereum部署跨链合约

broker合约可以在[pier-client-ethereum项目](https://github.com/meshplus/pier-client-ethereum)的example目录下获取。

部署broker合约需要有构造参数，具体参数含义可以参考[broker合约的说明](../../../design/broker/#broker_1)。

在Ethereum上部署合约的工具有很多，您可以使用[Remix](https://remix.ethereum.org/)进行合约的编译和部署，也可以使用Goduck进行部署：

=== "Remix"

    broker合约构造参数示例如下：

    ```
    "1356","ethappchain",["0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","0x79a1215469FaB6f9c63c1816b45183AD3624bE34","0x97c8B516D19edBf575D72a172Af7F418BE498C37","0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"],"3",["0xe7826817f96e6218A0a89100414F41022650c537"],"1"
    ```

    ![!](../../../../assets/eth_deploy_broker_remix.png)
    ![!](../../../../assets/eth_deploy_broker_remix3.png)

    ```
    注意：填写参数时，需要将ADMINS参数使用账户ACCOUNT代替
    ```

=== "Goduck"

    Step1: 获取goduck工具（若已获取可跳过）

    ```shell
    git clone https://github.com/meshplus/goduck.git
    cd goduck && make install
    # 初始化goduck配置
    goduck init
    ```

    Step2: 部署broker合约

    ```shell
    # address指定以太坊地址
    # key-path指定以太坊账户地址的私钥，goduck使用该账户进行合约部署，所以需要保证该账户有足够的gas费进行合约部署
    # psd-path指定以太坊账户地址私钥的密码
    # code-path指定broker.sol合约路径（在pier-client-ethereum/example目录下）
    # 合约的构造参数通过"^"进行分割
    # 注意!!!由于goduck采用远程编译的方式进行，需要确保以太坊所在服务器有对应的solitidy编译环境
    goduck ether contract deploy \
    --address http://localhost:8545 \
    --key-path account.key \
    --psd-path password \
    --code-path broker.sol \
    "1356"^"ethappchain"^["0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","0x79a1215469FaB6f9c63c1816b45183AD3624bE34","0x97c8B516D19edBf575D72a172Af7F418BE498C37","0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"]^"3"^["0x20f7fac801c5fc3f7e20cfbadaa1cdb33d818fa3"]^"1"
    ```

    ![!](../../../../assets/eth_deploy_broker.png)

**说明**：部署broker合约后，需要 **记住broker合约的地址** ，后续业务合约可能需要引用broker合约的地址，才能正确完成跨链调用。

## Fabric部署跨链合约

broker合约可以在[pier-client-fabric项目](https://github.com/meshplus/pier-client-fabric)的example目录下获取：

```shell
git clone https://github.com/meshplus/pier-client-fabric.git
cd pier-client-fabric && git checkout release-2.0-mock
# 需要部署的合约文件就在example目录下
# 解压即可
cd example && unzip -q contracts.zip
```

Fabric部署合约可以使用[fabric-cli](https://github.com/hyperledger/fabric-cli)，也可以使用Goduck：

=== "Fabric-cli"

    Step1: 安装部署合约的工具fabric-cli

    ```shell
    cd ~/bitxhub-v2.0.0 && go get github.com/securekey/fabric-examples/fabric-cli/cmd/fabric-cli
    ```

    Step2: 部署broker合约

    ```shell
    # --gopath 为pier-client-fabric/exmaple目录下的contracts目录
    # 安装和示例化broker合约，其中${CONFIG_YAML}为fabric配置文件的路径，初始文件路径为pier-client-fabric/config/config.yaml
    fabric-cli chaincode install --gopath ./contracts --ccp broker --ccid broker --config "${CONFIG_YAML}" --orgid org2 --user Admin --cid mychannel
    fabric-cli chaincode instantiate --ccp broker --ccid broker \
    --config "${CONFIG_YAML}" --orgid org2 --user Admin --cid mychannel

    # 初始化合约参数(以中继链id=1356和应用链id=fabappchain为例)
    fabric-cli chaincode invoke --cid mychannel --ccid=broker \
    --args='{"Func":"initialize", "Args":["1356", "fabappchain"]}' \
    --user Admin --orgid org2 --payload --config "${CONFIG_YAML}"
    ```

    ```shell
    注意：config.yaml文件不可以直接使用，需要修改所有${CONFIG_PATH}为启动的fabric
    所生成的配置文件的路径，以fabric—samples为例，生成的配置文件路径为fabric-samples/first-network/crypto-config
    ```
    

=== "Goduck"

    Step1: 获取goduck工具（若已获取可跳过）

    ```shell
    git clone https://github.com/meshplus/goduck.git
    cd goduck && make install
    # 初始化goduck配置
    goduck init
    ```

    Step2: 部署broker合约

    ```shell
    # config-path指定fabric配置文件路径
    # gopath指定fabric合约路径，pier-client-fabric/exmaple目录下的contracts目录
    # ccp指定fabric chaincode路径
    # ccid指定fabric chaincode id
    # version指定fabric chaincode版本
    # 注意！！！config.yaml如果使用的变量，需要指定具体的证书路径
    goduck fabric deploy \
    --config-path config.yaml \
    --gopath contracts
    --ccp broker \
    --ccid broker \
    --version 1

    # 初始化broker合约需要提供BitXHub的chainId，以及对应应用链的ID
    goduck fabric contract invoke --config-path config.yaml broker initialize 1356,fabappchain
    ```

    ![!](../../../../assets/fabric_deploy_broker.png)