# Pier命令行

`pier`命令有多组子命令，其作用是与应用链或中继链交互，`pier`命令描述如下：

```shell
NAME:
   Pier - Manipulate the crosschain node

USAGE:
   pier [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
   appchain    Command about appchain in bitxhub
   id          Get appchain id
   init        Initialize pier local configuration
   interchain  Query interchain info
   network     Modify pier network configuration
   p2p         Command about p2p
   plugin      Initialize pier plugins configuration
   rule        Command about rule
   start       Start a long-running daemon process
   version     Show version about ap
   proposals   proposals manage command
   help, h     Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --repo value   Pier repository path
   --tls          enable tls between pier and bitxhub or not
   --help, -h     show help
   --version, -v  print the version

```

**参数解释**

- `--repo`：可选参数，指定pier节点配置文件所在目录，如果不指定，默认使用$(pwd)/.bitxhub目录。
- `--tls`：可选参数，用于pier节点与bitxhub进行tls加密传输。如果不指定，默认为false。
- `--version`：可选参数，用于显示当前pier版本。



## 子命令描述

### pier appchain

`pier appchain`命令用于中继模式下，跨链网关Pier向中继链bitXHub实现应用链相关信息的注册、更新、变更等操作。

**用法**

```shell
NAME:
   Pier appchain - Command about appchain in bitxhub

USAGE:
   Pier appchain command [command options] [arguments...]

COMMANDS:
   service   Command about appchain service
   register  Register appchain to bitxhub
   update    update appchain in bitxhub
   activate  activate appchain in bitxhub
   logout    logout appchain in bitxhub
   get       Get appchain info
```

**具体描述**

`pier appchain`具体描述与使用示例参考[中继模式管理](/v1.18/bitxhub/function/relay_manager/)。

### pier id

`pier id`命令用于获取所绑定的应用链地址。

**用法**

```
NAME:
   pier id - Get appchain id

USAGE:
   pier id [arguments...]
```

**具体描述**

**样例：**

```shell
$ pier --repo $(pwd)/.pier1 id 
# 控制台输出
0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb
```



### pier init

`pier init`命令用于初始化pier相关配置。如生成公私钥、ca证书等。

**用法**

```shell
NAME:
   Pier init - Initialize pier local configuration

USAGE:
   Pier init command [command options] [arguments...]

COMMANDS:
   relay   Initialize pier relay mode configuration
   direct  Initialize pier direct mode configuration
   union   Initialize pier union mode configuration

OPTIONS:
   --algo value         Specify crypto algorithm (default: "Secp256k1")
   --http-port value    Specify http port (default: 44544)
   --pprof-port value   Specify pprof port (default: 44555)
   --enable-tls         Enable TLS or not
   --tlsca value        Specify TLS CA certificate path (default: "certs/agency.cert")
   --common-name value  Specify common name to verify (default: "BitXHub")
   --ha value           Specify if pier will run in single mode or high availability mode (default: "single")
   --help, -h           show help
```

**具体描述**

**参数解释**

- `--algo`:指定加密算法类型，目前支持RSA、SM2、ECDSA_P256、Secp256k1等常见的加密算法，默认为Secp256k1。

- `--http-port`：可选参数，指定监听的http端口号，默认为44544。
- `--pprof-port`：可选参数，指定pprof的端口号，默认为44555.
- `--enable-tls`：可选参数，是否进行TLS加密传输，默认为false。
- `--tlsca` ：可选参数，TLS CA路径，默认为certs/ca.pem。
- `--common-name` ：可选参数，指定ca证书的域名。默认为localhost。
- `--ha`：可选参数，选择网关是否高可用，高可用功能为商业版本，默认只有单个网关。



#### pier init delay 

中继模式网关初始化，命令参数如下：

```shell
NAME:
   Pier init relay - Initialize pier relay mode configuration

USAGE:
   Pier init relay [command options] [arguments...]

OPTIONS:
   --addrs value       Specify bitxhub nodes' address (default: "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014")
   --quorum value      Specify the quorum number of BitXHub (default: 2)
   --validators value  Specify validators of bitxhub (default: "0x000f1a7a08ccc48e5d30f80850cf1cf283aa3abd", "0xe93b92f1da08f925bdee44e91e7768380ae83307", "0xb18c8575e3284e79b92100025a31378feb8100d6", "0x856E2B9A5FA82FD1B031D1FF6863864DBAC7995D")
```

