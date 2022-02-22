
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
**子命令描述**

## bitxhub config
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



## bitxhub init

初始化节点配置：如未指定`repo`则默认使用`~/.bitxhub/`路径。

**示例说明**

```shell
$ bitxhub init   

initializing bitxhub at /Users/xxx/.bitxhub
bitxhub configuration file already exists
reinitializing would overwrite your configuration, Y/N?
y

```



## bitxhub start

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



## bitxhub key
**功能**
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

**子命令**

### bitxhub key gen

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

### bitxhub key show

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

### bitxhub key address

显示`key.json`地址。

**示例说明**

```shell
bitxhub key address --path ./key.json --passwd 123

0x3Db84D019fCd3A845e2fB3A21B56812Ffd933f19
```



## bitxhub version
**功能**
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



## bitxhub cert

**功能**
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


**子命令**

### bitxhub cert ca

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



### bitxhub cert csr

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



### bitxhub cert issue

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



### bitxhub cert parse

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



### bitxhub cert priv

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

#### bitxhub cert priv gen

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



#### bitxhub cert priv pid

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




## bitxhub client
**功能**

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
**子命令**

### bitxhub client account
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


### bitxhub client chain

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
#### bitxhub client chain meta

查询中继链信息。

**示例说明**

```shell
bitxhub client chain meta
# 控制台输出
{"height":"1","block_hash":"0x0BF41c03bE038e389470dBdC9ed53DF897F178921520AF37877188d1759A7068"}
```

#### bitxhub client chain status

查询中继链状态是否可用。

**示例说明**

```shell
# normal: 已处在共识状态
# abnormal：未共识状态，集群没有正常运行
$ bitxhub client chain status

# 控制台输出
normal
```



### bitxhub client block
区块信息查询，**可指定区块号或区块Hash**。

**示例说明**

```shell
$ bitxhub client block 1

# 控制台输出
{"block_header":{"number":"1","state_root":"0xC3A3B54F593e638D8a9FAbf66b781EFcb14D0f013D7c3E3a02ef698294f6BBF5"},"block_hash":"0x0BF41c03bE038e389470dBdC9ed53DF897F178921520AF37877188d1759A7068","signature":"74DK6FeGnAYBJ0pJ4bLwIaT9cp9YwIscenKsRc7myXUBQfvvLKhIpZqPJkf8tAI7V8u89SLOAdz94fo/ajKi+wA="}
```
### bitxhub client network
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

### bitxhub client receipt
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

### bitxhub client tx
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

#### bitxhub client tx send

交易发送

**示例说明**

```shell
$ bitxhub client tx send --key scripts/build/node1/key.json --to 0x97c8B516D19edBf575D72a172Af7F418BE498C37 --amount 2 --type 0

# 控制台输出
{"tx_hash":"0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E"}
```
#### bitxhub client tx get

交易查询

**示例说明**

```shell
$ bitxhub client tx get 0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E

# 控制台输出
{"tx":{"from":"0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","to":"0x97c8B516D19edBf575D72a172Af7F418BE498C37","timestamp":"1638168425560507000","transaction_hash":"0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E","payload":"EAI=","nonce":"2","signature":"mdOdSzAOTDxfZ0VphvyDyir1lE8w31rUaSTtnj1us9NMhtUPVHz5bLcOBdZrmUfnaVhL3xGQxK3/P7WHmihxIQA="},"tx_meta":{"block_hash":"HoMgbS+NeCRds5Qc/58NfHXA+jVCuhUcsc7p9IvJxMc=","block_height":"3"}}
```
### bitxhub client validators
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

### bitxhub client delVPNode
动态删除共识节点
```shell
NAME:
   BitXHub client delVPNode - delete a vp node

USAGE:
   BitXHub client delVPNode [command options] [arguments...]

OPTIONS:
   --pid value  pid of vp node
```

### bitxhub client governance
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
   rule       rule manage command
   node       node manage command
   role       role manage command
   dapp       dapp manage command

