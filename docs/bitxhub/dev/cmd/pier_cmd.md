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
   client      Command about appchain in pier
   id          Get appchain id
   init        Initialize pier core local configuration
   config      Initialize pier plugins configuration
   interchain  Query interchain info
   p2p         Command about p2p
   rule        Command about rule
   start       Start a long-running daemon process
   version     Show version about ap
   proposals   proposals manage command
   show        Show pier key from repo
   help, h     Shows a list of commands or help for one command

```

**参数解释**

- `--repo`：可选参数，指定pier节点配置文件所在目录，如果不指定，默认使用$HOME/.bitxhub目录。
- `--tls`：可选参数，用于pier节点与bitxhub进行tls加密传输。如果不指定，默认为false。

```shell
 GLOBAL OPTIONS:
   --repo value   Pier repository path
   --tls          enable tls between pier and bitxhub or not
   --help, -h     show help
   --version, -v  print the version
```

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
   method    Command about appchain method
   did       Command about appchain did
   register  Register pier to bitxhub
   update    update appchain in bitxhub
   activate  activate appchain in bitxhub
   logout    logout appchain in bitxhub
   get       Get appchain info

OPTIONS:
   --help, -h  show help

```

**具体描述**

`pier appchain`具体描述与使用示例参考[中继模式管理](/v1.11/bitxhub/function/relay_manager/)。



### pier client

`pier client`命令用于直连模式的应用链管理。

**用法**

```shell
NAME:
   Pier client - Command about appchain in pier

USAGE:
   Pier client command [command options] [arguments...]

COMMANDS:
   register  Register appchain in pier
   update    Update appchain in pier
   audit     Audit appchain in pier
   get       Get appchain info
   rule      register appchain validation rule

OPTIONS:
   --help, -h  show help

```

**具体描述**

`pier appchain`具体描述与使用示例参考[直连模式管理](/v1.11/bitxhub/bitxhub/function/direct_manager/)。

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
$ pier --repo $HOME/.pier1 id 
# 控制台输出
0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb
```



### pier init

`pier init`命令用于初始化pier相关配置。如生成公私钥、ca证书等。

**用法**

```shell
NAME:
   Pier init - Initialize pier core local configuration

USAGE:
   Pier init command [command options] [arguments...]

COMMANDS:
   relay   Initialize pier relay mode configuration
   direct  Initialize pier direct mode configuration
   union   Initialize pier union mode configuration

OPTIONS:
   --http-port value    Specify http port (default: 44544)
   --pprof-port value   Specify pprof port (default: 44555)
   --enable-tls         Enable TLS or not
   --tlsca value        Specify TLS CA certificate path (default: "certs/ca.pem")
   --common-name value  Specify common name to verify (default: "localhost")
   --ha value           Specify if pier will run in single mode or high availability mode (default: "single")
   --help, -h           show help
```

**具体描述**

**参数解释**

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

OPTIONS:
   --peers value  Specify counter party peers to connect
```

**具体描述**

**参数解释**

- `--peers`：必选参数，指定直连模式下pier的网络信息（包含自己）。

**样例：**

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
# 初始化pier1相关配置，配置文件目录为$HOME/.pier1

$ PID1=$(pier --repo .pier1 p2p id)
$ PID2=$(pier --repo .pier1 p2p id)

$ pier --repo .pier1 init direct --peers /ip4/127.0.0.1/tcp/3001/p2p/$PID1 --peers /ip4/127.0.0.1/tcp/3002/p2p/$PID2
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
   --connectors value  Specify the remote union peers to connect
```

**具体描述**

**参数解释**

- `--addrs`：可选参数，指定bixhub各节点地址及端口号。

- `--connectors`：必选参数，指定大规模模式下pier的网络信息（包含自己）。

**样例：**

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
# 初始化pier1相关配置，配置文件目录为$HOME/.pier1

$ PID1=$(pier --repo .pier1 p2p id)
$ PID2=$(pier --repo .pier1 p2p id)

$ pier --repo .pier1 init union --peers /ip4/127.0.0.1/tcp/3001/p2p/$PID1 --peers /ip4/127.0.0.1/tcp/3002/p2p/$PID2
```



### pier interchain

`pier interchain`命令用于查询应用链的跨链交易信息。

用法

```
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

```
USAGE:
   Pier interchain command [command options] [arguments...]

COMMANDS:
   ibtp  Query ibtp by id
```

**样例：**

```shell
pier --repo $HOME/.pier1 interchain --key $HOME/.pier1/key.json ibtp --id 0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb-0x23Fb0E7eF676467563d37D820F1b1Fddb0f9a2E1-1