**具体描述**

**参数解释**

- `--addrs`：可选参数，指定bixhub各节点地址及端口号。
- `--quorum`：可选参数，指定bixhub集群的quorum个数，与具体共识算法与节点规模相关。
- `--validators`：可选参数，指定bitxhub管理员节点地址。



#### pier init direct 

直连模式网关初始化，命令参数如下：

```shell
NAME:
   Pier init direct - Initialize pier direct mode configuration

USAGE:
   Pier init direct [command options] [arguments...]
```

**具体描述**

**参数解释**

- `--addPier`：可选参数，指定直连模式下pier的网络信息（包含自己），可暂不设置，一般init过后，在pier network中配置。

**样例：**

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
# 初始化pier1相关配置，配置文件目录为$(pwd)/.pier1

$ pier --repo .pier1 init direct
```



#### Pier init union

大规模模式下的初始化配置，命令如下：

```shell
NAME:
   Pier init union - Initialize pier union mode configuration

USAGE:
   Pier init union [command options] [arguments...]

OPTIONS:
   --addrs value       Specify bitxhub nodes' address (default: "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014")
```

**具体描述**

**参数解释**

- `--addrs`：可选参数，指定bixhub各节点地址及端口号。

**样例：**

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
# 初始化pier1相关配置，配置文件目录为$(pwd)/.pier1

$ PID1=$(pier --repo .pier1 p2p id)
$ PID2=$(pier --repo .pier1 p2p id)

$ pier --repo .pier1 init union --peers /ip4/127.0.0.1/tcp/3001/p2p/$PID1
```



### pier interchain

`pier interchain`命令用于查询应用链的跨链交易信息。

**用法**

```shell
NAME:
   Pier interchain - Query interchain info

USAGE:
   Pier interchain command [command options] [arguments...]

COMMANDS:
   ibtp  Query ibtp by id

OPTIONS:
   --key value  Specific key.json path
   --help, -h   show help
```

**具体描述**

**参数解释**

- `--key`：必选参数指定pier的key.json文件。

**子命令**

#### Pier interchain ibtp

```
NAME:
   Pier interchain ibtp - Query ibtp by id

USAGE:
   Pier interchain ibtp [command options] [arguments...]

OPTIONS:
   --id value  Specific ibtp id
```

**样例：**

```shell
$ pier --repo $(pwd)/.pier1 interchain --key $(pwd)/.pier1/key.json ibtp --id 0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb-0x23Fb0E7eF676467563d37D820F1b1Fddb0f9a2E1-1

# 控制台输出
INFO[16:52:41.917] Establish connection with bitxhub localhost:60013 successfully  module=rpcx
Tx hash: 0x82d3434e0a295B22A8e55884FEdd137d19e8cBDc32d5B593AD6D9AFB5cf59600
tx:<from:<RawAddress:[255 129 153 250 228 140 128 139 69 102 125 160 204 170 238 131 155 26 16 203] Address:"" > to:<RawAddress:[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 10] Address:"" > timestamp:1638435054429909000 transaction_hash:<RawHash:[130 211 67 78 10 41 91 34 168 229 88 132 254 221 19 125 25 232 203 220 50 213 181 147 173 109 154 251 92 245 150 0] Hash:"" > IBTP:<from:"0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb" to:"0x23Fb0E7eF676467563d37D820F1b1Fddb0f9a2E1" index:1 timestamp:1638435054428680000 proof:"k\206\262s\3774\374\341\235k\200N\377Z?WG\255\244\352\242/\035I\300\036R\335\267\207[K" payload:"\022\221\001\n*0x5bFe03Dbd09817d4957693f672cc31A133Bb6084\022*0xA5dD12E27Ee5E79cE0B50adb376414351C8eea5f\032\020interchainCharge\"\001A\"\001B\"\002102\000:\022interchainRollbackB\001AB\00210" > nonce:4 signature:"T)\313\333~\316f\372.\010\235\234\222J\314@<\202\260\243\367b\035\016\366\326y\3515\020\222\256$\010\314\035/\271H\217|~\302\233\353b\275\267ir\t\242\376\224\307q\217l\232\037DP\n\317\000" extra:"1" > tx_meta:<block_hash:"\326{90^\355a\236\342\235i\302\374+\352VI\030\242\211\335x\371w\221>(\254%o\212\222" block_height:14 >
```



