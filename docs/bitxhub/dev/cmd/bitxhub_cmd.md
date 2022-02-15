
# bitxhub
中继链命令描述如下：

```shell
bitxhub -h

NAME:
   BitXHub - A leading inter-blockchain platform

USAGE:
   bitxhub [global options] command [command options] [arguments...]

COMMANDS:
   config   Operate bitxhub config
   init     Initialize BitXHub local configuration
   start    Start a long-running start process
   key      Create and show key information
   version  BitXHub version
   cert     Certification tools
   client   BitXHub client command
   help, h  Shows a list of commands or help for one command
            
```

**参数解释**

- `repo`: 可选参数，指定bitxhub节点配置文件所在目录，如果不指定，默认使用$HOME/.bitxhub目录。
```shell
GLOBAL OPTIONS:
   --repo value   BitXHub storage repo path
   --help, -h     show help
   --version, -v  print the version
```
## 子命令描述

### bitxhub config
显示当前节点配置：如未指定`repo`则默认使用`~/.bitxhub/`路径，若路径下存在旧配置，则提示覆盖与否。

**示例说明**

```shell
$ bitxhub config 

{
  "cert": {
    "agency_cert_path": "certs/agency.cert",
    "ca_cert_path": "certs/ca.cert",
    "node_cert_path": "certs/node.cert",
    "verify": true
  },
  "executor": {
    "type": "serial"
  },
  "gateway": {
    "AllowedOrigins": [
      "*"
    ]
  },
  "genesis": {
    "admins": [
      {
        "address": "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
        "weight": 2
      },
      {
        "address": "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
        "weight": 2
      },
      {
        "address": "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
        "weight": 2
      },
      {
        "address": "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8",
        "weight": 2
      }
    ],
    "strategy": {
      "appchainmgr": "SimpleMajority",
      "nodemgr": "SimpleMajority",
      "rulemgr": "SimpleMajority",
      "servicemgr": "SimpleMajority"
    }
  },
  "license": {
    "key": "133847740289a8dc5447bfbaedf10ed5ee9460110e4ba6b455e33cf941253527f18dd721515c5142fcea9b6415ddad6b3f1696011a4bbc86f35833ae9296a3ea",
    "verifier": "https://172.16.5.3:11443/v1/validation"
  },
  "limiter": {
    "capacity": 10000,
    "interval": 50000000,
    "quantum": 500
  },
  "log": {
    "dir": "logs",
    "filename": "bitxhub.log",
    "level": "info",
    "module": {
      "api": "info",
      "consensus": "info",
      "coreapi": "info",
      "executor": "info",
      "p2p": "info",
      "profile": "info",
      "router": "info",
      "storage": "info"
    },
    "report_caller": false
  },
  "monitor": {
    "Enable": true
  },
  "order": {
    "plugin": "plugins/raft.so"
  },
  "ping": {
    "duration": 15000000000,
    "enable": true
  },
  "port": {
    "gateway": 9091,
    "grpc": 60011,
    "monitor": 40011,
    "pprof": 53121
  },
  "pprof": {
    "duration": 30000000000,
    "enable": true,
    "mode": "memory",
    "ptype": "http"
  },
  "repo_root": "/Users/xxx/.bitxhub",
  "security": {
    "EnableTLS": false,
    "pem_file_path": "certs/node.cert",
    "server_key_path": "certs/node.priv"
  },
  "solo": false,
  "title": "BitXHub configuration file",
  "txpool": {
    "batch_size": 500,
    "batch_timeout": 500000000
  }
}

```



### bitxhub init

初始化节点配置：如未指定`repo`则默认使用`~/.bitxhub/`路径。

**示例说明**

```shell
$ bitxhub init   

initializing bitxhub at /Users/xxx/.bitxhub
bitxhub configuration file already exists
reinitializing would overwrite your configuration, Y/N?
y

```



### bitxhub start

该命令`bitxhub start`显示BitXHub的版本信息。它会在命令行显示版本、构建日期、操作系统/架构和Go编译器版本。

