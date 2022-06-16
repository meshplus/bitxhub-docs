# 节点管理
中继链中继模式提供对中继链自身节点的增删管理功能

## 1 概述
中继链自身节点有共识节点和审计节点两种，中继链初始启动的节点都是共识节点。

### 共识节点
参与中继链共识

关于共识节点，目前中继链只支持rbft共识算法的节点增删管理。而rbft共识只在BitXHub商业版本中支持，启动商业版本的BitXHub需要获取相应的LICENSE，并将LICENSE放在每个BitXHub节点的根目录下。  
_（下文中的关于共识节点使用方法的介绍均为rbft共识算法下的共识节点）_

此外，为了保证治理结果的不确定性不影响中继链上的节点共识，要求共识节点的治理操作只能逐个进行，即当有共识节点正在注册或注销过程中时，无法发起新的共识节点注册。


### 审计节点

不参与共识，只能同步数据

关于审计节点，中继链上支持对审计节点信息的注册、注销、更新、绑定，实际的审计节点需要另外启动，中继链上的治理操作可以对审计节点同步数据的权限进行限制

中继链的节点管理功能主要提供关于共识节点的注册、注销功能，关于审计节点的注册、更新、注销功能。

## 2 共识节点注册

### 2.1 功能介绍
目前中继链支持在使用rbft共识算法的情况下增加共识节点，注册过程中共识节点的一般状态转换如下：  
`unavailable` --> `registering` --> `available`  

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 2.2 使用方法

#### bitxhub client governance node register

注册共识节点相关参数解释 *（未列出参数与共识节点无关可忽略）*：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录；
- `--account`：指定新节点account信息，共识节点通常为节点配置目录下key.json对应的地址；
- `--type`：指定新节点类型，默认为共识节点`vpNode`；
- `--pid`：指定新共识节点的pid信息，通常为节点配置目录下certs/node.priv对应的pid；
- `--id`：指定新共识节点的id信息，通常为共识节点的递增序号，但不要求一定递增，新赠共识节点的id需要比现有所有可用共识节点的id大即可，即，4个创世共识节点的id分别为1/2/3/4，那么新增的第一个共识节点id>=5即可；
- `--reason`：可选参数，说明注册理由。

_（注意，注册共识节点需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 2.3 示例说明

前提条件：

- 商业版本bitxhub已经启动，使用rbft共识，使用默认`ZeroPermission`投票策略，且相应二进制文件已编译