### pier network

`pier network`命令用于配置直连模式或者大规模模式下节点的网络信息。

**用法**

```
NAME:
   Pier network - Modify pier network configuration

USAGE:
   Pier network command [command options] [arguments...]

COMMANDS:
   direct  Modify pier direct mode network configuration
   union   Modify pier union mode network configuration
```

**子命令**

#### pier network relay

用于中继模式的bitxhub节点修改，命令描述如下：

```shell
NAME:
   Pier network relay - Modify pier relay mode network configuration

USAGE:
   Pier network relay [command options] [arguments...]

OPTIONS:
   --addNodes value  add bitxhub node address
   --delNodes value  delete bitxhub node address
```

**具体描述**

**<a name="directAnchor">**参数解释**</a>**

- `--addNodes`：可选参数，指定新增的bixhub节点地址及端口号，如localhost:60015。
- `--delNodes`：可选参数，指定需要删除的bixhub节点地址及端口号，如localhost:60011。

**样例：**

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
$ pier --repo $(pwd)/.pier1 network relay --addNodes localhost:60015

# 查看配置文件是否有更改
$ cat $(pwd)/.pier1/pier.toml
…………
[mode.relay]
	addrs = ["localhost:60015", "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014"]
…………
```

可以看到配置文件pier.toml下的addr在原有的4个节点基础上新增了localhost:60015。



#### pier network direct

用于直连模式的network.toml配置，命令描述如下：

```shell
NAME:
   Pier network direct - Modify pier direct mode network configuration

USAGE:
   Pier network direct [command options] [arguments...]

OPTIONS:
   --addPier value  add pier address and pid, split with "#"
   --delPier value  delete pier address and pid, split with "#"
```

**具体描述**

<a name="directAnchor">**参数解释**</a>

- `--addPier`：必选选参数，添加pier的网络配置（包括自己），如：127.0.0.1:5554#QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ。
- `--delPier`：必选参数，指定需要删除的bixhub节点地址及端口号，如127.0.0.1:5554#QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ。

**样例：**

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
# 获取pier1和pier2的pid
$ PID1=$(pier --repo $(pwd)/.pier1 p2p id)
$ PID2=$(pier --repo $(pwd)/.pier2 p2p id)

# 修改pier1的network.toml
$ pier --repo $(pwd)/.pier1 network direct --addPier 127.0.0.1:5554#$PID1 --addPier 127.0.0.1:5555#$PID2

# 修改pier2的network.toml
$ pier --repo $(pwd)/.pier2 network direct --addPier 127.0.0.1:5554#$PID1 --addPier 127.0.0.1:5555#$PID2

# 查看配置文件是否有更改
$ cat .pier1/network.toml

[[piers]]
  hosts = ["/ip4/127.0.0.1/tcp/5554/p2p/"]
  pid = "QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ"

[[piers]]
  hosts = ["/ip4/127.0.0.1/tcp/5555/p2p/"]
  pid = "Qmd5VnC2j5Mix28eqVPEm5gWKKSp8nQWkxq2DE1DecEzXX"
```



#### Pier network union

用于大规模模式的bitxhub节点修改与network.toml配置，命令描述如下：

```shell
NAME:
   Pier network union - Modify pier union mode network configuration

USAGE:
   Pier network union [command options] [arguments...]

OPTIONS:
   --addNodes value  add bitxhub node address
   --delNodes value  delete bitxhub node address
   --addPier value   add pier address and pid, split with "#"
   --delPier value   add pier address and pid, split with "#"
```

**具体描述**

参考<a href="/v1.18/bitxhub/introduction/summary/#relayAnchor">pier network relay</a> 与<a href="/v1.18/bitxhub/introduction/summary/#directAnchor">pier network direct</a> 。



### pier p2p

`pier p2p`命令用于直连模式下pier网络通信的pid。

**用法**