# 控制台输出
INFO[16:52:41.917] Establish connection with bitxhub localhost:60013 successfully  module=rpcx
Tx hash: 0x82d3434e0a295B22A8e55884FEdd137d19e8cBDc32d5B593AD6D9AFB5cf59600
tx:<from:<RawAddress:[255 129 153 250 228 140 128 139 69 102 125 160 204 170 238 131 155 26 16 203] Address:"" > to:<RawAddress:[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 10] Address:"" > timestamp:1638435054429909000 transaction_hash:<RawHash:[130 211 67 78 10 41 91 34 168 229 88 132 254 221 19 125 25 232 203 220 50 213 181 147 173 109 154 251 92 245 150 0] Hash:"" > IBTP:<from:"0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb" to:"0x23Fb0E7eF676467563d37D820F1b1Fddb0f9a2E1" index:1 timestamp:1638435054428680000 proof:"k\206\262s\3774\374\341\235k\200N\377Z?WG\255\244\352\242/\035I\300\036R\335\267\207[K" payload:"\022\221\001\n*0x5bFe03Dbd09817d4957693f672cc31A133Bb6084\022*0xA5dD12E27Ee5E79cE0B50adb376414351C8eea5f\032\020interchainCharge\"\001A\"\001B\"\002102\000:\022interchainRollbackB\001AB\00210" > nonce:4 signature:"T)\313\333~\316f\372.\010\235\234\222J\314@<\202\260\243\367b\035\016\366\326y\3515\020\222\256$\010\314\035/\271H\217|~\302\233\353b\275\267ir\t\242\376\224\307q\217l\232\037DP\n\317\000" extra:"1" > tx_meta:<block_hash:"\326{90^\355a\236\342\235i\302\374+\352VI\030\242\211\335x\371w\221>(\254%o\212\222" block_height:14 >
```



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
# 初始化pier1相关配置，配置文件目录为$HOME/.pier1
$ pier --repo $HOME/.pier1 p2p id 

#控制台输出
QmNpQvh8Lm7gwt3sDqwJ1pgNn6taJLqdgtRVyZWhsD5bND
```



### pier rule

`pier rule`命令用于中继模式下pier向中继链部署应用链验证规则。

**用法**

```
NAME:
   Pier rule - Command about rule

USAGE:
   Pier rule command [command options] [arguments...]

COMMANDS:
   deploy  Deploy validation rule
   update  update master rule
   logout  logout validation rule

OPTIONS:
   --help, -h  show help
```

**具体描述**

`pier rule`具体描述与使用示例参考[直连模式管理](/v1.11/bitxhub/bitxhub/function/direct_manager/)的部署验证规则模块。



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
$ pier --repo $HOME/.pier1 start

# 控制台输出
Pier version: dev-release-1.6-1b5b79f
App build date: 2021-12-01T17:50:14
System version: darwin/amd64
Golang version: go1.15.13

INFO[17:07:29.150] Establish connection with bitxhub localhost:60013 successfully  module=app
2021-12-02T17:07:29.171+0800 [DEBUG] plugin: starting plugin: path=/bin/sh args="[sh -c /Users/liruoxin/testRelay/release1.6/eth1/plugins/eth-client]"
2021-12-02T17:07:29.178+0800 [DEBUG] plugin: plugin started: path=/bin/sh pid=74023
2021-12-02T17:07:29.178+0800 [DEBUG] plugin: waiting for RPC address: path=/bin/sh
2021-12-02T17:07:29.283+0800 [DEBUG] plugin.sh: 2021-12-02T17:07:29.283+0800 [DEBUG] client: plugin address: network=unix address=/var/folders/xr/pnhf9j6n2k5_fysm6s0xkgt00000gn/T/plugin718241634
2021-12-02T17:07:29.283+0800 [DEBUG] plugin: using plugin: version=4
2021-12-02T17:07:29.284+0800 [TRACE] plugin.stdio: waiting for stdio data
2021-12-02T17:07:29.287+0800 [DEBUG] plugin.sh: 2021-12-02T17:07:29.287+0800 [INFO ] client: Basic appchain info: broker address=0xFb23Af09e3E8D83fd5575De9558920Bf351F05E8 ethereum node ip=ws://127.0.0.1:8546
2021-12-02T17:07:29.416+0800 [DEBUG] plugin.sh: 2021-12-02T17:07:29.416+0800 [INFO ] client: ContractABI: addr=0x5bfe03dbd09817d4957693f672cc31a133bb6084 name=transfer.abi
Client Type: ethereum
Pprof on localhost:44555
…………
```

### pier version

`pier version`命令用于查询版本号。

**用法**

```
NAME:
   pier version - Show version about ap

USAGE:
   pier version [arguments...]
```

**具体描述**

**样例**：

```shell
$ pier --repo $HOME/.pier1 version

Pier version: dev-release-1.11-e1e7c14
App build date: 2021-12-01T17:50:14
System version: darwin/amd64
Golang version: go1.15.13
```

