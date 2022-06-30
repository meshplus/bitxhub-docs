# Goduck运维小工具

## 1 安装

### 1.1 获取源码
下载源码并切到稳定版本release-1.23
```
git clone git@github.com:meshplus/goduck.git
cd goduck
git checkout release-1.23
```

### 1.2 编译安装

```
make install
```

### 1.3 初始化

```
goduck init
# 初始化成功会打印出信息如下：
Init goduck successfully in $HOME/.goduck!
```

使用之前一定要先初始化，默认初始化路径为$HOME/.goduck，后续将用$repo代替goduck配置路径（如默认即为$HOME/.goduck）

## 2 使用

### 2.1 命令格式

```
goduck [global options] command [command options] [arguments...]
```

**command**

- `init`        初始化配置，默认路径：$HOME/.goduck
- `playground`  一键启动跨链组件
- `bitxhub`     启动或关闭bithxub节点
- `pier`        有关pier的操作
- `deploy`      远程部署bitxhub和pier
- `ether`       有关以太坊应用链的操作
- `fabric`      有关fabric应用链的操作
- `hpc`         部署、调用或更新hyperchain的合约
- `mq`          有关hyperchain的mq操作
- `status`      查询跨链系统各组件状态
- `info`        查询跨链系统各组件基本信息
- `key`         创建或显示密钥信息
- `prometheus`  启动或关闭prometheus
- `log`         打印bitxhub或pier的日志信息
- `version`     查询组件版本信息
- `help, h`

这些命令中，比较重要的是init（使用前一定要初始化）、status（查看当前运行组件状态）、bitxhub、pier。

**global options**

- `--repo value`          goduck配置文件默认存储路径
- `--help, -h`

### 2.2 关于BitXHub的操作

```
goduck bitxhub command [command options] [arguments...]
```

#### 2.2.1 启动BitXHub节点

```
goduck bitxhub start --version v1.23.0
```

该命令会初始化并启动1.23.0版本的BitXHub节点，如果有已启动的BitXHub节点会执行失败。执行成功后提示如下：

```
======> Start bitxhub cluster
Start bitxhub node1
Start bitxhub node2
Start bitxhub node3
Start bitxhub node4
You can use the "goduck status list" command to check the status of the startup BitXHub node.
```

这是默认启动方式：以二进制方式启动4节点cluster模式的bitxhub，bitxhub配置文件默认在$repo/bitxhub/.bitxhub/路径下。   

可以携带参数对启动方式进行简单调整：
- `--type value`               启动类型，二进制或docker，默认二进制
- `--target value`             启动配置路径，默认为$repo/bitxhub/.bitxhub/
- `--configPath value`         可修改配置路径，默认为$repo/bxh_config/$version/bxh_modify_config.toml，根据该文件可以对bitxhub更多信息进行自定义配置
- `--version value, -v value`  BitXHub版本
- `--help, -h`

如果想以更具体的自定义方式启动，可以对--configPath参数指定文件信息进行修改，即对$repo/bxh_config/v1.23.0/bxh_modify_config.toml配置文件相关参数进行调整。
该文件中部分参数配置如下：

```shell
...

[bitxhub]
  mode = cluster # 启动模式，solo或cluster
  num = 4 # cluster模式下启动节点个数
  consensus_type = raft # 共识算法
  rewrite = true # 启动bitxhub的目标路径下如果已经有bitxhub配置文件，是否重写
  # absolute path. It doesn't have to be in double quotes.
  agency_priv_path = REPO/bitxhub/certs/agency.priv # bitxhub节点代理机构私钥路径，需要使用绝对路径，默认情况下使用goduck提供的默认代理私钥
  agency_cert_path = REPO/bitxhub/certs/agency.cert # bitxhub节点代理机构证书路径，需要使用绝对路径，默认情况下使用goduck提供的默认代理证书
  ca_cert_path = REPO/bitxhub/certs/ca.cert # bitxhub节点CA机构证书路径，需要使用绝对路径，默认情况下使用goduck提供的默认CA证书
  [[bitxhub.node1]]
    node_host = host1 # 节点1所在机器，机器配置信息在bitxhub上方，默认情况下host1是本地机器，bitxhub start本地启动命令中该配置无效
    # 以下为节点各端口配置信息
    p2p_port = 4001
    jsonrpc_port = 8881
    grpc_port = 60011
    gateway_port = 9091
    pprof_port = 53121
    monitor_port = 40011
  #后续为bitxhub其他节点信息，如果启动节点个数超过配置节点个数，后续节点信息会使用默认配置信息（即端口号递增，其他信息不变）
...
```