```
NAME:
   Pier p2p - Command about p2p

USAGE:
   Pier p2p command [command options] [arguments...]

COMMANDS:
   id  get pier unique id in p2p network
```

**具体描述**

**样例：**

```shell
# 初始化pier1相关配置，配置文件目录为$(pwd)/.pier1
$ pier --repo $(pwd)/.pier1 p2p id 

#控制台输出
QmNpQvh8Lm7gwt3sDqwJ1pgNn6taJLqdgtRVyZWhsD5bND
```



### pier plugin

`pier plugin`命令用于配置应用链相关信息。

**用法**

```shell
NAME:
   Pier plugin - Initialize pier plugins configuration

USAGE:
   Pier plugin command [command options] [arguments...]

COMMANDS:
   fabric    Initialize pier and fabric plugin configuration
   ethereum  Initialize pier and ethereum plugin configuration

OPTIONS:
   --redownload  Re-download plugin or not
   --help, -h    show help
```

**具体描述**

**参数解释**

- `--redownload`：可选参数，是否重新下载插件，默认为true。

**子命令**

#### pier plugin fabric

配置fabric插件相关配置信息，并下载`pier-client-fabric`二进制文件，命令描述如下：

```shell
NAME:
   Pier plugin fabric - Initialize pier and fabric plugin configuration

USAGE:
   Pier plugin fabric [command options] [arguments...]

OPTIONS:
   --crypto-config value  Specify the path to crypto-config directory
   --config value         Specify the path to fabric config.yaml
   --event-filter value   Specify the event filter on fabric chaincode
   --username value       Specify the username to invoke fabric chaincode
   --ccid value           Specify chaincode id to invoke
   --channel-id value     Specify channel id
   --org value            Specify the organization
```

**参数解释**

- `--crypto-config`：必选参数，指定fabric的文件目录，一般与repo目录一致。

- `--config`：可选参数，指定fabric的config.yaml文件目录，默认在$repo/fabric目录下。
- `--event-filter`：可选参数，根据eventName过滤所需的事件。
- `--username`：可选参数，指定调用合约的用户名，默认为Admin。
- `--ccid`：可选参数，CCID 应匹配fabric的peer节点上链码的包名。
- `--channel-id`：可选参数，fabric链码部署所在的通道名。
- `--org`：可选参数，fabric上指定的peer节点所属的组织。

**样例：**

```shell
# 注意，由于安装包的地址为github，下载可能较慢，可以重试几次或通过设置代理
$ pier --repo .pier2 plugin fabric --crypto-config $(pwd)/.pier2

Start downloading https://github.com/meshplus/pier-client-fabric/releases/download/v1.18.1/fabric-client-v1.18.1-darwin
Finish downloading https://github.com/meshplus/pier-client-fabric/releases/download/v1.18.1/fabric-client-v1.18.1-darwin
```



#### pier plugin ethereum

配置ethereum插件相关配置信息，并下载`pier-client-ethereum`二进制文件，命令描述如下：

```shell
NAME:
   Pier plugin ethereum - Initialize pier and ethereum plugin configuration

USAGE:
   Pier plugin ethereum [command options] [arguments...]

OPTIONS:
   --addr value            Specify ethereum websocket address
   --broker value          Specify ethereum broker contract address
   --key value             Specify the ethereum key to sign the transaction
   --password value        Specify the password of the key
   --min-confirm value     Specify minimum blocks to confirm the transaction (default: 0)
   --transfer value        Specify the transfer contract address
   --data-swapper value    Specify the data swapper contract address
   --timeout-height value  Specify the ibtp timeout block height
```

**参数解释**

- `--addr`：以太坊的websocket地址，如果是自己搭建的私链，一般为ws://localhost:8546。
- `--broker`：必选参数，broker合约部署地址。
- `--key`：以太坊用于交易签名的账户私钥。默认为repo目录下的account.key，如果是自己搭建的私链，可以将datadir/keystore下的私钥复制到account.key中。
- `--password`：以太坊账户密码。
- `--min-confirm`：最小确认数，用于本地测试建议设置为0。
- `--transfer`：transfer合约地址。
- `--data-swapper`：data swapper合约地址。
- `--timeout-height`：设置超时块高H，如跨链交易在中继链的区块高度为h的区块上链后，h+H的高度还未收到目的链回执，将触发超时回滚。

