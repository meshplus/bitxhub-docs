
# bitxhub client
## 功能

该命令`bitxhub client`提供中继链节点的客户端操作命令。
它包含了账户信息查询、中继链信息、区块查询、节点网络信息、回执查询、交易操作、验证者集查询、删除共识节点、提案治理。
子命令信息如下:
```
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client

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

## 参数
- `gateway`: 指定客户端http服务地址，如果节点开启了TLS功能，则需要传递https地址；
- `cert`: 指定节点的CA证书，如果节点开始了TLS功能，需要传递；
```
OPTIONS:
--gateway value  Specific gateway address (default: "http://localhost:9091/v1/")
--cert value     Specific ca cert file if https is enabled
--help, -h       show help
```
## 子命令
### bitxhub client account
账户信息查询
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client account 0x79a1215469FaB6f9c63c1816b45183AD3624bE34                                         

{
  "type": "normal",
  "balance": 100000000,
  "contract_count": 0,
  "code_hash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}

```
### bitxhub client chain
中继链信息查询
```bigquery
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
- `normal`: 已处在共识状态
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client chain meta
{"height":"1","block_hash":"0x0BF41c03bE038e389470dBdC9ed53DF897F178921520AF37877188d1759A7068"}

bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client chain status
normal
```
### bitxhub client block
区块信息查询
- 可指定区块号或区块Hash
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client block 1
{"block_header":{"number":"1","state_root":"0xC3A3B54F593e638D8a9FAbf66b781EFcb14D0f013D7c3E3a02ef698294f6BBF5"},"block_hash":"0x0BF41c03bE038e389470dBdC9ed53DF897F178921520AF37877188d1759A7068","signature":"74DK6FeGnAYBJ0pJ4bLwIaT9cp9YwIscenKsRc7myXUBQfvvLKhIpZqPJkf8tAI7V8u89SLOAdz94fo/ajKi+wA="}

```
### bitxhub client network
全网节点信息
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client network

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
交易回执查询
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client receipt 0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E

{
  "tx_hash": "0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E",
  "ret": null,
  "status": 0,
  "events": []
}

```

### bitxhub client tx
简单交易
##### 交易发送
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client tx send --key scripts/build/node1/key.json --to 0x97c8B516D19edBf575D72a172Af7F418BE498C37 --amount 2 --type 0

{"tx_hash":"0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E"}
```
#### 交易查询
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client tx get 0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E

{"tx":{"from":"0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","to":"0x97c8B516D19edBf575D72a172Af7F418BE498C37","timestamp":"1638168425560507000","transaction_hash":"0x08ab0E9175ae67Aed7a085D6813C156e4ED3b49cDf902e02280F6a62d7AaD61E","payload":"EAI=","nonce":"2","signature":"mdOdSzAOTDxfZ0VphvyDyir1lE8w31rUaSTtnj1us9NMhtUPVHz5bLcOBdZrmUfnaVhL3xGQxK3/P7WHmihxIQA="},"tx_meta":{"block_hash":"HoMgbS+NeCRds5Qc/58NfHXA+jVCuhUcsc7p9IvJxMc=","block_height":"3"}}
```
### bitxhub client validators
全网验证人信息
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client validators

[
  "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
  "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
  "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
  "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"
]
```

### bitxhub client delVPNode
动态删除共识节点
```bigquery
NAME:
   BitXHub client delVPNode - delete a vp node

USAGE:
   BitXHub client delVPNode [command options] [arguments...]

OPTIONS:
   --pid value  pid of vp node
```

### bitxhub client governance
提案治理命令
```bigquery
NAME:
   BitXHub client governance - governance command

USAGE:
   BitXHub client governance command [command options] [arguments...]

COMMANDS:
   vote       vote to a proposal
   proposals  query proposals based on the condition
   chain      appchain manage command

```
#### 提案投票
-   命令参数
```bigquery
NAME:
   BitXHub client governance vote - vote to a proposal

USAGE:
   BitXHub client governance vote [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --info value    voting information, approve or reject
   --reason value  reason to vote

```
- 样例
```bigquery
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client governance vote --info approve --reason test --id 0x9A601dbebf20f3E3362241810C2229F7f97329d6-0

vote successfully! 
0x9A601dbebf20f3E3362241810C2229F7f97329d6-0

```
#### 提案查询
- id: 提案id
- type: 提案类型，当前支持应用链管理
- status: 提案状态，是否通过
- from: 提案者地址
```bigquery
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

#### 应用链管理
- status:查询应用链状态
- freeze:应用链冻结
- activate:应用链激活
```bigquery
NAME:
   BitXHub client governance chain - appchain manage command

USAGE:
   BitXHub client governance chain command [command options] [arguments...]

COMMANDS:
   status    query chain status by chain id
   freeze    freeze appchain by chain id
   activate  activate chain by chain id

```

