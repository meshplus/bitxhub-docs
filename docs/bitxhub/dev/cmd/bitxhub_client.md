
# bitxhub client
## 功能

该命令`bitxhub client`提供中继链节点的客户端操作命令。
它包含了账户信息查询、中继链信息、区块查询、节点网络信息、回执查询、交易操作、验证者集查询、删除共识节点、提案治理、did指令。
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
   did         did command                     
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
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client block 3
{
  "block_header": {
    "number": "3",
    "state_root": "0xA4E28e95578F5b4276C766810e9673F674a850EE876b319956761fbA1d3d8269",
    "tx_root": "0x7ffC813435b4D09acE5A2c6A33a51cd4bDf3b114cb292a3Ad2ca29c44ec8d5FB",
    "receipt_root": "0x2b57252fCe72cA03e7C28Ee220Fe8946fD0D6038Ca1398a19A8D5c354aFF68Bc",
    "parent_hash": "0xd3363F8f667c891c388fED72BbBE091e6A888b2B70920FA9e115539848e8C9B5",
    "timestamp": "1638243559049301000",
    "version": "MS4wLjA=",
    "Bloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
  },
  "transactions": {
    "Transactions": [
      {
        "from": "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
        "to": "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
        "timestamp": 1638243558643356000,
        "transaction_hash": "0x1947a1339F0C27157f76bB40ea4E68b1a9B4b1AFAd0055fBE24885CC6Ea87e63",
        "payload": "EgEy",
        "signature": "SMIlWc7qFn6r3WdcvJslSSjAl/PzEW0gIxZB+Ey6zRwFx3bjJdJD+JQ1xPAjyUXP8M+Bc2szRN9KoIAw85WIvwE="
      }
    ]
  },
  "block_hash": "0x39e9364747342A9A48055C13eBbDDE3F19415bfaB19E58375CDa8Eccb4d5c7DB",
  "signature": "qQIIEtSdXUxnVD8lXRrvwNJ0gUk4DS9X8w9c9EvlHMsbBSrVSy6+0mUx5ZT/m2coutpzSupDeRiFo5bfmTRK4gE="
}

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
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client receipt 0x1947a1339F0C27157f76bB40ea4E68b1a9B4b1AFAd0055fBE24885CC6Ea87e63

{
  "tx_hash": "0x1947a1339F0C27157f76bB40ea4E68b1a9B4b1AFAd0055fBE24885CC6Ea87e63",
  "ret": null,
  "status": 0,
  "events": [],
  "gas_used": "21000",
  "bloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"

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
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client tx get 0x1947a1339F0C27157f76bB40ea4E68b1a9B4b1AFAd0055fBE24885CC6Ea87e63

{
  "txs": {
    "Transactions": [
      {
        "from": "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
        "to": "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
        "timestamp": 1638243558643356000,
        "transaction_hash": "0x1947a1339F0C27157f76bB40ea4E68b1a9B4b1AFAd0055fBE24885CC6Ea87e63",
        "payload": "EgEy",
        "signature": "SMIlWc7qFn6r3WdcvJslSSjAl/PzEW0gIxZB+Ey6zRwFx3bjJdJD+JQ1xPAjyUXP8M+Bc2szRN9KoIAw85WIvwE="
      }
    ]
  },
  "tx_meta": [
    {
      "block_hash": "Oek2R0c0KppIBVwT673ePxlBW/qxnlg3XNqOzLTVx9s=",
      "block_height": "3"
    }
  ]
}
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
   rule      rule manage command
   node      node manage command
   role      role manage command
   dapp      dapp manage command

```
#### 规则管理
```bigquery
NAME:
   BitXHub client governance rule - rule manage command

USAGE:
   BitXHub client governance rule command [command options] [arguments...]

COMMANDS:
   all        query all rules info of one chain
   available  query available rule address of a chain
   status     query rule status by rule address and chain id
   update     update master rule with chain id

```
#### 节点管理
```bigquery
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
#### 角色管理
```bigquery
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
#### dapp管理
```bigquery
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
### bitxhub client did
提案治理命令
```bigquery
NAME:
   BitXHub client did - did command

USAGE:
   BitXHub client did command [command options] [arguments...]

COMMANDS:
   init      init method-registry contract
   addAdmin  add admin role for method-registry contract

```