**样例：**

```shell
# 注意，由于安装包的地址为github，下载可能较慢，可以重试几次或通过设置代理
$ pier --repo $(pwd)/.pier1 plugin ethereum --broker 0x8d0522826c787c20fEDA2A2E3B3557D9644a1CE5 --addr ws://localhost:8546 --key $(pwd)/.pier1/ethereum/account.key --password password --min-confirm 0 --timeout-height 10

Start downloading https://github.com/meshplus/pier-client-ethereum/releases/download/v1.18.0/eth-client-v1.18.0-darwin
Finish downloading https://github.com/meshplus/pier-client-ethereum/releases/download/v1.18.0/eth-client-v1.18.0-darwin
```



### <a name="ruleAnchor">pier rule</a>

`pier rule`命令用于中继模式下pier向中继链部署应用链验证规则。

**用法**

```shell
NAME:
   Pier rule - Command about rule

USAGE:
   Pier rule command [command options] [arguments...]

COMMANDS:
   deploy    Deploy validation rule
   register  Register validation rule
   update    update master rule
   logout    logout validation rule
```

**子命令**

#### pier rule deploy

**用法**

```shell
NAME:
   Pier rule deploy - Deploy validation rule

USAGE:
   Pier rule deploy [command options] [arguments...]

OPTIONS:
   --path value  Specify rule path
```

**具体描述**

**参数解释**

- `--path`:指定wasm验证规则路径。

**样例**

以`pier-client-fabric`项目下的验证规则举例说明。

```shell
# 复制验证规则到当前的fabric目录下
$ cp ~/goproject/meshplus/pier-client-fabric/config/validating.wasm ./fabric/

$ pier --repo $(pwd) rule deploy --path ./fabric/validating.wasm
# 控制台输出验证规则部署成功，输出验证规则地址用于验证规则注册或绑定应用链
Deploy rule successfully: 0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB
```

#### pier rule register

对于已注册的应用链，`pier rule register`可以注册多个验证规则，具体说明如下：

**用法**

```shell
NAME:
   Pier rule register - Register validation rule

USAGE:
   Pier rule register [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
   --rule value         Specify appchain rule addr
   --rule-url value     Specify appchain rule url
```

**具体描述**

**参数解释**

- `--appchain-id`：指定当前应用链的id。
- `--rule`：指定验证规则地址，通过`pier rule deploy`生成。
- `--rule-url`：验证规则的url地址，如果没有可以填写任意网址。

**样例**

以`pier-client-fabric`项目下的验证规则举例说明。

```shell
# 通过上诉pier rule deploy再次部署验证规则后，得到验证规则地址
$ pier --repo $(pwd) rule deploy --path ./fabric/validating.wasm
# 控制台输出验证规则部署成功，输出验证规则地址用于验证规则注册
Deploy rule successfully: 0x56feA5a7cd034E35a6ac2f42ffb914026971D0bd

# 为应用链注册当前验证规则
$ pier --repo $(pwd) rule register --appchain-id fabappchain --rule 0x56feA5a7cd034E35a6ac2f42ffb914026971D0bd --rule-url https://github.com

Register rule to bitxhub for appchain fabappchain successfully.
```



#### pier rule update

`pier rule update`命令用于更新应用链所绑定的验证规则。

注意：更新的验证规则需要通过`pier rule deploy`和`pier rule register`提前在中继链部署和注册）。

**用法**

```shell
NAME:
   Pier rule update - update master rule

USAGE:
   Pier rule update [command options] [arguments...]

OPTIONS:
   --addr value         Specify rule addr
   --appchain-id value  Specify appchain id
   --reason value       Specify governance reason
```

**具体描述**

**参数解释**

- `--addr`：指定验证规则地址，通过`pier rule deploy`生成。

- `--appchain-id`：指定当前应用链的id。
- `--reason`：验证规则更新相关说明。

**样例**

以`pier-client-fabric`项目下的验证规则举例说明。

