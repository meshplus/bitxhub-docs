# 跨链网关部署

跨链网关Pier能够支持业务所在区块链（以下简称 应用链）便捷、快速的接入到跨链平台BitXHub中，从而实现和其他业务区块链的跨链操作。目前中继链和跨链网关已经支持 **Fabric**、**Ethereum**、**BCOS**、**CITA** 和 **Hyperchain** 五种应用链接入并完成跨链交易，如果您有兴趣，也可以参与开发适配另外种类应用链的插件和合约。

跨链网关的部署需要提前确定应用链类型（对应不同的插件和配置），也需要提前在对应的应用链上部署跨链合约，我们将跨链网关的部署依次分为如下五个步骤：

1. **部署跨链合约**

2. **获取部署包和修改Pier配置**

3. **注册应用链信息**

4. **部署验证规则**

5. **启动跨链网关**



## 部署跨链合约

这一章是要在应用链上部署跨链合约，**注意：在此操作之前，您需要确认已经部署有可接入的应用链**。快速部署应用链请参考[Goduck关于应用链的操作](../../../../goduck/appchain)。

我们提供了针对不同应用链的跨链合约，broker合约是管理合约，transfer合约是业务交易合约，需要说明的是 transfer合约需要经过broker合约审核通过后才能发起或接受跨链交易，具体方法是：调用broker合约的audit方法，其参数依次是业务合约地址和合约状态（数字1表示审核通过，数字2表示审核失败）。

下面以Ethereum和Fabric为例进行介绍，其它类型的应用链部署跨链合约的步骤基本上是一致的。

