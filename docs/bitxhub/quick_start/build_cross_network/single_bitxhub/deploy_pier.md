# 3. 跨链网关部署

跨链网关Pier能够支持应用链便捷、快速地接入到跨链平台BitXHub中，从而实现和其他业务区块链的跨链操作。

## 获取pier部署包

可以通过源码编译和二进制下载的方式获取部署包。

```shell
# 编译跨链网关本身
cd ~/bitxhub-v2.0.0
git clone https://github.com/meshplus/pier.git
cd pier && git checkout release-2.0
make prepare && make build
```

## 初始化pier配置

在进行应用链注册、验证规则部署等步骤之前，需要初始化跨链网关的配置目录，以用户目录下的pier为例：

```shell
CONFIG_PATH=$HOME/bitxhub-v2.0.0
pier --repo $CONFIG_PATH/.pier init relay
#注意：如果想要同时启动两条链，例如以太坊和Fabric，需要使用pier再次初始化一个文件夹，例如
#pier --repo $CONFIG_PATH/.pier1 init relay
#再将另一条链对应的命令的.pier修改为.pier1
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

## 获取应用链插件部署包

=== "Ethereum"

    **源码下载编译**

    ```shell
    # 编译Ethereum 插件
    cd $CONFIG_PATH
    git clone https://github.com/meshplus/pier-client-ethereum.git
    cd pier-client-ethereum && git checkout release-2.0
    make eth

    # 说明：1.ethereum插件编译之后会在插件项目的build目录生成二进制插件文件eth-client；
    # 2.需要将生成好的二进制插件存放到pier的配置目录下的plugins文件夹底下；
    # 3.插件名称应与下一步的修改应用链插件的配置的plugin名称一致（为了方便起见，统一重命名为appchain_plugin）
    mkdir $CONFIG_PATH/.pier/plugins && cp build/eth-client $CONFIG_PATH/.pier/plugins/appchain_plugin
    ```

    **二进制下载**

    除了源码编译外，我们也提供了直接下载Pier及其插件二进制的方式，下载地址链接如下：[Pier二进制包下载](https://github.com/meshplus/pier/releases/tag/v2.0.0) 和 [ethereum插件二进制包下载](https://github.com/meshplus/pier-client-ethereum/releases/tag/v2.0.0)链接中已经包含了所需的二进制程序和依赖库，您只需跟据操作系统的实际情况进行选择和下载即可。

=== "Fabric"

    **源码下载编译**

    ```shell
    # 编译Fabric插件
    cd $CONFIG_PATH
    git clone https://github.com/meshplus/pier-client-fabric.git
    cd pier-client-fabric && git checkout release-2.0
    make fabric1.4

    # 说明：1.fabric插件编译之后会在插件项目的build目录生成fabric-client-1.4文件；
    # 2.需要将生成好的二进制插件存放到pier的配置目录下的plugins文件夹底下；
    # 3.插件名称应与下一步的修改应用链插件的配置的plugin名称一致（为了方便起见，统一重命名为appchain_plugin）
    mkdir $CONFIG_PATH/.pier/plugins && cp build/fabric-client-1.4 $CONFIG_PATH/.pier/plugins/appchain_plugin
    ```

    **二进制下载**

    除了源码编译外，我们也提供了直接下载Pier及其插件二进制的方式，下载地址链接如下：[Pier二进制包下载](https://github.com/meshplus/pier/releases/tag/v2.0.0) 和 [fabric插件二进制包下载](https://github.com/meshplus/pier-client-fabric/releases/tag/v2.0.0)链接中已经包含了所需的二进制程序和依赖库，您只需跟据操作系统的实际情况进行选择和下载即可。

经过以上的步骤，相信您已经编译出了部署Pier和fabric/ethereum应用链插件的二进制文件，Pier节点运行还需要外部依赖库，均在项目build目录下（Macos使用libwasmer.dylib，Linux使用libwasmer.so）,建议将得到的二进制和适配的依赖库文件拷贝到同一目录，然后使用 `export LD_LIBRARY_PATH=$(pwd)`命令指定依赖文件的路径，方便之后的操作。

## 修改pier配置

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
type = "relay" # relay, direct or union
[mode.relay]
addrs = ["localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014"]
timeout_limit = "1s"
quorum = 2
bitxhub_id = "1356"
enable_offchain_transmission = false
[mode.direct]
gas_limit = 0x5f5e100
```

- 修改应用链信息（针对不同应用链类型进行配置）

=== "Ethereum"

    ```toml
    [appchain]
    # 应用链id
    id = "ethappchain"
    # ethereum插件二进制文件的名称
    plugin = "appchain_plugin"
    # ethereum配置文件夹在跨链网关配置文件夹下的相对路径
    config = "ether"
    ```
=== "Fabric"

    ```toml
    [appchain]
    # 应用链id
    id = "fabappchain"
    # fabric插件二进制文件的名称
    plugin = "appchain_plugin"
    # ethereum配置文件夹在跨链网关配置文件夹下的相对路径
    config = "fabric"
    ```

## 修改应用链插件的配置

应用链插件的配置目录即是pier.toml中的config字段，它的模板分别在`pier-client-fabric`或`pier-client-ethereum`项目（之前拉取跨链合约时已经clone），直接在GitHub上下载代码即可。

=== "Ethereum"

    ```shell
    # 切换到pier-client-ethereum项目路径下
    cd  pier-client-ethereum
    cp -r ./config $CONFIG_PATH/.pier/ether
    ```
    其中重要配置如下：
    ```text
    ├── account.key
    ├── broker.abi
    ├── ether.validators
    ├── ethereum.toml
    ├── password
    └── validating.wasm
    ```
    **说明**：

    - account.key和password建议换成应用链上的真实账户，且须保证有一定金额（ethereum上调用合约需要gas费）
    - broker.abi可以使用示例，也可以使用您自己编译/部署broker合约时返回的abi；
    - ether.validators和validating.wasm一般不需要修改。
    - ethereum.toml是需要重点修改的，需要根据应用链实际情况填写**ethereum网络地址**、broker合约地址及业务合约abi**，账户的key等，示例如下：

    ```toml
    [ether]
    addr = "wss://kovan.infura.io/ws/v3/cc512c8c74c94938aef1c833e1b50b9a"
    name = "ether-kovan"
    ## 此处合约地址需要替换成变量代表的实际字符串
    contract_address = "$brokerAddr"
    key_path = "account.key"
    password = "password"
    min_confirm = 1
    timeout_height = 100
    timeout_period = 60
    offchain_addr = ""
    offchain_path = ""
    ```

=== "Fabric"

    ```shell
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
    # 替换为您部署的Fabric网络的拓扑设置文件即可，同时需要修改所有的Fabric的IP地址，如：
    url: grpcs://localhost:7050 => url: grpcs://10.1.16.48:7050
    ```
    3. **修改Plugin配置文件 fabric.toml**

    示例是以官方部署脚本进行配置：
    ```toml
    [fabric]
    username = "Admin"
    ccid = "broker"
    channel_id = "mychannel"
    org = "org2"
    timeout_height = 30
    chain_id = "3"

    [[services]]
    id = "mychannel&transfer"
    name = "transfer"

    [[services]]
    id = "mychannel&data_swapper"
    name = "data_swapper"
    ```

## 启动跨链网关节点

在完成以上步骤之后，可以启动跨链网关节点了：

```shell
# 以用户目录下的pier为例
pier --repo $CONFIG_PATH/.pier start
```

**观察日志信息没有报错信息，可以正常同步到中继链上的区块信息，即说明pier启动成功。**
