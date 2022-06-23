# pier

`pier`命令有多组子命令，其作用是与应用链或中继链交互，`pier`命令描述如下：

```shell
$ pier -h

NAME:
   Pier - A Gateway Used To Cross the Appchain

USAGE:
   pier [global options] command [command options] [arguments...]

VERSION:
   Pier version: dev-master-dae0511


COMMANDS:
   appchain  Command about appchain in bitxhub
   id        Get appchain id
   init      Initialize pier local configuration
   network   Modify pier network configuration
   p2p       Command about p2p
   plugin    Initialize pier plugins configuration
   start     Start a long-running daemon process
   version   Show version about pier
   help, h   Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --repo value   Pier repository path
   --tls          enable tls between pier and bitxhub or not
   --help, -h     show help
   --version, -v  print the version
```

**参数解释**：

- `--repo`：可选参数，指定pier节点配置文件所在目录，如果不指定，默认使用`~/.pier`目录。
- `--tls`：可选参数，用于pier节点与bitxhub进行tls加密传输。如果不指定，默认为false。
- `--version`：可选参数，用于显示当前pier版本。

**子命令**：

## 1. pier appchain

`pier appchain`命令用于中继模式下，跨链网关Pier向中继链bitXHub实现应用链相关信息的注册、更新、变更等操作。

```shell
$ pier appchain -h

NAME:
   Pier appchain - Command about appchain in bitxhub

USAGE:
   Pier appchain command [command options] [arguments...]

COMMANDS:
   service     Command about appchain service
   register    Register appchain to bitxhub
   update      update appchain in bitxhub
   activate    activate appchain in bitxhub
   logout      logout appchain in bitxhub
   get         Get appchain info
   interchain  Query interchain info
   rule        Command about rule
   proposals   proposals manage command

OPTIONS:
   --help, -h  show help
```

`pier appchain`具体描述与使用示例参考[中继模式管理](../../../function/relay_manager/)。

## 2. pier id

`pier id`命令用于获取所绑定的应用链地址。

```shell
$ pier id -h

NAME:
   pier id - Get appchain id

USAGE:
   pier id [arguments...]
```

**示例**：

```shell
# pier配置文件目录路径。若不指定，则默认 ~/.pier 目录
$ pier --repo xxx/.pier id

0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb
```

## 3. pier init

`pier init`命令用于初始化pier相关配置，若不指定`--repo`，则默认会在`~/.pier`目录下生成公私钥、ca证书等配置文件。

```shell
$ pier init -h

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

**参数解释**：

- `--algo`:指定加密算法类型，目前支持RSA、SM2、ECDSA_P256、Secp256k1等常见的加密算法，默认为Secp256k1。
- `--http-port`：可选参数，指定监听的http端口号，默认为44544。
- `--pprof-port`：可选参数，指定pprof的端口号，默认为44555.
- `--enable-tls`：可选参数，是否进行TLS加密传输，默认为false。
- `--tlsca` ：可选参数，TLS CA路径，默认为certs/ca.pem。
- `--common-name` ：可选参数，指定ca证书的域名。默认为localhost。
- `--ha`：可选参数，选择网关是否高可用，高可用功能为商业版本，默认只有单个网关。

**子命令**：

### pier init relay

初始化中继模式下pier节点的配置。

```shell
$ pier init relay -h

NAME:
   Pier init relay - Initialize pier relay mode configuration

USAGE:
   Pier init relay [command options] [arguments...]

OPTIONS:
   --addrs value   Specify bitxhub nodes' address (default: "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014")
   --quorum value  Specify the quorum number of BitXHub (default: "2")
```

**参数解释**：

- `--addrs`：可选参数，指定bixhub各节点地址及端口号。
- `--quorum`：可选参数，指定bixhub集群的quorum个数，与具体共识算法与节点规模相关。

**示例**：

```shell
# 若不指定 --repo ，则默认 ~/.pier 目录
$ pier --repo xxx/.pier init relay
```

### pier init direct

初始化直连模式pier节点的配置。

```shell
$ pier init direct -h

NAME:
   Pier init direct - Initialize pier direct mode configuration

USAGE:
   Pier init direct [command options] [arguments...]

OPTIONS:
   --addPier value  Specify counter party piers to connect, input looks like: [ip]:[port]#[Pid]
```

**参数解释**：

- `--addPier`：可选参数，指定直连模式下pier的网络信息（包含自己），可暂不设置，一般init过后，在pier配置目录下的network.toml中配置。

**示例**：

```shell
# 若不指定 --repo ，则默认 ~/.pier 目录
$ pier --repo xxx/.pier init direct
```

### pier init union

初始化大规模模式下pier节点的配置。

```shell
$ pier init union -h