#### 2.2.2 为BitXHub节点生成配置文件

```
goduck bitxhub config --version v1.23.0

# 成功最后的日志打印如下：
...
======> Rewrite config for node 1
【1】rewrite bitxhub.toml
【2】rewrite network.toml
======> Rewrite config for node 2
【1】rewrite bitxhub.toml
【2】rewrite network.toml
======> Rewrite config for node 3
【1】rewrite bitxhub.toml
【2】rewrite network.toml
======> Rewrite config for node 4
【1】rewrite bitxhub.toml
【2】rewrite network.toml
```

该命令默认初始化4个BitXHub节点。执行成功后会在默认路径（$repo/bitxhub/.bitxhub/）生成相关证书、私钥文件以及四个节点的文件夹:

```
.
├── node1
│   ├── README.md
│   ├── appchain
│   │   ├── eth_header.json
│   │   └── eth_header1.json
│   ├── bitxhub.toml
│   ├── certs
│   │   ├── agency.cert
│   │   ├── ca.cert
│   │   ├── node.cert
│   │   ├── node.priv
│   │   ├── server.key
│   │   └── server.pem
│   ├── key.json
│   ├── network.toml
│   └── order.toml
├── node2/
├── node3/
└── node4/

12 directories, 52 files
```

可以携带参数对生成方式进行简单调整：
- `--target value`             配置文件路径，默认为$repo/bitxhub/.bitxhub/
- `--configPath value`         可修改配置路径，默认为$repo/bxh_config/$version/bxh_modify_config.toml，根据该文件可以对bitxhub更多信息进行自定义配置
- `--version value, -v value`  BitXHub版本
- `--help, -h`

如果想以更具体的自定义方式生成配置，可以对--configPath参数指定文件信息进行修改，即对$repo/bxh_config/v1.23.0/bxh_modify_config.toml配置文件相关参数进行调整。
该文件中参数配置同2.2.1节中的介绍

#### 2.2.3 关闭BitXHub节点

```
goduck bitxhub stop
```

该命令会关闭所有启动的BitXHub节点。执行成功后会提示里会给出关闭节点的id，大致如下：

```
exec:  /bin/bash $repo/playground.sh down
======> Stop bitxhub
node pid:59443 exit
node pid:59462 exit
node pid:59481 exit
node pid:59500 exit
```

#### 2.2.4 清除BitXHub节点

```
goduck bitxhub clean
```

该命令会清除bitxhub节点的配置文件。如果bitxhub节点没有关闭，会先关闭节点再清除配置文件。

当bitxhub节点成功在关闭后执行此命令，会打印出提示如下：

```
exec:  /bin/bash $repo/playground.sh clean
======> Stop bitxhub
======> Clean bitxhub
remove bitxhub configure node1
remove bitxhub configure node2
remove bitxhub configure node3
remove bitxhub configure node4
```

当bitxhub solo节点成功在未关闭的情况下执行此命令，会打印出提示如下：

```
exec:  /bin/bash $repo/playground.sh clean
======> Stop bitxhub
node pid:73062 exit
node pid:73081 exit
node pid:73100 exit
node pid:73119 exit
======> Clean bitxhub
remove bitxhub configure node1
remove bitxhub configure node2
remove bitxhub configure node3
remove bitxhub configure node4
```

### 2.3 关于pier的操作

```
GoDuck pier command [command options] [arguments...]
```

#### 2.3.1 启动pier

```shell
goduck pier start --version v1.23.0

# 启动成功后打印日志大致如下：
======> Generate configuration files for pier_ethereum
【1】generate configuration files
【2】copy pier plugin and appchain config
======> Rewrite config for pier_ethereum
【1】rewrite pier.toml
【2】rewrite appchain config
【3】rewrite api
======> Start pier of ethereum in binary...
You can use the "goduck status list" command to check the status of the startup pier.
```

这是默认启动方式：以二进制方式启动对接以太坊应用链的pier，pier配置文件默认在$repo/pier/.pier_ethereum/路径下。