```
#### bitxhub client governance vote

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
#### bitxhub client governance proposals
提案管理。

##### bitxhub client governance proposals query
提案查询。用于查询中继链提案的内容以及相关的状态。命令说明如下：

```shell
NAME:
   BitXHub client governance proposals query - query proposals based on the condition

USAGE:
   BitXHub client governance proposal query [command options] [arguments...]
   
OPTIONS:
   --id value      proposal id
   --type value    proposal type, currently only AppchainMgr, RuleMgr, NodeMgr, RoleMgr are supported
   --status value  proposal status, one of proposed, paused, approve or reject
   --from value    the address of the account to which the proposal was made
   --objId value   the ID of the managed object
```

**参数解释**

- `id`: 提案id；
- `type`: 提案类型，当前支持应用链管理、验证规则管理、节点管理、身份管理；
- `status`: 提案状态，是否通过；
- `from`: 提案者地址；
- `objId`: 管理对象id，如应用链id。

以上五个参数可任意指定至少一个作为查询条件

**示例说明**

```shell
$ bitxhub client governance proposals --id  0x0FDDC68A2300cF9CB0a217123D5f656e0943C1Da-0 --type AppchainMgr --status proposed --from 0x0FDDC68A2300cF9CB0a217123D5f656e0943C1Da --objId 10000000

# 控制台输出
Id                                            Type         Status    ApproveNum  RejectNum  ElectorateNum  ThresholdNum  Des
--                                            ----         ------    ----------  ---------  -------------  ------------  ---
0x9A601dbebf20f3E3362241810C2229F7f97329d6-0  AppchainMgr  proposed  1           0          4              3             register
```


##### bitxhub client governance proposals withdraw
提案撤销。命令说明如下：

```shell
NAME:
   BitXHub client governance proposal withdraw - withdraw a proposal