NAME:
   Pier init union - Initialize pier union mode configuration

USAGE:
   Pier init union [command options] [arguments...]

OPTIONS:
   --addrs value    Specify bitxhub nodes' address (default: "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014")
   --addPier value  Specify the remote union piers to connect, input looks like: [ip]:[port]#[Pid]
```

**参数解释**：

- `--addrs`：可选参数，指定bixhub各节点地址及端口号。
- `--addPier`：可选参数，指定直连模式下pier的网络信息（包含自己），可暂不设置，一般init过后，在pier配置目录下的network.toml中配置。

**示例**：

```shell
# 若不指定 --repo ，则默认 ~/.pier 目录
$ pier --repo xxx/.pier init union
```

## 4. pier network

`pier network`命令用于配置直连模式或者大规模模式下节点的网络信息。

```shell
$ pier network -h

NAME:
   Pier network - Modify pier network configuration

USAGE:
   Pier network command [command options] [arguments...]

COMMANDS:
   relay   Modify pier relay mode network configuration
   direct  Modify pier direct mode network configuration
   union   Modify pier union mode network configuration

OPTIONS:
   --help, -h  show help
```

**子命令**：

### pier network relay

用于修改中继模式下pier节点的network.toml配置。

```shell
$ pier network relay -h

NAME:
   Pier network relay - Modify pier relay mode network configuration

USAGE:
   Pier network relay [command options] [arguments...]

OPTIONS:
   --addNodes value  add bitxhub node address
   --delNodes value  delete bitxhub node address
```

**参数解释**：

- `--addNodes`：可选参数，指定新增的bixhub节点地址及端口号，如localhost:60015。
- `--delNodes`：可选参数，指定需要删除的bixhub节点地址及端口号，如localhost:60011。

**示例**：

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
$ PIER_CONFIG_PATH="xxx/.pier"
$ pier --repo $PIER_CONFIG_PATH network relay --addNodes localhost:60015

# 查看配置文件是否有更改
$ cat $PIER_CONFIG_PATH/pier.toml
…………
[mode.relay]
    addrs = ["localhost:60015", "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014"]
…………
```

可以看到配置文件pier.toml下的addr在原有的4个节点基础上新增了localhost:60015。

### pier network direct

用于直连模式下pier节点的network.toml配置。

```shell
$ pier network direct -h

NAME:
   Pier network direct - Modify pier direct mode network configuration

USAGE:
   Pier network direct [command options] [arguments...]

OPTIONS:
   --addPier value  add pier address and pid, split with "#"
   --delPier value  delete pier address and pid, split with "#"
```

**参数解释**：

- `--addPier`：必选选参数，添加pier的网络配置（包括自己），如：127.0.0.1:5554#QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ。其中地址为pier启动时所在地址，端口号可任意指定可用端口。
- `--delPier`：必选参数，指定需要删除的bixhub节点地址及端口号，如127.0.0.1:5554#QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ。

**示例**：

```shell
# pier1 pier2配置文件目录路径，pier init时生成
$ PIER1_CONFIG_PATH="xxx/.pier1"
$ PIER2_CONFIG_PATH="xxx/.pier2"

# 获取pier1和pier2的pid
$ PID1=$(pier --repo $PIER1_CONFIG_PATH p2p id)
$ PID2=$(pier --repo $PIER2_CONFIG_PATH p2p id)

# 修改pier1的network.toml
$ pier --repo $PIER1_CONFIG_PATH network direct --addPier "127.0.0.1:5554#$PID1" --addPier "127.0.0.1:5555#$PID2"

# 修改pier2的network.toml
$ pier --repo $PIER2_CONFIG_PATH network direct --addPier "127.0.0.1:5554#$PID1" --addPier "127.0.0.1:5555#$PID2"

# 查看配置文件是否有更改
$ cat $PIER1_CONFIG_PATH/network.toml
$ cat $PIER2_CONFIG_PATH/network.toml

[[piers]]
  hosts = ["/ip4/127.0.0.1/tcp/5554/p2p/"]
  pid = "QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ"

[[piers]]
  hosts = ["/ip4/127.0.0.1/tcp/5555/p2p/"]
  pid = "Qmd5VnC2j5Mix28eqVPEm5gWKKSp8nQWkxq2DE1DecEzXX"
```

### pier network union

用于大规模模式的bitxhub节点修改与network.toml配置，命令描述如下：