可以携带参数对启动方式进行简单调整：
- `--appchain value`           应用链类型，ethereum或fabric，默认"ethereum"
- `--target value`             启动配置路径，默认为$repo/pier/.pier_$chainType
- `--upType value`             启动方式类型，binary或docker，默认"binary"
- `--configPath value`         可修改配置路径，默认为$repo/pier_config/$VERSION/pier_modify_config.toml
- `--version value, -v value`  Pier版本
- `--help, -h`

如果想以更具体的自定义方式启动，可以对--configPath参数指定文件信息进行修改，即对$repo/pier_config/v1.23.0/pier_modify_config.toml配置文件相关参数进行调整。
该文件中部分参数配置如下：

```shell
...

[pier]
  pier_host = host1 # pier所在机器，机器配置信息在pier上方，默认情况下host1是本地机器，pier start本地启动命令中该配置无效
  method = appchain # 应用链did的method
  # whether to overwrite the configuration files if them already exist
  rewrite = true # 启动pier的目标路径下如果已经有pier配置文件，是否重写
  # 以下为pier各端口配置信息
  httpPort = 44554
  pprofPort = 44550
  apiPort = 8080
  # whether to enable TLS
  tls = false # 是否开启tls
  # direct, relay or union
  mode = relay # 跨链模式，relay、direct或union，主要介绍relay模式
  [pier.relay]
    bitxhubAddr = localhost:60011 # relay模式中继链节点地址
    # relay模式中继链上验证人信息
    bitxhubValidators = ["0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","0x79a1215469FaB6f9c63c1816b45183AD3624bE34","0x97c8B516D19edBf575D72a172Af7F418BE498C37","0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"]
  
  ...
  
[appchain]
  [appchain.ethereum]
    # address of the contract on the appChain
    contractAddr = 0xD3880ea40670eD51C3e3C0ea089fDbDc9e3FBBb4 # 以太坊应用链上broker合约地址
    ethAddr = ws://127.0.0.1:8546 # 以太坊地址
  [appchain.fabric]
    # path of crypto-config
    cryptoPath = $repo/fabric/crypto-config # fabric链的crypto-config地址，这里指定的是goduck启动fabric后该文件的默认地址
    # binary local: 0.0.0.0
    # docker local: host.docker.internal
    fabricIP = 0.0.0.0 # fabric链ip地址，如果pier以docker模式启动需要改为host.docker.internal
    # 以下为fabric的各端口信息
    ordererP = 7050
    urlSubstitutionExpP1 = 7051
    eventUrlSubstitutionExpP1 = 7053
    urlSubstitutionExpP2 = 8051
    eventUrlSubstitutionExpP2 = 8053
    urlSubstitutionExpP3 = 9051
    eventUrlSubstitutionExpP3 = 9053
    urlSubstitutionExpP4 = 10051
    eventUrlSubstitutionExpP4 = 10053
```

在使用此命令之前，您需要启动一个相同版本的BitxHub和一个需要pier连接的应用链。如果应用链或bitxhub不存在，pier即使启动也会失败，可以通过查看pier日志信息查看pier状态:

```shell
# --pier-repo指定pier启动目录
goduck log pier --pier-repo $repo/pier/.pier_ethereum
```

#### 2.3.2 关闭pier

```
goduck pier stop
```

该命令可以关闭pier，可以通过携带参数指定关闭那种类型应用链的pier

#### 2.3.3 清除Pier

```
goduck pier clean
```

该命令可以清除pier的配置文件，如果pier还没有关闭，该命令会先关闭pier再清除其配置文件。

#### 2.3.4 生成Pier的配置文件

```
goduck pier config --version "v1.23.0"
``` 

### 2.4 关于应用链的操作

#### 2.2.1 以太坊

```shell
GoDuck ether command [command options] [arguments...]
```

关于以太坊应用链，我们对应不同bitxhub版本提供了已经部署好跨链合约的以太坊私链镜像，直接启动即可使用

（1）启动以太坊
注意启动时需要注明需要跨链的bitxhub版本，因为不同版本的bitxhub对应的跨链合约可能有差别。
#### goduck ether start
- --type：可选参数，指定启动模式，默认为docker模式，可指定为binary模式;
- --bxh-version：指定连接的bitxhub版本，不同版本的bitxhub对应的跨链合约可能有差别;