```shell
# 通过上诉pier rule register注册验证规则后，得到验证规则地址，并将该应用链绑定的验证规则更新为新注册的验证规则
$ pier --repo $(pwd) rule update --appchain-id fabappchain --addr 0x56feA5a7cd034E35a6ac2f42ffb914026971D0bd --reason "test update rule"
# 控制台输出验证规则更新成功，输出更新验证规则的提案号用于中继链管理员投票
Update master rule to bitxhub for appchain fabappchain successfully, wait for proposal 0x0df6746854682fc8ce5A7729BBB14c7652ae4516-1 to finish.

# 在中继链上查询该应用链所有的验证规则列表
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance rule all --id "fabappchain"
ChainID      RuleAddress                                 Status     Master  CreateTime
-------      -----------                                 ------     ------  ----------
fabappchain  0x00000000000000000000000000000000000000a2  bindable   false   1643101796307170000
fabappchain  0x00000000000000000000000000000000000000a0  bindable   false   1643101796307170000
fabappchain  0x00000000000000000000000000000000000000a1  bindable   false   1643101796307170000
fabappchain  0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB  bindable   false   1643101796307170000
fabappchain  0x56feA5a7cd034E35a6ac2f42ffb914026971D0bd  available  true    1643103182256151000
```

其中Master标识应用链目前绑定的验证规则，可以发现绑定的验证规则已更新为0x56feA5a7cd034E35a6ac2f42ffb914026971D0bd。说明验证规则更新成功。

#### pier rule logout

**用法**

```shell
NAME:
   Pier rule logout - logout validation rule

USAGE:
   Pier rule logout [command options] [arguments...]

OPTIONS:
   --addr value         Specify rule addr
   --appchain-id value  Specify appchain id
```

**具体描述**

**参数解释**

- `--addr`：指定验证规则地址，通过`pier rule deploy`生成。

- `--appchain-id`：指定当前应用链的id。

**样例**

将上述被替换的地址为0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB验证规则注销。

```shell
# 通过上诉pier rule register注册验证规则后，得到验证规则地址，并将该应用链绑定的验证规则更新为新注册的验证规则
$ pier --repo $(pwd) rule logout --appchain-id fabappchain --addr 0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB

# 控制台输出验证规则注销成功
The logout request was submitted successfully

# 在中继链上查询该应用链所有的验证规则列表
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance rule all --id "fabappchain"
ChainID      RuleAddress                                 Status     Master  CreateTime
-------      -----------                                 ------     ------  ----------
fabappchain  0x00000000000000000000000000000000000000a2  bindable   false   1643101796307170000
fabappchain  0x00000000000000000000000000000000000000a0  bindable   false   1643101796307170000
fabappchain  0x00000000000000000000000000000000000000a1  bindable   false   1643101796307170000
fabappchain  0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB  forbidden  false   1643101796307170000
fabappchain  0x56feA5a7cd034E35a6ac2f42ffb914026971D0bd  available  true    1643103182256151000
```

其中Status标识应用链目前所注册的验证规则的状态，可以发现地址为0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB的验证规则状态已被更改为forbitdden。说明该验证规则注销成功。

### pier start

`pier start`命令用于启动pier。

**用法**

```
NAME:
   pier start - Start a long-running daemon process

USAGE:
   pier start [arguments...]
```

**具体描述**

**样例**：