```shell
# 1. 准备新共识节点的配置

# 1.1 线下向CA证书的代理机构请求准入证书。bitxhub命令行提供了生成证书功能
# 生成节点证书私钥
$ pbitxhub cert priv gen --name node --target .
node.priv key is generated under directory $(pwd)
# 获取私钥的csr文件
$ bitxhub cert csr --key ./node.priv --org Node --target .
# 代理结构颁发证书
$ bitxhub cert issue --csr node.csr --is_ca false --key $bitxhub_project_repo/scripts/certs/agency.priv --cert $bitxhub_project_repo/scripts/certs/agency.cert --target .
# 当前目录下的node.cert即为需要的节点准入证书
$ tree
.
├── node.cert
├── node.csr
└── node.priv

# 1.2 线下准备新节点的私钥（这个私钥与上文中用户获取证书的私钥不同，此私钥与节点network.toml中的account对应）
# 生成Secp256k1加密算法的私钥
$ bitxhub key gen --target . --passwd bitxhub
key.json key is generated under directory $(pwd)

# 1.3 准备节点启动配置文件
# 生成节点配置文件模板
$ bitxhub --repo ./node5 init
initializing bitxhub at ./node5
# 拷贝节点ca证书及ca证书私钥到节点配置目录
$ cp ./node.cert ./node5/certs/
$ cp ./node.priv ./node5/certs/
# 拷贝agency证书及ca根证书到节点配置目录
$ cp $bitxhub_project_repo/scripts/certs/node1/certs/agency.cert ./node5/certs/
$ cp $bitxhub_project_repo/scripts/certs/node1/certs/ca.cert ./node5/certs/
# 拷贝节点私钥到配置目录
$ cp ./key.json ./node5
# 拷贝商业版本许可到配置目录
$ cp $license_repo/LICENSE ./node5
# 检查完整的新节点配置文件
$ tree node5
node5
├── LICENSE
├── README.md
├── appchain
│   ├── eth_header.json
│   └── eth_header1.json
├── bitxhub.toml
├── certs
│   ├── agency.cert
│   ├── agency.priv
│   ├── ca.cert
│   ├── node.cert
│   ├── node.priv
│   ├── server.key
│   └── server.pem
├── key.json
├── network.toml
└── order.toml

# 1.4 修改节点配置信息
# 修改bitxhub.toml配置信息
$ vim ./node5/bitxhub.toml
# 修改bitxhub.toml中端口信息和共识类型，需要修改的配置项修改后如下：
[port]
  jsonrpc = 8885
  grpc = 60015
  gateway = 9095
  pprof = 53125
  monitor = 40015
[order]
  type = "rbft"
# 在network.toml中添加新节点的网络信息，bitxhub命令行提供了根据相关私钥查看节点pid和account信息的功能
# 查看新节点pid信息
$ bitxhub cert priv pid --path ./node5/certs/node.priv
QmVnKh44L2onm763Wqizprm7WNuwCpAMwtWWUytgxijHHy
# 查看新节点account信息
$ bitxhub key show --path ./node5/key.json
private key: 5ddfbd627c97563f922d104654adcbb6352fd64e783abaea8d7879e8e96543e4
public key: 04b442e315c200f5d2261b5486e28f7d9d3e366d6f252c492dc15b232777fb720f655ec075fbd01a25eec5662994a10b1e7b8df12be134581c02d6e6fad57459a8
address: 0x8fa7257AAD61f67957c77a7559c746561f39e202
# 修改network.toml
$ vim ./node5/network.toml
# 将新节点网络信息添加到network.toml末尾，并修改id和new参数，修改后netwokr.toml如下：
id = 5 # self id
n = 4 # the number of primary vp nodes
new = true # track whether the node is a new node
[[nodes]]
account = "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013"
hosts = ["/ip4/127.0.0.1/tcp/4001/p2p/"]
id = 1
pid = "QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL"
[[nodes]]
account = "0x79a1215469FaB6f9c63c1816b45183AD3624bE34"
hosts = ["/ip4/127.0.0.1/tcp/4002/p2p/"]
id = 2
pid = "QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4"
[[nodes]]
account = "0x97c8B516D19edBf575D72a172Af7F418BE498C37"
hosts = ["/ip4/127.0.0.1/tcp/4003/p2p/"]
id = 3
pid = "QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS"
[[nodes]]
account = "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"
hosts = ["/ip4/127.0.0.1/tcp/4004/p2p/"]
id = 4
pid = "QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy"
[[nodes]]
account = "0x8fa7257AAD61f67957c77a7559c746561f39e202"
hosts = ["/ip4/127.0.0.1/tcp/4005/p2p/"]
id = 5
pid = "QmVnKh44L2onm763Wqizprm7WNuwCpAMwtWWUytgxijHHy"

# 建议完成以上步骤后拷贝一份已经准备好的节点配置文件备份，以备后续步骤如果出错不需要重新准备
$ cp -r ./node5 ./node5_backup

# 2 启动新的共识节点
$ bitxhub --repo ./node5 start
···（部分日志省略）
ERRO[2022-02-17T15:37:20.349] Connect failed                                error="dial backoff" module=p2p node=2
ERRO[2022-02-17T15:37:20.349] Connect failed                                error="dial backoff" module=p2p node=3
ERRO[2022-02-17T15:37:20.349] Connect failed                                error="dial backoff" module=p2p node=1
ERRO[2022-02-17T15:37:20.349] Connect failed                                error="dial backoff" module=p2p node=4
···（后续日志省略）

# 3 查询所有节点：四个初始的共识节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type    Pid                                             VpNodeId  Name  Permission  Status     AuditAdminAddr
-------                                     ----    ---                                             --------  ----  ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode  QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                           available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode  QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                           available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode  QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                           available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode  QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                           available

# 4 注册新的共识节点
$ bitxhub --repo $bitxhub_node_repo client governance node register --account 0x8fa7257AAD61f67957c77a7559c746561f39e202 --type vpNode --pid QmVnKh44L2onm763Wqizprm7WNuwCpAMwtWWUytgxijHHy --id 5
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
# ZeroPermission策略下管理员投票可省略，即已注册成功
# 等待少许时间后，新启动的node5节点将会打印出启动成功后的bitxhub loge

# 5 查询所有节点：五个可用的共识节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type    Pid                                             VpNodeId  Name  Permission  Status     AuditAdminAddr
-------                                     ----    ---                                             --------  ----  ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode  QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                           available
0x8fa7257AAD61f67957c77a7559c746561f39e202  vpNode  QmVnKh44L2onm763Wqizprm7WNuwCpAMwtWWUytgxijHHy  5                           available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode  QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                           available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode  QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                           available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode  QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                           available
```