示例说明：
```shell
goduck ether start --bxh-version v1.23.0

# 启动成功后打印示例如下：
exec:  /bin/bash ethereum.sh docker 1.2.0
start a new ethereum-node container
fc6b26e2507fb7da3ca04abea6d014968297383986596377cd42e8852b2b83e6
start ethereum private chain with data directory in /Users/xxx/.goduck/ethereum/datadir.
```

该命令会以docker模式启动一条以太坊私链，如果想以二进制模式启动也可以通过指定参数实现，命令如下：
```shell
goduck ether start --bxh-version v1.23.0 --type binary
```

（2）关闭以太坊
关闭以太坊有stop和clean两个命令，推荐使用clean命令，在停止以太坊运行的同时会清除容器（如果是二进制模式启动会清除相关配置文件）
```shell
goduck ether stop

# 暂停成功后打印示例如下：
exec:  /bin/bash ethereum.sh down
===> stop ethereum in binary...
===> stop ethereum in docker...
ethereum-node
ethereum docker container stopped

goduck ether clean

# 清除成功后打印示例如下：
exec:  /bin/bash ethereum.sh clean
===> stop ethereum in binary...
===> stop ethereum in docker...
ethereum-node
ethereum docker container stopped
===> clean ethereum in binary...
===> clean ethereum in docker...
ethereum-node
ethereum docker container cleaned
```

（3）部署智能合约

#### goduck ether contract deploy

参数解释

- --address：可选参数，指定以太坊应用链地址，默认启动地址为http://localhost:8545

- --key-path：可选参数，指定部署合约账户私钥地址，默认为$goduck_repo/ethereum/account.key

- --psd-path：可选参数，指定部署合约账户密码地址，默认为$goduck_repo/ethereum/password

- --code-path：指定部署solidity合约地址，如有多个合约可以逗号隔开

目前最新版本智能合约可在#HOME/goduck/scripts/example下找到，部署示例如下