USAGE:
   BitXHub client governance proposal withdraw [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --reason value  withdraw reason
```

**参数解释**

- `id`: 提案id；
- `reason`: 提案撤销理由；


#### bitxhub client governance chain

应用链管理。命令说明如下：

```shell
NAME:
   BitXHub client governance chain - appchain manage command

USAGE:
   BitXHub client governance chain command [command options] [arguments...]

COMMANDS:
   status    query chain status by chain id
   register  register appchain
   freeze    freeze appchain by chain id
   activate  activate chain by chain id

```

**子命令**

##### bitxhub client governance chain status
查询应用链状态。命令说明如下：

```shell
NAME:
   BitXHub client governance chain status - query chain status by chain id

USAGE:
   BitXHub client governance chain status [command options] [arguments...]

OPTIONS:
   --id value  Specify chain id
```

**参数解释**

- `id`：必选参数，应用链id。

**示例说明**

```shell
# 管理员治理投票通过后，应用链状态从registing转为available
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance chain status --id did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.

# 控制台输出
appchain did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:. is available

```

##### bitxhub client governance chain register
注册应用链。命令说明如下：

```shell
NAME:
   BitXHub client governance chain register - register appchain

USAGE:
   BitXHub client governance chain register [command options] [arguments...]

OPTIONS:
   --method value      Specific did sub method name(like appchain)
   --doc-addr value    Specify the addr of did document
   --doc-hash value    Specify the hash of did document
   --name value        Specify appchain name
   --type value        Specify appchain type
   --desc value        Specify appchain description
   --version value     Specify appchain version
   --validators value  Specify appchain validators path
   --consensus value   Specify appchain consensus type
   --pubkey value      Specify appchain pubkey
   --reason value      Specify register reason
   --rule value        Specify master rule addr
   --ruleUrl value     Specify master rule url
```

**参数解释**

- `method`：必选参数，应用链did中method名称，1.11.2版本要求method格式为`appchain`拼接应用链管理员地址，如`appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F`；
- `doc-addr`：必选参数，did文件地址；
- `doc-hash`：必选参数，did文件的哈希值；
- `name`：必选参数，应用链名称；
- `type`：必选参数，应用链类型，如fabric、flato；
- `desc`：必选参数，应用链的描述信息；
- `version`：必选参数，应用链版本信息；
- `validators`：必选参数，应用链的验证人信息所在的文件路径；
- `consensus`：必选参数，应用链的共识类型，如rbft、raft等；
- `pubkey`：必选参数，应用链管理员公钥信息；
- `reason`：注册理由；
- `rule`：必选参数，应用链主验证规则地址；
- `ruleUrl`：必选参数，应用链主厌女症规则url。

**示例说明**

```shell
# 注册应用链
$ bitxhub --repo $bitxhub_node_repo client governance chain register --method appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F --doc-addr "doc-addr" --doc-hash "doc-hash" --name 应用链1 --type farbric --desc "desc" --version 1 --validators ./fabric.validators --consensus rbft --pubkey 04a35e958549f19c474090403f0f8742a490da71c73f98d667881818d5e8754aa33ea8d97c50964795d7c3f8676abb6f1004722f60fc2fe3433d9682c73ea20357 --rule 0x00000000000000000000000000000000000000a1 --ruleUrl "ruleUrl"

# 控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0, chain id is did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.
```

##### bitxhub client governance chain freeze
冻结应用链。命令说明如下：

```shell
NAME:
   BitXHub client governance chain freeze - freeze appchain by chain id

USAGE:
   BitXHub client governance chain freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify chain id
   --reason value  Specify freeze reason
```

**参数解释**
- `id`：必选参数，应用链id；
- `reason`：冻结理由。

**示例说明**
```shell
# 冻结前应用链状态为可用
$ bitxhub --repo $bitxhub_node_repo client governance chain status --id did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.
appchain did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:. is available

# 冻结应用链，发起提案
$ bitxhub --repo $bitxhub_node_repo client governance chain freeze --id did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1

# 冻结提案发起后应用链状态为冻结中
$ bitxhub --repo $bitxhub_node_repo client governance chain status --id did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.
appchain did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:. is freezing
```

##### bitxhub client governance chain activate
冻结应用链。命令说明如下：

```shell
NAME:
   BitXHub client governance chain activate - activate chain by chain id

USAGE:
   BitXHub client governance chain activate [command options] [arguments...]

OPTIONS:
   --id value      Specify chain id
   --reason value  Specify activate reason
```

**参数解释**
- `id`：必选参数，应用链id；
- `reason`：激活理由。

**示例说明**
```shell
# 冻结提案通过后，应用链为已冻结状态
$ bitxhub --repo $bitxhub_node_repo client governance chain status --id did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.
appchain did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:. is frozen

# 激活应用链，发起提案
$  bitxhub --repo $bitxhub_node_repo client governance chain activate --id did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3

# 激活提案发起后应用链状态为激活中
$ bitxhub --repo $bitxhub_node_repo client governance chain status --id did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:.
appchain did:bitxhub:appchain0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F:. is activating
```

#### bitxhub client governance node

节点管理。命令说明如下：

```shell
NAME:
   BitXHub client governance node - node manage command

USAGE:
   BitXHub client governance node command [command options] [arguments...]

COMMANDS:
   status    query node status by node pid
   register  register node
   logout    logout node by node pid
   all       query all nodes info
```

**子命令**

##### bitxhub client governance node status
查询节点状态。命令说明如下：

```shell
NAME:
   BitXHub client governance node status - query node status by node pid

USAGE:
   BitXHub client governance node status [command options] [arguments...]

OPTIONS:
   --pid value  Specify node pid
```

**参数解释**

- `pid`：必选参数，节点id。

**示例说明**

```shell
# 指定节点为bitxhub初始启动的共识节点，状态为可用
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance node status --pid QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy

# 控制台输出
node QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy is available
```

##### bitxhub client governance node register
注册节点。命令说明如下：

```shell
NAME:
   BitXHub client governance node register - register node

USAGE:
   BitXHub client governance node register [command options] [arguments...]

OPTIONS:
   --pid value      Specify node pid
   --id value       Specify vp node id, only useful for VPnode (default: 0)
   --account value  Specify node account
   --type value     Specify node type (vpNode or nvpNode), currently only VPNode is supported (default: "vpNode")
   --reason value   Specify register reason
```

**参数解释**

- `pid`：必选参数，节点网络连接pid；
- `id`：必选参数，共识节点id序号；
- `account`：必选参数，节点账户地址；
- `type`：必选参数，节点类型，vpNode为共识节点， nvpNode为审计节点，目前只支持共识节点；
- `reason`：注册理由。

**示例说明**

```shell
# 注册共识节点
$ bitxhub --repo $bitxhub_node_repo client governance node register --pid QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8 --account 0xaE6934E4071082CA2a83F7FEf29ede10C1bc743C --type vpNode --id 5
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0

# 发起注册提案后，节点状态为注册中
$ bitxhub --repo $bitxhub_node_repo client governance node status --pid QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8
node QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8 is registering
```

##### bitxhub client governance node logout
注销节点。命令说明如下：

```shell
NAME:
   BitXHub client governance node logout - logout node by node pid

USAGE:
   BitXHub client governance node logout [command options] [arguments...]

OPTIONS:
   --pid value     Specify node pid
   --reason value  Specify logout reason
```

**参数解释**
- `pid`：必选参数，节点pid；
- `reason`：注销理由。

**示例说明**
```shell
# 节点注册提案投票功后状态为可用
$ bitxhub --repo $bitxhub_node_repo client governance node status --pid QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8
node QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8 is available

# 注销节点，发起提案
$ bitxhub --repo $bitxhub_node_repo client governance node logout --pid QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1

# 注销提案发起后节点状态为注销中
$ bitxhub --repo $bitxhub_node_repo client governance node status --pid QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8
node QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8 is logouting
```

##### bitxhub client governance node all
查询所有节点。

**示例说明**
```shell
# 查询所有节点状态
$ bitxhub --repo $bitxhub_node_repo client governance node all

# 控制台打印：bitxhub启动初始的四个共识节点始终为可用状态，一个logouting状态的共识节点为后续注册的新节点
NodePid                                         type    VpNodeId  Account                                     Status
-------                                         ----    --------  -------                                     ------
QmcFyjWf8w5o3N175RneZYzp4VmCb1Usi68K2A4EEtr5g8  vpNode  5         0xaE6934E4071082CA2a83F7FEf29ede10C1bc743C  logouting
QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  vpNode  4         0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  available
QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  vpNode  2         0x79a1215469FaB6f9c63c1816b45183AD3624bE34  available
QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  vpNode  1         0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  available
QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  vpNode  3         0x97c8B516D19edBf575D72a172Af7F418BE498C37  available
```




#### bitxhub client governance role

身份管理。命令说明如下：

```shell
NAME:
   BitXHub client governance role - role manage command

USAGE:
   BitXHub client governance role command [command options] [arguments...]

COMMANDS:
   status    query role status by role id
   register  register role
   update    update node for auditAdmin
   freeze    freeze role by role id
   activate  activate role by role id
   logout    logout role by role id
   all       query all roles info
```

**子命令**

##### bitxhub client governance role status
查询身份状态。命令说明如下：

```shell
NAME:
   BitXHub client governance role status - query role status by role id

USAGE:
   BitXHub client governance role status [command options] [arguments...]

OPTIONS:
   --id value  Specify role id(address)
```

**参数解释**

- `id`：必选参数，身份id，即身份账户地址。

**示例说明**

```shell
# 指定节点为bitxhub初始的超级管理员，状态为可用
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8
role 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8 is available
```

##### bitxhub client governance role register
注册身份。命令说明如下：

```shell
NAME:
   BitXHub client governance role register - register role

USAGE:
   BitXHub client governance role register [command options] [arguments...]

OPTIONS:
   --address value  Specify role address(id)
   --type value     Specify role type, one of governanceAdmin or auditAdmin (default: "governanceAdmin")
   --nodePid value  Specify node pid for auditAdmin, only useful for auditAdmin
   --reason value   Specify register reason
```

**参数解释**

- `address`：必选参数，身份账户地址，即身份id；
- `id`：必选参数，共识节点id序号；
- `type`：必选参数，身份类型，governanceAdmin为治理管理员， auditAdmin为审计管理员，默认为治理管理员，审计管理员目前可以注册但实际未实现；
- `nodePid`：审计管理员绑定的审计节点pid，仅对审计管理员有效；
- `reason`：注册理由。

**示例说明**

```shell
# 注册治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role register --address 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F --type governanceAdmin
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2

# 发起注册提案后，身份状态为注册中
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is registering
```

##### bitxhub client governance role update
更新审计管理员绑定的节点信息。命令说明如下：

```shell
NAME:
   BitXHub client governance role update - update node for auditAdmin

USAGE:
   BitXHub client governance role update [command options] [arguments...]

OPTIONS:
   --id value       Specify auditAdmin id
   --nodePid value  Specify node pid
   --reason value   Specify update reason
```

**参数解释**
- `id`：必选参数，身份id；
- `nodePid`：必须参数，新的审计节点pid，节点需要为已经注册的审计节点；
- `reason`：更新理由。

_此命令仅对审计管理员有效，但审计节点与审计管理员功能在当前版本尚未完全实现，故不推荐使用此命令且不给出示例，后续版本将会继续完善。_

##### bitxhub client governance role freeze
冻结身份。命令说明如下：

```shell
NAME:
   BitXHub client governance role freeze - freeze role by role id

USAGE:
   BitXHub client governance role freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify role id
   --reason value  Specify freeze reason
```

**参数解释**
- `id`：必选参数，身份id；
- `reason`：冻结理由。

**示例说明**
```shell
# 身份注册提案投票功后状态为可用
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is available

# 冻结身份，发起提案
$ bitxhub --repo $bitxhub_node_repo client governance role freeze --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3

# 冻结提案发起后身份状态为冻结中
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is freezing
```

##### bitxhub client governance role activate
激活身份。命令说明如下：

```shell
NAME:
   BitXHub client governance role activate - activate role by role id

USAGE:
   BitXHub client governance role activate [command options] [arguments...]

OPTIONS:
   --id value      Specify role id
   --reason value  Specify activate reason
```

**参数解释**
- `id`：必选参数，身份id；
- `reason`：激活理由。

**示例说明**
```shell
# 身份冻结提案投票功后状态为已冻结
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is frozen

# 激活身份，发起提案
$ bitxhub --repo $bitxhub_node_repo client governance role activate --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-4

# 激活提案发起后身份状态为激活中
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is activating
```

##### bitxhub client governance role logout
注销身份。命令说明如下：

```shell
NAME:
   BitXHub client governance role logout - logout role by role id

USAGE:
   BitXHub client governance role logout [command options] [arguments...]

OPTIONS:
   --id value      Specify role pid
   --reason value  Specify logout reason
```

**参数解释**
- `pid`：必选参数，节点pid；
- `reason`：注销理由。

**示例说明**
```shell
# 身份当前状态为激活中
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is activating

# 注销身份，发起提案
$ bitxhub --repo $bitxhub_node_repo client governance role logout --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-5

# 注销提案发起后身份状态为注销中
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is logouting
```

##### bitxhub client governance role all
查询所有身份。

**示例说明**
```shell
# 查询所有身份信息
$ bitxhub --repo $bitxhub_node_repo client governance role all

# 控制台打印：bitxhub启动初始的四个超级治理管理员始终为可用状态，一个logouting状态的治理管理员为后续注册的普通治理管理员
RoleId                                      type             Weight  NodePid  Status
------                                      ----             ------  -------  ------
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  governanceAdmin  2                available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  governanceAdmin  2                available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  governanceAdmin  1                logouting
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  governanceAdmin  2                available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  governanceAdmin  2                available
```







#### bitxhub client governance dapp

dapp管理。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp - dapp manage command

USAGE:
   BitXHub client governance dapp command [command options] [arguments...]

COMMANDS:
   status    query dapp status by dapp id
   myDapps   query dapps by owner addr
   register  register dapp
   update    update dapp
   freeze    freeze dapp by dapp id
   activate  activate dapp by dapp id
   transfer  transfer dapp to other user
   confirm   confirm dapp transfer
   evaluate  evaluate dapp
```

**子命令**

##### bitxhub client governance dapp status
查询dapp状态。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp status - query dapp status by dapp id

USAGE:
   BitXHub client governance dapp status [command options] [arguments...]

OPTIONS:
   --id value  Specify dapp id
```

**参数解释**

- `id`：必选参数，dapp id，dapp注册成功后后打印出id。


##### bitxhub client governance dapp myDapps
查询当前用户拥有dapp列表。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp myDapps - query dapps by owner addr

USAGE:
   BitXHub client governance dapp myDapps [command options] [arguments...]

OPTIONS:
   --addr value  Specify user addr
```

**参数解释**

- `addr`：必选参数，当前用户账户地址。

##### bitxhub client governance dapp register
注册dapp。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp register - register dapp

USAGE:
   BitXHub client governance dapp register [command options] [arguments...]

OPTIONS:
   --name value           Specify dapp name
   --type value           Specify dapp type, one of tool, application, game and others
   --desc value           Specify dapp description
   --url value            Specify dapp url
   --contractAddrs value  Specify dapp contract addr. If there are multiple contract addresses, separate them with ','
   --permission value     Specify the addr of users which are not allowed to see the dapp. If there are multiple contract addresses, separate them with ','
   --reason value         Specify register reason
```

**参数解释**

- `name`：必选参数，dapp名称；
- `type`：必选参数，dapp类型，目前支持`tool`,`application`,`game`和`others`四种类型；
- `desc`：必选参数，dapp描述信息；
- `url`：必选参数，dapp url；
- `contractAddrs`：必选参数，dapp合约地址列表，如果有多个用逗号隔开，合约地址需要为bitxhub上部署过的合约；
- `permission`：必选参数，dapp使用账户黑名单，如果有多个用逗号隔开；
- `reason`：注册理由。


##### bitxhub client governance dapp update
更新dapp信息。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp update - update dapp

USAGE:
   BitXHub client governance dapp update [command options] [arguments...]

OPTIONS:
   --id value             Specify dapp id
   --name value           Specify dapp name
   --type value           Specify dapp type, one of tool, application, game and others
   --desc value           Specify dapp description
   --url value            Specify dapp url
   --contractAddrs value  Specify dapp contract addr. If there are multiple contract addresses, separate them with ','
   --permission value     Specify the addr of users which are not allowed to see the dapp. If there are multiple contract addresses, separate them with ','
   --reason value         Specify register reason
```

**参数解释**
- `id`：必选参数，dapp id；
- `name`：必选参数，dapp名称；
- `type`：必选参数，dapp类型，目前支持`tool`,`application`,`game`和`others`四种类型；
- `desc`：必选参数，dapp描述信息；
- `url`：必选参数，dapp url；
- `contractAddrs`：必选参数，dapp合约地址列表，如果有多个用逗号隔开，合约地址需要为bitxhub上部署过的合约；
- `permission`：必选参数，dapp使用账户黑名单，如果有多个用逗号隔开；
- `reason`：更新理由。

##### bitxhub client governance dapp freeze
冻结dapp。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp freeze - freeze dapp by dapp id

USAGE:
   BitXHub client governance dapp freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --reason value  Specify freeze reason
```

**参数解释**
- `id`：必选参数，dapp id；
- `reason`：冻结理由。


##### bitxhub client governance dapp activate
激活dapp。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp activate - activate dapp by dapp id

USAGE:
   BitXHub client governance dapp activate [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --reason value  Specify activate reason
```

**参数解释**
- `id`：必选参数，dapp id；
- `reason`：激活理由。

##### bitxhub client governance dapp transfer
转让dapp。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp transfer - transfer dapp to other user

USAGE:
   BitXHub client governance dapp transfer [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --addr value    Specify new owner addr
   --reason value  Specify activate reason
```

**参数解释**
- `id`：必选参数，dapp id；
- `addr`：必选参数，dapp接收账户地址；
- `reason`：转让理由。


##### bitxhub client governance dapp confirm
dapp接收确认。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp confirm - confirm dapp transfer

USAGE:
   BitXHub client governance dapp confirm [command options] [arguments...]

OPTIONS:
   --id value  Specify dapp id
```

**参数解释**
- `id`：必选参数，dapp id；

##### bitxhub client governance dapp evaluate
dapp评分。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp evaluate - evaluate dapp

USAGE:
   BitXHub client governance dapp evaluate [command options] [arguments...]

OPTIONS:
   --id value     Specify dapp id
   --desc value   Specify evaluate desc
   --score value  Specify evaluate score (default: 0)
```

**参数解释**
- `id`：必选参数，dapp id；
- `desc`：必选参数，dapp评价信息；
- `score`：dapp评价分数，评分应在[0,5]区间内，默认为0；


**示例说明**

```shell
# 注册dapp
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance dapp register --name dapp1 --desc "desc"  --contractAddrs 0x615bAa02f2751f3378c8c57F9eB084daD6A55a92 --url "url" --type tool
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-6, dapp id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0

# 发起注册提案后，dapp状态为注册中
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance dapp status --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
dapp 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0 is registering

# 治理投票省略，注册提案已通过

# 冻结dapp
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance dapp freeze --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-7

# 治理投票省略，冻结提案已通过

# 激活dapp
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance dapp activate --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-8

# 治理投票省略，激活提案已通过

# 转让dapp
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance dapp transfer --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0 --addr 0x97c8B516D19edBf575D72a172Af7F418BE498C37
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-9

# 治理投票省略，转让提案已通过

# 查询dapp接收账户所有dapp
$ bitxhub --repo ~/work/bitxhub/scripts/build/node1 client governance dapp myDapps --addr 0x97c8B516D19edBf575D72a172Af7F418BE498C37
========================================================================================
Id                                            Name  Type  Owner                                       Createtime           Score  Status
--                                            ----  ----  -----                                       ----------           -----  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  1     tool  0x97c8B516D19edBf575D72a172Af7F418BE498C37  1645499462680860000  0      available

# 接收账户确认接收dapp（此步骤意义不大，不做也可）
$ bitxhub --repo ~/work/bitxhub/scripts/build/node3 client governance dapp confirm --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
confirm dapp transfer success

# dapp所有者对其进行评价
$ bitxhub --repo ~/work/bitxhub/scripts/build/node3 client governance dapp evaluate --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0 --desc "good dapp" --score 5
evaluate dapp success
```


#### bitxhub client governance strategy

投票策略管理。命令说明如下：

```shell
NAME:
   BitXHub client governance strategy - proposal strategy command

USAGE:
   BitXHub client governance strategy command [command options] [arguments...]

COMMANDS:
   all  query all proposal strategy
```

**子命令**
##### bitxhub client governance strategy all
查询所有管理模块投票策略。

**示例说明**
```shell
$ bitxhub --repo $bitxhub_node_repo client governance strategy all

#控制台输出，所有模块的投票策略都为默认简单多数投票策略，即赞成票数大于半数时提案通过
module        strategy        Extra
------        --------        -----
appchain_mgr  SimpleMajority  a > 0.5 * t
node_mgr      SimpleMajority  a > 0.5 * t
dapp_mgr      SimpleMajority  a > 0.5 * t
role_mgr      SimpleMajority  a > 0.5 * t
rule_mgr      SimpleMajority  a > 0.5 * t
```