```shell
$ pier --repo $(pwd)/.pier2 start
Pier version: dev-release-1.18-3826f39
App build date: 2022-01-06T17:04:07
System version: darwin/amd64
Golang version: go1.16.6

2022-01-06T19:50:21.504+0800 [DEBUG] plugin: starting plugin: path=/bin/sh args="[sh -c /Users/liruoxin/testDirect/release1.18/.pier2/plugins/eth-client start]"
2022-01-06T19:50:21.506+0800 [DEBUG] plugin: plugin started: path=/bin/sh pid=22551
2022-01-06T19:50:21.506+0800 [DEBUG] plugin: waiting for RPC address: path=/bin/sh
2022-01-06T19:50:21.549+0800 [DEBUG] plugin: using plugin: version=4
2022-01-06T19:50:21.549+0800 [DEBUG] plugin.sh: 2022-01-06T19:50:21.549+0800 [DEBUG] client: plugin address: network=unix address=/var/folders/xr/pnhf9j6n2k5_fysm6s0xkgt00000gn/T/plugin713572722
2022-01-06T19:50:21.550+0800 [TRACE] plugin.stdio: waiting for stdio data
2022-01-06T19:50:21.551+0800 [DEBUG] plugin.sh: 2022-01-06T19:50:21.551+0800 [INFO ] client: Basic appchain info: broker address=0xFb23Af09e3E8D83fd5575De9558920Bf351F05E8 ethereum node ip=ws://localhost:8546
Pprof on localhost:44556

2022-01-06 19:50:21 [INFO] [/Users/liruoxin/goproject/meshplus/pier/internal/app/pier.go:233] pier HA manager start
2022-01-06T19:50:21.624+0800 [DEBUG] plugin.sh: 2022-01-06T19:50:21.624+0800 [INFO ] client: Consumer started
INFO[2022-01-06T19:50:21.624] appchain adapter start                        module=appchain_adapter
INFO[2022-01-06T19:50:21.625] start p2p success                             module=lightp2p
INFO[2022-01-06T19:50:21.637] Connect successfully                          address:=appchain1 module=peer_mgr node=QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ
INFO[2022-01-06T19:50:21.637] direct adapter start                          module=direct_adapter
INFO[2022-01-06T19:50:21.654] Start To Recover IBTPs!                       module=exchanger
INFO[2022-01-06T19:50:21.655] End To Recover IBTPs!                         module=exchanger
INFO[2022-01-06T19:50:21.655] Exchanger started                             module=exchanger
INFO[2022-01-06T19:50:21.655] listenIBTPFromSrcAdaptToServicePairCh appchain:appchain2 Start!  module=exchanger
INFO[2022-01-06T19:50:21.655] listenIBTPFromDestAdaptToServicePairCh direct:appchain2 Start!  module=exchanger
…………
```



### pier version

`pier version`命令用于查询版本号。

**用法**

```shell
NAME:
   pier version - Show version about ap

USAGE:
   pier version [arguments...]
```

**具体描述**

**样例**：

```shell
$ pier --repo $(pwd)/.pier1 version

Pier version: dev-release-1.18-3826f39
App build date: 2022-01-06T17:04:07
System version: darwin/amd64
Golang version: go1.16.6
```



### pier propsals 

`pier proposals`命令用于撤销指定的提案。

**用法**

```shell
NAME:
   Pier proposals - proposals manage command

USAGE:
   Pier proposals command [command options] [arguments...]

COMMANDS:
   withdraw  withdraw a proposal

```

**子命令**

#### Pier proposals withdraw

```shell
NAME:
   Pier proposals withdraw - withdraw a proposal

USAGE:
   Pier proposals withdraw [command options] [arguments...]

OPTIONS:
   --admin-key value  Specific admin key path
   --id value         proposal id
   --reason value     Specify governance reason

```

**样例**

当想要撤销某个提案时（如注册应用链的提案）使用如下命令。其中，proposal id 为想要撤销的提案号，提案生成参考[中继模式应用链管理](/v1.18/bitxhub/function/relay_manager/)。

```shell
# 应用链所在网关撤销提案
$ pier --repo $(pwd) proposals withdraw --admin-key ./key.json --id 0x450884c9F7fdFc72E2bC1245306d15dE1750A880-0 --reason "withdraw proposal"

# 撤销提案后，在中继链查询提案状态可以看到提案状态为：withdrawn by the proposal sponsor
$ bitxhub client governance proposal query -id 0x450884c9F7fdFc72E2bC1245306d15dE1750A880-0
========================================================================================
Id                                            ManagedObjectId               Type         EventType  Status  A/R  IE/AE/TE  Special/Super  CreateTime           Description  EndReason
--                                            ---------------               ----         ---------  ------  ---  --------  -------------  ----------           -----------  ---------
0x450884c9F7fdFc72E2bC1245306d15dE1750A880-0  did:bitxhub:fabricappchain:.  AppchainMgr  register   reject  0/0  4/4/3     false/false    1643088788665643000               withdrawn by the proposal sponsor
========================================================================================
* A/R：approve num / reject num
* IE/AE/TE：the total number of electorate at the time of the initial proposal / the number of available electorate currently /the minimum threshold for votes to take effect
* Special/Super：is special proposal / is super admin voted
```