## 3 共识节点注销
### 3.1 功能介绍
共识节点注销时的一般状态转换如下：  
`available` --> `logouting` --> `forbidden`  

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

删除节点有以下几点注意：  
（1）初始启动的节点不可以删除，通常为初始的4个节点；  
（2）cluster模式删除节点时需要保证共识节点个数不少于4个

### 3.2 使用方法

#### bitxhub client governance node logout

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--account`：指定待删除节点account信息，共识节点通常为节点配置目录下key.json对应的地址
- `--reason`：可选参数，说明注销理由

_（注意，注销共识节点需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 3.3 示例说明

前提条件：

- 接2.3示例
```shell
# 查询所有节点：五个可用的共识节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type    Pid                                             VpNodeId  Name  Permission  Status     AuditAdminAddr
-------                                     ----    ---                                             --------  ----  ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode  QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                           available
0x8fa7257AAD61f67957c77a7559c746561f39e202  vpNode  QmVnKh44L2onm763Wqizprm7WNuwCpAMwtWWUytgxijHHy  5                           available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode  QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                           available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode  QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                           available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode  QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                           available

# 注销共识节点5
$ bitxhub --repo $bitxhub_node_repo client governance node logout --account 0x8fa7257AAD61f67957c77a7559c746561f39e202
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
# ZeroPermission策略下管理员投票可省略，即已注销成功

# 注销成功后，共识节点5会被中继链主动端口，打印日志如下：
INFO[2022-02-17T16:06:37.514] Delete node [ID: 5, peerInfo: id:5 pid:"QmVnKh44L2onm763Wqizprm7WNuwCpAMwtWWUytgxijHHy" account:"0x8fa7257AAD61f67957c77a7559c746561f39e202" hosts:"/ip4/127.0.0.1/tcp/4005/p2p/" ]   module=p2p
network config file changed:  "node5/network.toml": WRITE
unmarshal config:  result must be addressable (a pointer)
WARN[2022-02-17T16:06:37.543] Receive missing transaction [account: 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, nonce: 0, hash: 0xe7789972fed4b61A8fcE043168CB2C99454Cb3C8228d6b7D9dCaD5e4028d11b3] from primary, we will replace the old transaction  module=order
INFO[2022-02-17T16:06:37.543] ======== THIS NODE WILL STOP IN 3 SECONDS     module=order
INFO[2022-02-17T16:06:37.543] Replica 5 need to process seqNo 1 as a null request  module=order
INFO[2022-02-17T16:06:37.543] Replica 5 persist view=10/N=4 after updateN   module=order
INFO[2022-02-17T16:06:37.543] ======== Replica 5 finished updateN, primary=3, n=4/f=1/view=10/h=0  module=order
INFO[2022-02-17T16:06:40.548] RBFT stopping...                              module=order
INFO[2022-02-17T16:06:40.548] ======== RBFT stopped!                        module=order
INFO[2022-02-17T16:06:40.552] Disconnect peer [ID: 2, Pid: QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4]  module=p2p
INFO[2022-02-17T16:06:40.552] Disconnect peer [ID: 3, Pid: QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS]  module=p2p
INFO[2022-02-17T16:06:40.552] Disconnect peer [ID: 4, Pid: QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy]  module=p2p
INFO[2022-02-17T16:06:40.552] Disconnect peer [ID: 1, Pid: QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL]  module=p2p
INFO[2022-02-17T16:06:40.553] ======== THIS NODE HAS BEEN DELETED!!!        module=order

# 查询所有节点：四个可用的共识节点，一个已注销的共识节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type    Pid                                             VpNodeId  Name  Permission  Status     AuditAdminAddr
-------                                     ----    ---                                             --------  ----  ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode  QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                           available
0x8fa7257AAD61f67957c77a7559c746561f39e202  vpNode  QmVnKh44L2onm763Wqizprm7WNuwCpAMwtWWUytgxijHHy  5                           forbidden
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode  QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                           available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode  QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                           available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode  QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                           available
```

## 4 注册审计节点

### 4.1 功能介绍

中继链管理员可以向中继链上注册审计节点，需要注意的是注册审计节点时需要携带不为空的应用链审计权限（即需要至少注册一条应用链后再注册审计节点）。   
注册过程中审计节点的状态转换如下：

`unavailable` --> `registering` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 4.2 使用方法

#### bitxhub client governance node register

注册审计节点相关参数解释 *（未列出参数与审计节点无关可忽略）*：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--account`：指定新节点account信息，审计节点通常为审计节点私钥key.json对应的地址
- `--type`：指定新节点类型，默认为共识节点`vpNode`，注册审计节点时需要明确指定为`nvpNode`
- `--name`：指定新审计节点的name，可随意自定义
- `--permission`：指定新审计节点的应用链审计权限，即一个应用链id列表，不可以为空，如有多条应用链以逗号隔开
- `--reason`：可选参数，说明注册理由