```shell
$ pier network union -h

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

**示例**：

参考 pier network rela 与 pier network direct 的示例。

## 5. pier p2p

`pier p2p`命令用于直连模式下pier网络通信的pid。

```shell
$ pier p2p -h

NAME:
   Pier p2p - Command about p2p

USAGE:
   Pier p2p command [command options] [arguments...]

COMMANDS:
   id  get pier unique id in p2p network

OPTIONS:
   --help, -h  show help
```

**示例**：

```shell
$ pier --repo xxx/.pier p2p id

QmNpQvh8Lm7gwt3sDqwJ1pgNn6taJLqdgtRVyZWhsD5bND
```

## 6. pier plugin

`pier plugin`命令用于配置应用链插件相关信息。

```shell
$ pier plugin -h

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

**参数解释**：

- `--redownload`：可选参数，是否重新下载插件，默认为true。

**子命令**：

### pier plugin fabric

配置fabric插件相关配置信息，并下载`pier-client-fabric`二进制文件。

```shell
$ pier plugin fabric -h

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

**参数解释**：

- `--crypto-config`：必选参数，指定fabric的文件目录，一般与repo目录一致，需传入绝对路径；
- `--config`：必选参数，指定fabric的config.yaml文件目录，默认在repo/fabric目录下，需传入绝对路径；
- `--event-filter`：可选参数，根据eventName过滤所需的事件；
- `--username`：可选参数，指定调用合约的用户名，默认为Admin；
- `--ccid`：可选参数，CCID 应匹配fabric的peer节点上链码的包名；
- `--channel-id`：可选参数，fabric链码部署所在的通道名；
- `--org`：可选参数，fabric上指定的peer节点所属的组织。

**示例**：

```shell
# 注意，由于安装包的地址为github，下载可能较慢，可以重试几次或通过设置代理
$ PIER_CONFIG_PATH="/xxx/xxx/.pier" # 此处需使用绝对路径
$ pier --repo $PIER_CONFIG_PATH plugin fabric --crypto-config $PIER_CONFIG_PATH --config $PIER_CONFIG_PATH/fabric

Start downloading https://github.com/meshplus/pier-client-fabric/releases/download/v1.18.1/fabric-client-v1.18.1-darwin
Finish downloading https://github.com/meshplus/pier-client-fabric/releases/download/v1.18.1/fabric-client-v1.18.1-darwin
```

### pier plugin ethereum

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

**参数解释**：

- `--addr`：以太坊的websocket地址，如果是自己搭建的私链，一般为ws://localhost:8546；
- `--broker`：必选参数，broker合约部署地址；
- `--key`：以太坊用于交易签名的账户私钥。若不指定，可自行将账户私钥复制到repo/ethereum目录下的account.key文件中；
- `--password`：以太坊账户密码。若不指定，可自行将密码填入repo/ethereum目录下的password文件中；
- `--min-confirm`：最小确认数，用于本地测试建议设置为0；
- `--transfer`：transfer合约地址；
- `--data-swapper`：data swapper合约地址；
- `--timeout-height`：设置超时块高H，如跨链交易在中继链的区块高度为h的区块上链后，h+H的高度还未收到目的链回执，将触发超时回滚。

**示例**：

```shell
# 注意，由于安装包的地址为github，下载可能较慢，可以重试几次或通过设置代理
# 需先在以太坊链上部署好broker合约
# 可先不指定key和password，后续自行修改repo/ethereum目录下的account.key文件和password文件
$ pier --repo xxx/.pier plugin ethereum --broker 0x8d0522826c787c20fEDA2A2E3B3557D9644a1CE5 --addr ws://localhost:8546 --key xxx/datadir/keystore/xxx --password "123" --min-confirm 0 --timeout-height 10

Start downloading https://github.com/meshplus/pier-client-ethereum/releases/download/v1.18.0/eth-client-v1.18.0-darwin
Finish downloading https://github.com/meshplus/pier-client-ethereum/releases/download/v1.18.0/eth-client-v1.18.0-darwin
```

## 7. pier start

`pier start`命令用于启动pier。

```shell
$ pier start -h

NAME:
   pier start - Start a long-running daemon process

USAGE:
   pier start [arguments...]
```

**示例**：

```shell
$ pier --repo xxx/.pier start
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

## 8. pier version

`pier version`命令用于查询版本号。

```shell
$ pier version -h

NAME:
   pier version - Show version about ap

USAGE:
   pier version [arguments...]
```

**示例**：

```shell
$ pier version

Pier version: dev-release-1.18-3826f39
App build date: 2022-01-06T17:04:07
System version: darwin/amd64
Golang version: go1.16.6
```