```shell
goduck ether contract deploy --code-path "#HOME/goduck/scripts/example/broker.sol" "1356^ethappchain1^["0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","0x79a1215469FaB6f9c63c1816b45183AD3624bE34","0x97c8B516D19edBf575D72a172Af7F418BE498C37","0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"]^1^["0x20F7Fac801C5Fc3f7E20cFbADaA1CDb33d818Fa3"]^1" 

======= /goduck/scripts/example/broker.sol:Broker =======
Deployed contract address is 0x857133c5C69e6Ce66F7AD46F200B9B3573e77582
Contract JSON ABI
[{"inputs":[{"internalType":"string","name":"_bitxhubID","type":"string"},{"internalType":"string","name":"_appchainID","type":"string"},{"internalType":"address[]","name":"_validators","type":"address[]"},{"internalType":"uint64","name":"_valThreshold","type":"uint64"},{"internalType":"address[]","name":"_admins","type":"address[]"},{"internalType":"uint64","name":"_adminThreshold","type":"uint64"}],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"uint64","name":"index","type":"uint64"},{"indexed":false,"internalType":"string","name":"dstFullID","type":"string"},{"indexed":false,"internalType":"string","name":"srcFullID","type":"string"},{"indexed":false,"internalType":"string","name":"func","type":"string"},{"indexed":false,"internalType":"bytes[]","name":"args","type":"bytes[]"},{"indexed":false,"internalType":"bytes32","name":"hash","type":"bytes32"}],"name":"throwInterchainEvent","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"uint64","name":"index","type":"uint64"},{"indexed":false,"internalType":"string","name":"dstFullID","type":"string"},{"indexed":false,"internalType":"string","name":"srcFullID","type":"string"},{"indexed":false,"internalType":"uint64","name":"typ","type":"uint64"},{"indexed":false,"internalType":"bool","name":"status","type":"bool"},{"indexed":false,"internalType":"bytes[]","name":"result","type":"bytes[]"},{"indexed":false,"internalType":"bytes32","name":"hash","type":"bytes32"}],"name":"throwReceiptEvent","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"bool","name":"","type":"bool"}],"name":"throwReceiptStatus","type":"event"},{"inputs":[{"internalType":"address","name":"addr","type":"address"},{"internalType":"int64","name":"status","type":"int64"}],"name":"audit","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"destFullServiceID","type":"string"},{"internalType":"string","name":"funcCall","type":"string"},{"internalType":"bytes[]","name":"args","type":"bytes[]"},{"internalType":"string","name":"funcCb","type":"string"},{"internalType":"bytes[]","name":"argsCb","type":"bytes[]"},{"internalType":"string","name":"funcRb","type":"string"},{"internalType":"bytes[]","name":"argsRb","type":"bytes[]"},{"internalType":"bool","name":"isEncrypt","type":"bool"}],"name":"emitInterchainEvent","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"chainID","type":"string"}],"name":"getAppchainInfo","outputs":[{"internalType":"string","name":"","type":"string"},{"internalType":"bytes","name":"","type":"bytes"},{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getCallbackMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getChainID","outputs":[{"internalType":"string","name":"","type":"string"},{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"id","type":"string"}],"name":"getDirectTransactionMeta","outputs":[{"internalType":"uint256","name":"","type":"uint256"},{"internalType":"uint64","name":"","type":"uint64"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getDstRollbackMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getInnerMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getLocalServiceList","outputs":[{"internalType":"string[]","name":"","type":"string[]"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"addr","type":"address"}],"name":"getLocalWhiteList","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"outServicePair","type":"string"},{"internalType":"uint64","name":"idx","type":"uint64"}],"name":"getOutMessage","outputs":[{"internalType":"string","name":"","type":"string"},{"internalType":"bytes[]","name":"","type":"bytes[]"},{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getOuterMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"remoteAddr","type":"string"}],"name":"getRSWhiteList","outputs":[{"internalType":"address[]","name":"","type":"address[]"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"inServicePair","type":"string"},{"internalType":"uint64","name":"idx","type":"uint64"}],"name":"getReceiptMessage","outputs":[{"internalType":"bytes[]","name":"","type":"bytes[]"},{"internalType":"uint64","name":"","type":"uint64"},{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getRemoteServiceList","outputs":[{"internalType":"string[]","name":"","type":"string[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"initialize","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"srcFullID","type":"string"},{"internalType":"string","name":"destAddr","type":"string"},{"internalType":"uint64","name":"index","type":"uint64"},{"internalType":"uint64","name":"typ","type":"uint64"},{"internalType":"string","name":"callFunc","type":"string"},{"internalType":"bytes[]","name":"args","type":"bytes[]"},{"internalType":"uint64","name":"txStatus","type":"uint64"},{"internalType":"bytes[]","name":"signatures","type":"bytes[]"},{"internalType":"bool","name":"isEncrypt","type":"bool"}],"name":"invokeInterchain","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[{"internalType":"string","name":"srcAddr","type":"string"},{"internalType":"string","name":"dstFullID","type":"string"},{"internalType":"uint64","name":"index","type":"uint64"},{"internalType":"uint64","name":"typ","type":"uint64"},{"internalType":"bytes[]","name":"result","type":"bytes[]"},{"internalType":"uint64","name":"txStatus","type":"uint64"},{"internalType":"bytes[]","name":"signatures","type":"bytes[]"}],"name":"invokeReceipt","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[],"name":"register","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"chainID","type":"string"},{"internalType":"string","name":"broker","type":"string"},{"internalType":"address","name":"ruleAddr","type":"address"},{"internalType":"bytes","name":"trustRoot","type":"bytes"}],"name":"registerAppchain","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"registerDirectTransaction","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"chainID","type":"string"},{"internalType":"string","name":"serviceID","type":"string"},{"internalType":"address[]","name":"whiteList","type":"address[]"}],"name":"registerRemoteService","outputs":[],"stateMutability":"nonpayable","type":"function"}]
```
上述示例中，合约部署地址为0x857133c5C69e6Ce66F7AD46F200B9B3573e77582，同时也打印出了合约部署生成的abi详情。

（4）以太坊合约调用

#### goduck ether contract invoke

- --address：可选参数，指定以太坊应用链地址，默认启动地址为http://localhost:8545

- --key-path：可选参数，指定部署合约账户私钥地址，默认为$goduck_repo/ethereum/account.key

- --psd-path：可选参数，指定部署合约账户密码地址，默认为$goduck_repo/ethereum/password

- --code-path：指定部署solidity合约地址，如有多个合约可以逗号隔开

调用示例如下
```shell
goduck ether contract invoke  --abi-path "#HOME/goduck/scripts/example/broker.abi  0x857133c5C69e6Ce66F7AD46F200B9B3573e77582 audit "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11^1"
```


#### 2.2.2 fabric

```shell
GoDuck fabric command [command options] [arguments...]
```

关于fabric应用链，链启动和合约部署是分开的操作

（1）启动fabric