```shell
NAME:
   bitxhub start - Start a long-running start process

USAGE:
   bitxhub start [command options] [arguments...]

OPTIONS:
   --passwd value   bitxhub key password
   --config value   bitxhub config path
   --network value  bitxhub network config path
   --order value    bitxhub order config path

```

**参数解释**

- `passwd`：指定节点私钥密码；
- `config`：指定节点配置路径，一般指`bitxhub.toml`；
- `network`：指定节点p2p网络配置文件，一般指`network.toml`；
- `order`：指定节点共识配置文件，一般指`order.toml`。

**若不指定以上配置，则默认至bitxhub --repo路径下寻找**。

**示例说明**

```shell
$ bitxhub --repo <node_config_path> start                         

# 控制台输出
BitXHub version: dev-release-1.11-48106fe                                                                                                                                                                                               
App build date: 2021-11-25T16:43:19                                                                                                                 
System version: darwin/amd64                                                                                                                              
Golang version: go1.14.15                                                                                                                                 
                                                                                                                                                                                                
time="2021-11-29T18:07:37.722" level=info msg="MemPool batch size = 200" module=order
time="2021-11-29T18:07:37.722" level=info msg="MemPool tx slice size = 200" module=order
time="2021-11-29T18:07:37.722" level=info msg="MemPool batch seqNo = 6" module=order
time="2021-11-29T18:07:37.723" level=info msg="MemPool pool size = 50000" module=order
time="2021-11-29T18:07:37.723" level=info msg="Raft localID = 1" module=order
time="2021-11-29T18:07:37.723" level=info msg="Raft lastExec = 6  " module=order
time="2021-11-29T18:07:37.723" level=info msg="Raft snapshotCount = 1000" module=order
time="2021-11-29T18:07:37.723" level=info msg="GRPC service started" module=api port=60011
time="2021-11-29T18:07:37.723" level=info msg="Gateway service started" module=api port=9091
```



### bitxhub key
####**功能**
该命令`bitxhub key`提供针对bitxhub节点的私钥管理。它包含了私钥生成、私钥格式转换、私钥查看、地址查看。子命令信息如下:

```shell
NAME:
BitXHub key - Create and show key information

USAGE:
BitXHub key command [command options] [arguments...]

COMMANDS:
gen      Create new Secp256k1 private key in specified directory
show     Show BitXHub key from repo
address  Show address from Secp256k1 private key
```

####**子命令**

#### bitxhub key gen

```shell
NAME:
   BitXHub key gen - Create new Secp256k1 private key in specified directory

USAGE:
   BitXHub key gen [command options] [arguments...]

OPTIONS:
   --target value  Specific target directory
   --passwd value  Specify password

```

**参数解释**

- `target`：指定生成路径；
- `passwd`：指定私钥密码。

**示例说明**

```shell
$ bitxhub key gen --target ~/.bitxhub
# 控制台输出
key.json key is generated under directory /Users/xxx/.bitxhub
```

#### bitxhub key show

显示`key.json`具体内容。

`key.json`展示

**示例说明**

```shell
bitxhub key show     

{
 "type": 3,
 "cipher": {
  "data": "18bc2c685cc79d7098fa1f3b97f14541121b61b1d3492ef4f298c84844e7b380fa8e8a84dc44d910aa9dafddc0347c670bace2c74d8553a4c843ae58ce4aeb78",
  "cipher": "AES-256"
 }
}
```

#### bitxhub key address

显示`key.json`地址。

**示例说明**

```shell
bitxhub key address --path ./key.json --passwd 123

0x3Db84D019fCd3A845e2fB3A21B56812Ffd933f19
```



### bitxhub version
####**功能**
该命令`bitxhub version`显示BitXHub的版本信息。它会在命令行显示版本、构建日期、操作系统/架构和Go编译器版本。

```shell
NAME:
   bitxhub version - BitXHub version

USAGE:
   bitxhub version [arguments...]

```


**示例说明**

```shell
$ bitxhub version
BitXHub version: dev-release-1.11-48106fe                                                                                                          
App build date: 2021-11-25T15:01:28
System version: darwin/amd64
Golang version: go1.14.15
```



### bitxhub cert