_（注意，注册审计节点需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 4.3 示例说明

前提条件：

- bitxhub已经启动，使用默认raft共识，使用默认`ZeroPermission`投票策略，且相应二进制文件已编译
- pier配置文件已准备好，对应一个`Fabric V1.4.3`类型应用链
- pier配置文件下的key.json对应中继链上一个有足够交易费余额的账户

```shell
# 部署验证规则
$ pier --repo $pier_repo rule deploy --path $pier_repo/fabric/validating.wasm
Deploy rule successfully: 0x615bAa02f2751f3378c8c57F9eB084daD6A55a92

# 注册应用链
$ pier --repo $pier_repo appchain register --appchain-id appchain1 --name 应用链A --type "Fabric V1.4.3" --trustroot $pier_repo/fabric/fabric.validators --broker-cid "mychannel" --broker-ccid "broker" --broker-v 1 --desc "test fabric"  --master-rule 0x615bAa02f2751f3378c8c57F9eB084daD6A55a92 --rule-url "https://bitxhub.cn/" --admin 0x89cc4e498e073fe3A54524239226187f9Dc6414d
Register appchain successfully, wait for proposal 0x89cc4e498e073fe3A54524239226187f9Dc6414d-0 to finish.
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 查询所有节点：4个初始的共识节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available

# 注册审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node register --account 0x96B5619A637639E1651EADD49CDA5B8EF4882331 --type nvpNode --name node1 --permission appchain1
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 查询所有节点：4个初始的共识节点，1个新注册的审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node1  appchain1   available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available
```

## 5 更新审计节点

### 5.1 功能介绍

中继链管理员可以更新中继链上记录的审计节点信息，具体可以更新的是审计节点的名称和权限。   
更新过程中审计节点的状态转换如下：

`available` --> `updating` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 5.2 使用方法

#### bitxhub client governance node update

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--account`：指定待更新节点account信息，通常为审计节点私钥key.json对应的地址
- `--name`：指定待更新节点的name，可随意自定义，但不可以和其他审计节点重名
- `--permission`：指定新待更新节点的应用链审计权限，即一个应用链id列表，不可以为空，如有多条应用链以逗号隔开
- `--reason`：可选参数，说明更新理由

_（注意，更新审计节点需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 5.3 示例说明

前提条件：

- 接4.3示例
```shell
# 查询所有节点：4个初始的共识节点，1个名称为node1权限为appchain1的审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node1  appchain1   available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available

# 更新审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node update --account 0x96B5619A637639E1651EADD49CDA5B8EF4882331 --name node2 --permission appchain1
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
# ZeroPermission策略下管理员投票可省略，即已更新成功

# 查询所有节点：4个初始的共识节点，1个名称更新为node2权限为appchain1的审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node2  appchain1   available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available
```

## 6 注销审计节点

### 6.1 功能介绍

中继链管理员可以注销中继链上已注册的审计节点，审计节点注销后即失去从中继链上同步数据的权限，与其绑定的审计管理员也会被系统自动冻结（详见身份管理中关于审计管理员的介绍）。  
审计节点注销时的一般状态转换如下：  
`*` --> `logouting` --> `forbidden`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 6.2 使用方法

#### bitxhub client governance node logout

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--account`：指定待删除节点account信息，审计节点通常为私钥key.json对应的地址
- `--reason`：可选参数，说明注销理由

_（注意，注销审计节点需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 6.3 示例说明

前提条件：
- 接5.3示例

```shell
# 查询所有节点：4个初始的共识节点，1个可用的审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node2  appchain1   available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available

# 注销审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node logout --account 0x96B5619A637639E1651EADD49CDA5B8EF4882331
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
# ZeroPermission策略下管理员投票可省略，即已注销成功

# 查询所有节点：4个初始的共识节点，1个已注销的审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node2  appchain1   forbidden
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available
```

## 7 其他功能
中继链还提供了其他查询节点信息的功能。
以下给出的示例说明的前提条件均为接上文6.3示例

### 7.1 查询所有节点
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node2  appchain1   forbidden
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available
```

### 7.2 查询某个节点的状态
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance node status --account 0x96B5619A637639E1651EADD49CDA5B8EF4882331
node  is forbidden
```