=== "Ethereum"

    在Ethereum上部署合约的工具有很多，您可以使[Remix](https://remix.ethereum.org/)进行合约的编译和部署，这里关键的是跨链合约的获取。可以在[pier-client-ethereum项目](https://github.com/meshplus/pier-client-ethereum/blob/v1.11.2/example)的exampe文件夹中获取。

    **说明：**
    1. 合约文件就在项目的example目录下，broker.sol是跨链管理合约，transfer.sol是示例业务合约；
    2. 首先部署broker合约，然后将返回的合约地址填入transfer合约中的`BrokerAddr`字段，这样业务合约才能正确跨链调用。

=== "Fabric"

    在Fabric上部署跨链合约工具一般是fabric-cli（可以参考[官方项目的使用说明](https://github.com/hyperledger/fabric-cli)）， 在Fabric上部署跨链合约的过程和部署其它合约没有区别，只是合约名称和代码文件需要替换，以下操作的命令可供参考，默认应用链是使用的fabric-sample项目的v1.4.3版本部署。
    
    Step1: 安装部署合约的工具fabric-cli
    ```shell
    cd ~/bitxhub-v1.11 && go get github.com/securekey/fabric-examples/fabric-cli/cmd/fabric-cli
    ```
    
    Step2: 获取需要部署的合约文件并解压
    ```shell
    git clone https://github.com/meshplus/pier-client-fabric.git 
    cd pier-client-fabric && git checkout v1.11.2
    # 需要部署的合约文件就在example目录下
    # 解压即可
    unzip -q contracts.zip
    ```
    
    Step3: 部署broker、transfer合约
    ```shell
    #安装和示例化broker合约
    fabric-cli chaincode install --gopath ./contracts --ccp broker --ccid broker --config "${CONFIG_YAML}" --orgid org2 --user Admin --cid mychannel
    fabric-cli chaincode instantiate --ccp broker --ccid broker --config "${CONFIG_YAML}" --orgid org2 --user Admin --cid mychannel
    
    #安装和示例化transfer合约
    fabric-cli chaincode install --gopath ./contracts --ccp transfer --ccid transfer --config "${CONFIG_YAML}" --orgid org2 --user Admin --cid mychannel
    fabric-cli chaincode instantiate --ccp transfer --ccid transfer --config "${CONFIG_YAML}" --orgid org2 --user Admin --cid mychannel
    
    #业务合约需要broker管理合约审计后，才能进行跨链交易
    fabric-cli chaincode invoke --cid mychannel --ccid=broker \
    --args='{"Func":"audit", "Args":["mychannel", "transfer", "1"]}' \
    --user Admin --orgid org2 --payload --config "${CONFIG_YAML}"
    ```

## 获取部署包和修改Pier配置
这一章是要获取部署包和修改Pier的配置，为启动pier节点作准备，主要分为pier本身和应用链插件的配置修改。可以通过源码编译和二进制下载的方式获取部署包。
### 获取部署包

```shell
# 编译跨链网关本身
cd ~/bitxhub-v1.11
git clone https://github.com/meshplus/pier.git
cd pier && git checkout v1.11.2
make prepare && make install
```

### 初始化pier配置

在进行应用链注册、验证规则部署等步骤之前，需要初始化跨链网关的配置目录，以用户目录下的pier为例：

```shell
CONFIG_PATH=$(HOME)/bitxhub-v1.11
pier --repo $CONFIG_PATH/.pier init relay
```

该命令会生成跨链网关的一些基础配置文件模板，使用 tree 命令可查看目录信息：

```shell
tree -L 1 $CONFIG_PATH/.pier
├── api
├── certs
├── key.json
├── node.priv
└── pier.toml    
1 directory, 4 files
```

### 获取应用链插件部署包

=== "Ethereum"

    **源码下载编译**
    
    ```shell
    # 编译Ethereum 插件
    cd $CONFIG_PATH
    git clone https://github.com/meshplus/pier-client-ethereum.git
    cd pier-client-ethereum && git checkout v1.11.2
    make eth
    
    # 说明：1.ethereum插件编译之后会在插件项目的build目录生成eth-client文件；
    # 2.需要将生成好的二进制插件存放到pier的配置目录下的plugins文件夹底下；
    # 3.插件名称应与下一步的修改应用链插件的配置的plugin名称一致（为了方便起见，统一重命名为appchain_plugin）
    mkdir $CONFIG_PATH/.pier/plugins && cp build/eth-client $CONFIG_PATH/.pier/plugins/appchain_plugin
    ```
    
    **二进制下载**
    
    除了源码编译外，我们也提供了直接下载Pier及其插件二进制的方式，下载地址链接如下：[Pier二进制包下载](https://github.com/meshplus/pier/releases/tag/v1.11.2) 和 [ethereum插件二进制包下载](https://github.com/meshplus/pier-client-ethereum/releases/tag/v1.11.2)链接中已经包含了所需的二进制程序和依赖库，您只需根据操作系统的实际情况进行选择和下载即可。

=== "Fabric"

    **源码下载编译**
    
    ```shell
    # 编译Fabric插件
    cd $CONFIG_PATH
    git clone https://github.com/meshplus/pier-client-fabric.git
    cd pier-client-fabric && git checkout v1.11.2
    make fabric1.4
    
    # 说明：1.fabric插件编译之后会在插件项目的build目录生成fabric-client-1.4文件；
    # 2.需要将生成好的二进制插件存放到pier的配置目录下的plugins文件夹底下；
    # 3.插件名称应与下一步的修改应用链插件的配置的plugin名称一致（为了方便起见，统一重命名为appchain_plugin）
    mkdir $CONFIG_PATH/.pier/plugins && cp build/fabric-client-1.4 $CONFIG_PATH/.pier/plugins/appchain_plugin
    ```
    
    **二进制下载**
    
    除了源码编译外，我们也提供了直接下载Pier及其插件二进制的方式，下载地址链接如下：[Pier二进制包下载](https://github.com/meshplus/pier/releases/tag/v1.11.2) 和 [fabric插件二进制包下载](https://github.com/meshplus/pier-client-fabric/releases/tag/v1.11.2)链接中已经包含了所需的二进制程序和依赖库，您只需跟据操作系统的实际情况进行选择和下载即可。

经过以上的步骤，相信您已经编译出了部署Pier和fabric/ethereum应用链插件的二进制文件，Pier节点运行还需要外部依赖库，均在项目build目录下（Macos使用libwasmer.dylib，Linux使用libwasmer.so）,建议将得到的二进制和适配的依赖库文件拷贝到同一目录，然后使用 `export LD_LIBRARY_PATH=$(pwd)`命令指定依赖文件的路径，方便之后的操作。

### 修改Pier配置

pier.toml是描述链跨链网关启动的主要配置，一般需要修改的是端口信息、中继链的信息、应用链的信息。

```shell
cd $CONFIG_PATH/.pier && vim pier.toml
```

- 修改端口信息

```toml
[port]
# 如果不冲突的话，可以不用修改
http  = 44544
pprof = 44555
```

- 修改中继链信息（一般只修改addrs字段，指定bitxhub的节点地址）

```toml
[mode]
type = "relay" # relay or direct
[mode.relay]
addrs = ["localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014"]
quorum = 2
validators = [
    "0x000f1a7a08ccc48e5d30f80850cf1cf283aa3abd",
    "0xe93b92f1da08f925bdee44e91e7768380ae83307",
    "0xb18c8575e3284e79b92100025a31378feb8100d6",
    "0x856E2B9A5FA82FD1B031D1FF6863864DBAC7995D",
]
```

- 修改应用链信息（针对不同应用链类型进行配置）
    - 其中did的格式应为`did:bitxhub:appchain{pier_id}:.`，pier_id通过`pier --repo=$CONFIG_PATH/.pier id`获得；
    - `plugin`为插件名称，**注意**：插件名称应该与pier的repo地址下plugin目录的插件名称相同。

=== "Ethereum"

    ```toml
    [appchain]
    config = "ethereum" #标识当前应用链类型，用户可自定义
    did = "did:bitxhub:appchain0x450884c9F7fdFc72E2bC1245306d15dE1750A880:."
    plugin = "appchain_plugin"
    ```
=== "Fabric"

    ```toml
    [appchain]
    config = "fabric"
    did = "did:bitxhub:appchain0x450884c9F7fdFc72E2bC1245306d15dE1750A880:."
    plugin = "appchain_plugin"
    ```

### 修改应用链插件的配置

应用链插件的配置目录即是pier.toml中的config字段，它的模板在`pier-client-ethereum`或`pier-client-ethereum`项目（之前拉取跨链合约时已经clone），直接在GitHub上下载代码即可。

=== "Ethereum"

    ```shell
    cd $CONFIG_PATH/.pier && mkdir ether
    
    # 将pier-client-ethereum的config目录下的文件拷贝到pier的配置路径
    cp $CONFIG_PATH/pier-client-ethereum/config $CONFIG_PATH/.pier/ether
    ```
    其中重要配置如下：
    ```shell
    tree -L 1 $CONFIG_PATH/.pier/ether
    ├── account.key
    ├── broker.abi
    ├── data_swapper.abi
    ├── ether.validators
    ├── ethereum.toml
    ├── password
    ├── pier.toml
    ├── transfer.abi
    └── validating.wasm
    ```
    **注意：如果使用[goduck关于应用链的操作](../../../../goduck/appchain/#_1)启动应用链，需要将`${goduck_repo}/pier/ethereum/1.3.0/`文件夹下的文件拷贝到${pier_repo}/ehter目录下：**
    ```shell
    cp $CONFIG_PATH/.goduck/pier/ethereum/1.3.0/* $CONFIG_PATH/.pier/ether
    ```
    
    **说明**：
    
    - account.key和password建议换成应用链上的真实账户，且须保证有一定金额（ethereum上调用合约需要gas费）;
    - broker.abi可以使用示例，也可以使用您自己编译/部署broker合约时返回的abi；
    - ether.validators和validating.wasm一般不需要修改,pier.toml为pier配置模板，暂未使用，可不做更改；
    - ethereum.toml是需要重点修改的，需要根据应用链实际情况填写 **ethereum网络地址**、**broker合约地址及业务合约abi**，**账户的key**，示例如下：
        - `addr`: 如果是本地启动的以太坊私链或者参考[goduck关于应用链的操作](../../../../goduck/appchain/#_1)启动的应用链，地址应为`ws://localhost:8546`；如果，地址应为`ws://host.docker.internal:8546`；
        - `nanme`：应用链名称，用户可自定义；
        - `contract_address`：broker合约地址，如果是本地启动的以太坊私链，地址更换为正确的合约地址，如果使用goduck启动应用链，地址为`0xD3880ea40670eD51C3e3C0ea089fDbDc9e3FBBb4`；
        - `abi_path`:`broker.abi`文件路径；
        - `key_path`：以太坊账户私钥，如果为本地启动的以太坊私链，地址为`${datadir}/data/keystore`；
        - `password`：以太坊账户密码；
        - `min_confirm`：最小区块确认数；
        - `[contract_abi]`:填写业务合约地址，如：`0x668a209Dc6562707469374B8235e37b8eC25db08="transfer.abi"`
        
    ```toml
    [ether]
    addr = "ws://localhost:8546"
    name = "ether"
    ## 此处合约地址需要替换成变量代表的实际字符串
    contract_address = "0xD3880ea40670eD51C3e3C0ea089fDbDc9e3FBBb4"
    abi_path = "broker.abi"
    key_path = "account.key"
    password = "password"
    min_confirm = 1
    
    [contract_abi]
    "0x668a209Dc6562707469374B8235e37b8eC25db08"="transfer.abi"
    ```

=== "Fabric"
    ```shell
    # 将fabric插件拷贝到plugins目录下
    cp fabric-client-1.4 $CONFIG_PATH/.pier/plugins/
    # 切换到pier-client-fabric项目路径下
    cd pier-client-fabric
    cp ./config $CONFIG_PATH/.pier/fabric
    ```
    其中重要配置如下：
    ```shell
    ├── crypto-config/
    ├── config.yaml
    ├── fabric.toml
    ├── fabric.validators
    └── validating.wasm
    ```
    接下来主要修改Fabric网络配置，验证证书，跨链合约设置：

    1. **fabric证书配置**
    
    启动Fabric网络时，会生成所有节点（包括Order、peer等）的证书信息，并保存在 crypto-config文件夹中，Fabric插件和Fabric交互时需要用到这些证书。
    ```shell
    # 复制您所部署的Fabric所产生的crypto-config文件夹
    cp -r /path/to/crypto-config $CONFIG_PATH/.pier/fabric/
    
    # 复制Fabric上验证人证书
    cp $CONFIG_PATH/.pier/fabric/crypto-config/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp/signcerts/peer1.org2.example.com-cert.pem $CONFIG_PATH/.pier/fabric/fabric.validators
    ```
    2. **修改Plugin配置文件config.yaml**
    
    `config.yaml`文件记录的Fabric网络配置（用您的网络拓扑配置文件替换这个样例文件），需要使用绝对路径，把所有的路径都修改为 `crypto-config`文件夹所在的绝对路径。
    ```shell
    {CONFIG_PATH}/fabric/crypto-config =>$CONFIG_PATH/.pier/fabric/crypto-config
    # 替换为您部署的Fabric网络的拓扑设置文件即可，同时需要修改所有的Fabric 的IP地址，如：
    url: grpcs://localhost:7050 => url: grpcs://10.1.16.48:7050
    ```
    3. **修改Plugin配置文件 fabric.toml**
    
    示例是以官方部署脚本进行配置：
    ```toml
    addr = "localhost:7053" // 若Fabric部署在服务器上，该为服务器地址
    event_filter = "interchain-event-name"
    username = "Admin"
    ccid = "broker" // 若部署跨链broker合约名字不是broker需要修改
    channel_id = "mychannel"
    org = "org2"
    ```

**至此，对接ethereum和fabric应用链的pier及其插件的配置已经完成，接下来需要进行应用链注册和验证规则部署后，再启动pier节点。**

中继模式验证规则部署流程请参考[验证规则](../../../function/relay_manager/#3)。

**！！！注意在进行应用链注册之前首先需要进行验证规则的部署**

Fabric内置的验证规则有:

1. Fabric复杂验证规则`0x00000000000000000000000000000000000000a0`
2. Fabric简单验证规则`0x00000000000000000000000000000000000000a1`

所有类型的应用链（包含Fabric）都支持无验证规则`0x00000000000000000000000000000000000000a2`
## 注册应用链
在启动跨链网关Pier之前，需要先注册应用链并部署验证规则，这些操作均是Pier命令行发起。中继模式应用链注册参考[中继模式管理](../../../function/relay_manager)需要注意的是，在v1.11.2及以上的版本，注册应用链需要中继链BitXHub节点管理员进行投票，投票通过之后才能接入。这一步Ethereum和Fabric（包括其它类型应用链）的流程一样，只是注册信息有所不同。
**需要注意的是v1.11.2及以上版本的网关注册时会将提供的验证规则地址注册为主验证规则。**

1. [Pier命令行发起应用链注册](../../../function/relay_manager/#11);

2. [中继链节点依次投票](../../../function/relay_manager/#12)。

## 更新主验证规则
应用链除了在注册应用链时，绑定主验证规则，在可用状态下也可以更新主验证规则，即可以在应用链注册成功且中继链投票通过后进行主验证规则的更新。之前已经准备好了验证规则文件，接下来在Pier端发起部署验证规则的命令再进行主验证规则的更新。

1. [部署并注册验证规则](../../../function/relay_manager/#31)；
2. [更新主验证规则](../../../function/relay_manager/#32)。

## 启动跨链网关节点
在完成以上步骤之后，可以启动跨链网关节点了！

```
#以用户目录下的pier为例
pier --repo=$CONFIG_PATH/.pier start
```

**观察日志信息没有报错信息，可以正常同步到中继链上的区块信息，即说明pier启动成功。**