####**功能**
该命令`bitxhub cert`提供一系列x509标准的证书工具，服务于中继链节点的准入机制。它包含了CA生成、CSR描述生成、证书签发、证书解析、证书私钥生成以及证书校验。说明信息如下:

```shell
NAME:
   BitXHub cert - Certification tools

USAGE:
   BitXHub cert command [command options] [arguments...]

COMMANDS:
   ca      Generate ca cert and private key
   csr     Generate csr file
   issue   Issue certification by ca
   parse   parse certification
   priv    Generate and show private key for certificate
   verify  verify cert
                   
```


####**子命令**

#### bitxhub cert ca

生成根CA证书

```shell
bitxhub cert ca -h
NAME:
   BitXHub cert ca - Generate ca cert and private key

USAGE:
   BitXHub cert ca [arguments...]

```

**示例说明**

```shell
$ bitxhub --repo=$HOME/cert cert ca

# 在repo目录下生成如下文件
├── ca.cert
├── ca.priv
```



#### bitxhub cert csr

生成CSR描述信息。

```shell
NAME:
   BitXHub cert csr - Generate csr file

USAGE:
   BitXHub cert csr [command options] [arguments...]

OPTIONS:
   --key value     Specify Secp256r1 private key path
   --org value     Specify organization name
   --target value  Specific target directory

```

**参数解释**

- `key`: 指定私钥路径；
- `org`: 指定被签发人组织名称；
- `target`: 指定目标CSR路径；



#### bitxhub cert issue

证书签发。

```shell
NAME:
   BitXHub cert issue - Issue certification by ca

USAGE:
   BitXHub cert issue [command options] [arguments...]

OPTIONS:
   --csr value     Specify csr path
   --is_ca value   Specify whether it's ca
   --key value     Specify ca's secp256r1 private key path
   --cert value    Specify ca certification path
   --target value  Specific target directory

```

**参数解释**

- `csr`: 指定CSR描述文件路径；
- `is_ca`: 指定签发人是否为CA证书；
- `key`: 指定当前签发人私钥路径；
- `cert`: 指定当前签发人证书路径；
- `target`: 指定生成的证书路径。



#### bitxhub cert parse

证书解析。

```shell
NAME:
   BitXHub cert parse - parse certification

USAGE:
   BitXHub cert parse [command options] [arguments...]

OPTIONS:
   --path value  certification path
```

**参数解释**

- `path`: 指定证书路径。



#### bitxhub cert priv

生成或展示用于p2p网络通信的私钥。

```shell
NAME:
   BitXHub cert priv - Generate and show private key for certificate

USAGE:
   BitXHub cert priv command [command options] [arguments...]

COMMANDS:
   gen  Create new private key
   pid  Show pid from private key

```

**子命令**

**私钥生成**

##### bitxhub cert priv gen

```shell
NAME:
   BitXHub cert priv gen - Create new private key

USAGE:
   BitXHub cert priv gen [command options] [arguments...]

OPTIONS:
   --name value    Specific private key name
   --target value  Specific target directory
```

**示例说明**

```shell
bitxhub cert priv gen --name server --target ./

server.priv key is generated under directory /Users/xxx/GolandProjectsTest/bitxhub-docs
```

****



##### bitxhub cert priv pid

获取libp2p节点pid。

```shell
NAME:
   BitXHub cert priv pid - Show pid from private key

USAGE:
   BitXHub cert priv pid [command options] [arguments...]

OPTIONS:
   --path value  Specific private key path
```

**示例说明**

```shell
bitxhub cert priv pid --path ./server.priv     
# 控制台输出
QmWw4hPtm51jpxzKjyYMe3E7bNei9wdoug6B1rZqSwVSL4
```




### bitxhub client
####**功能**

该命令`bitxhub client`提供中继链节点的客户端操作命令。它包含了账户信息查询、中继链信息、区块查询、节点网络信息、回执查询、交易操作、验证者集查询、删除共识节点、提案治理。
子命令信息如下:

```shell
NAME:
   BitXHub client - BitXHub client command

USAGE:
   BitXHub client command [command options] [arguments...]

COMMANDS:
   account     Query account information
   chain       Query bitxhub chain info
   block       Query block
   network     Query network info from node
   receipt     Query receipt
   tx          Transaction manipulation
   validators  Query validator address
   delVPNode   delete a vp node
   governance  governance command                      
```

**参数解释**

- `gateway`: 指定客户端http服务地址，如果节点开启了TLS功能，则需要传递https地址；
- `cert`: 指定节点的CA证书，如果节点开始了TLS功能，需要传递；

```shell
OPTIONS:
--gateway value  Specific gateway address (default: "http://localhost:9091/v1/")
--cert value     Specific ca cert file if https is enabled
--help, -h       show help
```
####**子命令**

#### bitxhub client account
账户信息查询。
```shell
bitxhub client account 0x79a1215469FaB6f9c63c1816b45183AD3624bE34                                         

# 控制台输出
{
  "type": "normal",
  "balance": 100000000,
  "contract_count": 0,
  "code_hash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}

```


#### bitxhub client chain

中继链信息查询。
```shell
NAME:
   BitXHub client chain - Query bitxhub chain info

USAGE:
   BitXHub client chain command [command options] [arguments...]

COMMANDS:
   meta    Query bitxhub chain meta
   status  Query bitxhub chain status

OPTIONS:
   --help, -h  show help
```
##### bitxhub client chain meta

查询中继链信息。

**示例说明**

```shell
bitxhub client chain meta
# 控制台输出
{"height":"1","block_hash":"0x0BF41c03bE038e389470dBdC9ed53DF897F178921520AF37877188d1759A7068"}
```

##### bitxhub client chain status

查询中继链状态是否可用。

**示例说明**

```shell
# normal: 已处在共识状态
# abnormal：未共识状态，集群没有正常运行
$ bitxhub client chain status

# 控制台输出
normal
```



#### bitxhub client block
区块信息查询，**可指定区块号或区块Hash**。

**示例说明**

```shell
$ bitxhub client block 1

# 控制台输出
{"block_header":{"number":"1","state_root":"0xC3A3B54F593e638D8a9FAbf66b781EFcb14D0f013D7c3E3a02ef698294f6BBF5"},"block_hash":"0x0BF41c03bE038e389470dBdC9ed53DF897F178921520AF37877188d1759A7068","signature":"74DK6FeGnAYBJ0pJ4bLwIaT9cp9YwIscenKsRc7myXUBQfvvLKhIpZqPJkf8tAI7V8u89SLOAdz94fo/ajKi+wA="}
```
#### bitxhub client network
全网节点信息。

**示例说明**

```shell
$ bitxhub client network

# 控制台输出
{
  "1": {
    "id": 1,
    "pid": "QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL",
    "account": "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4001/p2p/"
    ]
  },
  "2": {
    "id": 2,
    "pid": "QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4",
    "account": "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4002/p2p/"
    ]
  },
  "3": {
    "id": 3,
    "pid": "QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS",
    "account": "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4003/p2p/"
    ]
  },
  "4": {
    "id": 4,
    "pid": "QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy",
    "account": "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4004/p2p/"
    ]
  }
}
```

#### bitxhub client receipt
交易回执查询。

**示例说明**

```shell
$ bitxhub client receipt 0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E

# 控制台输出
{
  "tx_hash": "0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E",
  "ret": null,
  "status": 0,
  "events": []
}

```

#### bitxhub client tx
交易发送与交易查询。

```shell
NAME:
   BitXHub client tx - Transaction manipulation

USAGE:
   BitXHub client tx command [command options] [arguments...]

COMMANDS:
   get   Query transaction
   send  Send transaction

OPTIONS:
   --help, -h  show help
```

##### bitxhub client tx send

交易发送

**示例说明**

```shell
$ bitxhub client tx send --key scripts/build/node1/key.json --to 0x97c8B516D19edBf575D72a172Af7F418BE498C37 --amount 2 --type 0

# 控制台输出
{"tx_hash":"0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E"}
```
##### bitxhub client tx get

交易查询

**示例说明**