```shell
goduck fabric start

# 启动成功后最后会打印出fabric的logo如下：
========= All GOOD, BYFN execution completed ===========


 _____   _   _   ____
| ____| | \ | | |  _ \
|  _|   |  \| | | | | |
| |___  | |\  | | |_| |
|_____| |_| \_| |____/

```

注意启动过程中可能需要科学上网。 如果出现报错或fabric-samples下载失败，建议删除fabric-samples文件，重新执行fabric启动命令。

（2）部署fabric合约
#### goduck fabric contract chaincode

参数解释

- --config-path：可选参数，指定fabric链的config.yaml文件，默认为$goduck_repo/fabric/config.yaml

- --code-path：可选参数，指定部署合约文件，默认为$goduck_repo/contracts，如合约文件不存在会根据--bxh-version参数下载相应版本的默认合约文件

- --bxh-version：指定连接的bitxhub版本，不同版本的bitxhub对应的跨链合约可能有差别

我们会提供已经准备好的fabric跨链合约contract文件，但部署时需要注明需要跨链的bitxhub版本，因为不同版本的bitxhub对应的跨链合约可能有查别。

示例如下
```shell
goduck fabric contract chaincode --bxh-version v1.23.0

# 部署成功后会依次打印出几个步骤的日志，日志中有部分报错不影响，只要没有红色报错，且能大致打印出以下日志即可：
install default interchain chaincode
===> Install chaincode
===> 1. Deploying broker, transfer and data_swapper chaincode
Installing chaincode broker on org[org2] peers:
-- grpcs://localhost:9051
-- grpcs://localhost:10051
...successfuly installed chaincode broker.v0 on peer localhost:10051.
...successfuly installed chaincode broker.v0 on peer localhost:9051.
...successfuly instantiated chaincode broker on channel mychannel.
Installing chaincode transfer on org[org2] peers:
-- grpcs://localhost:9051
-- grpcs://localhost:10051
...successfuly installed chaincode transfer.v0 on peer localhost:10051.
...successfuly installed chaincode transfer.v0 on peer localhost:9051.
...successfuly instantiated chaincode transfer on channel mychannel.
Installing chaincode data_swapper on org[org2] peers:
-- grpcs://localhost:9051
-- grpcs://localhost:10051
...successfuly installed chaincode data_swapper.v0 on peer localhost:10051.
...successfuly installed chaincode data_swapper.v0 on peer localhost:9051.
...successfuly instantiated chaincode data_swapper on channel mychannel.
===> 2. Set Alice 10000 amout in transfer chaincode
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: 
***** |  Response[1]:
***** |  |  Payload: 
****************************************************************************************************
===> 3. Set (key: path, value: /Users/weiyijie/.goduck) in data_swapper chaincode
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: 
***** |  Response[1]:
***** |  |  Payload: 
****************************************************************************************************
===> 4. Register transfer and data_swapper chaincode to broker chaincode
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: mychannel&transfer
***** |  Response[1]:
***** |  |  Payload: mychannel&transfer
****************************************************************************************************
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: mychannel&data_swapper
***** |  Response[1]:
***** |  |  Payload: mychannel&data_swapper
****************************************************************************************************
===> 6. Audit transfer and data_swapper chaincode
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: set status of chaincode mychannel&transfer to 1
***** |  Response[1]:
***** |  |  Payload: set status of chaincode mychannel&transfer to 1
****************************************************************************************************
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: set status of chaincode mychannel&data_swapper to 1
***** |  Response[1]:
***** |  |  Payload: set status of chaincode mychannel&data_swapper to 1
****************************************************************************************************
```

（3）fabric合约调用

#### goduck fabric contract invoke [chaincode_id] [function] [args(optional)]

参数解释

- --config-path：可选参数，指定fabric链的config.yaml文件，默认为$goduck_repo/fabric/config.yaml

示例如下：

```shell
#调用transfer合约的getbalance函数查询Alice的余额
goduck fabric contract invoke transfer getBalance Alice

[fabric] invoke function "getBalance", receipt is 10000
```

（4）关闭fabric
关闭fabric有stop和clean两个命令，推荐使用clean命令，在停止fabric容器的同时会清除所有相关镜像
```shell
# 关闭并清除应用链容器
goduck fabric stop

# 关闭并清除应用链容器，同时清除应用链及合约相关镜像
goduck fabric clean
```