```shell
$ bitxhub client tx get 0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E

# 控制台输出
{"tx":{"from":"0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","to":"0x97c8B516D19edBf575D72a172Af7F418BE498C37","timestamp":"1638168425560507000","transaction_hash":"0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E","payload":"EAI=","nonce":"2","signature":"mdOdSzAOTDxfZ0VphvyDyir1lE8w31rUaSTtnj1us9NMhtUPVHz5bLcOBdZrmUfnaVhL3xGQxK3/P7WHmihxIQA="},"tx_meta":{"block_hash":"HoMgbS+NeCRds5Qc/58NfHXA+jVCuhUcsc7p9IvJxMc=","block_height":"3"}}
```
#### bitxhub client validators
全网验证人信息。

**示例说明**

```shell
$ bitxhub client validators

# 控制台输出
[
  "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
  "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
  "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
  "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"
]
```

#### bitxhub client delVPNode
动态删除共识节点
```shell
NAME:
   BitXHub client delVPNode - delete a vp node

USAGE:
   BitXHub client delVPNode [command options] [arguments...]

OPTIONS:
   --pid value  pid of vp node
```

#### bitxhub client governance
提案治理命令。
```shell
NAME:
   BitXHub client governance - governance command

USAGE:
   BitXHub client governance command [command options] [arguments...]

COMMANDS:
   vote       vote to a proposal
   proposals  query proposals based on the condition
   chain      appchain manage command

```
##### bitxhub client governance vote

提案投票。

```shell
NAME:
   BitXHub client governance vote - vote to a proposal

USAGE:
   BitXHub client governance vote [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --info value    voting information, approve or reject
   --reason value  reason to vote

```
**参数解释**

- `id`：必选参数。提案id，生成提案时将在控制台打印。
- `info`：必选参数。是否通过提案。approve为通过，reject为拒绝。
- `reason`：必选参数。投票理由说明。

**示例说明**

```shell
$ bitxhub client governance vote --info approve --reason test --id 0x9A601dbebf20f3E3362241810C2229F7f97329d6-0

# 控制台输出
vote successfully! 
0x9A601dbebf20f3E3362241810C2229F7f97329d6-0

```
##### bitxhub client governance proposals

提案查询。用于查询中继链提案的内容以及相关的状态。命令说明如下：

```shell
NAME:
   BitXHub client governance proposals - query proposals based on the condition

USAGE:
   BitXHub client governance proposals [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --type value    proposal type, currently only AppchainMgr is supported
   --status value  proposal status, one of proposed, approve or reject
   --from value    the address of the account to which the proposal was made
```

**参数解释**

- `id`: 提案id；
- `type`: 提案类型，当前支持应用链管理；
- `status`: 提案状态，是否通过；
- `from`: 提案者地址。

**示例说明**

```shell
$ bitxhub client governance proposals --id  0x0FDDC68A2300cF9CB0a217123D5f656e0943C1Da-0 --type AppchainMgr --status proposed --from 0x0FDDC68A2300cF9CB0a217123D5f656e0943C1Da

# 控制台输出
Id                                            Type         Status    ApproveNum  RejectNum  ElectorateNum  ThresholdNum  Des
--                                            ----         ------    ----------  ---------  -------------  ------------  ---
0x9A601dbebf20f3E3362241810C2229F7f97329d6-0  AppchainMgr  proposed  1           0          4              3             register
```



##### bitxhub client governance chain

应用链管理。命令说明如下：

```shell
NAME:
   BitXHub client governance chain - appchain manage command

USAGE:
   BitXHub client governance chain command [command options] [arguments...]

COMMANDS:
   status    query chain status by chain id
   freeze    freeze appchain by chain id
   activate  activate chain by chain id

```

**参数解释**

- `status`:查询应用链状态；
- `freeze`:应用链冻结；
- `activate`:应用链激活。

**示例说明**

```shell
# 管理员治理投票通过后，应用链状态从registing转换为available
$ bitxhub client governance chain status --id 0x0FDDC68A2300cF9CB0a217123D5f656e0943C1Da

# 控制台输出
appchain 0x0FDDC68A2300cF9CB0a217123D5f656e0943C1Da is available

```

