# 中继模式管理

## pier appchain

在V1.18版本，中继链IBTP2.0模式将跨链单位从应用链级别细粒度为应用链某个服务。中继链支持对应用链与服务的管理，包括注册、更新、冻结、注销应用链等功能。

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



## 1. 验证规则部署

参考<a href="../../dev/cmd/pier_cmd/#ruleAnchor">验证规则部署命令</a>。



## 2. 应用链注册
### pier appchain register 
对于需要加入跨链网络使用中继链进行跨链的应用链，需要首先由应用链管理员向中继链注册应用链，参数说明如下：

```shell
NAME:
   Pier appchain register - Register appchain to bitxhub

USAGE:
   Pier appchain register [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
   --name value         Specify appchain name
   --type value         Specify appchain type
   --trustroot value    Specify appchain trustroot path
   --broker value       Specify appchain broker contract address
   --broker-cid value   Specify fabric broker contract channel ID, only for fabric appchain
   --broker-ccid value  Specify fabric broker contract chaincode ID, only for fabric appchain
   --broker-v value     Specify appchain broker contract version, only for fabric appchain
   --desc value         Specify appchain description
   --master-rule value  Specify appchain master-rule
   --rule-url value     Specify appchain master-rule url
   --admin value        Specify appchain admin addr list, multiple addresses are separated by ",". The current user is included by default.
   --reason value       Specify governance reason
   --addr value         Specific bitxhub node address
```
**参数解释：**

* `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录。
* `--appchain-id`：必选参数，指定当前应用链id，该id用于填充IBTP的From或者To字段。
* `--name` ：必选参数，指定当前应用链名称。
*  `--type`：必选参数，指定当前应用链类型
*  `--trustroot` ：必选参数，应用链信任根，不同的共识算法的信任根的类型不同，对于PoW共识非确定性共识类的应用链，`trustRoot`为某一个高度的区块头，对于PBFT确定性类共识的区块链，`trustRoot`为验证人集合。
*  `--broker` ：可选参数，应用链上broker合约地址。
*  `--broker-cid` ：可选参数，在使用fabric类型的应用链时，指定broker合约的channel id。
*  `--broker-ccid` ：可选参数，在使用fabric类型的应用链时，指定broker合约的chaincode id。
* `--broker-v`：可选参数，在使用fabric类型的应用链时，指定broker合约的版本号。
*  `--desc`：必选参数，当前应用链注册的描述信息。
*  `--master-rule` ：必选参数，所绑定的验证规则地址，需要提前在中继链部署相关验证规则或使用内置验证规则地址。
*  `--rule-url` ：可选参数，验证规则url地址，如未设置可填写任意网址。
*  `--admin` ：可选参数，应用链管理员地址，一般为所绑定网关的key.json。
*  `--reason` ：可选参数，应用链注册的描述信息。
*  `--addr` ：可选参数，指定所连接的中继链节点地址

该命令向中继链发送一笔应用链注册的交易，中继链以交易的from（即当前pier公钥的地址）作为应用链的ID，生成一个应用链注册的提案。

中继链管理员需要对提案进行投票，命令如下：
```shell
bitxhub [--repo <repository>] client governance vote --id <proposal id> --info <voting information>  --reason <reason to vote>
```
**参数解释：**

* `--repo`：可选参数，指定bitxhub节点配置文件所在目录，如果不指定，默认使用$HOME/.bitxhub目录。
* `--id`：必选参数，指定提案id。
* `--info`：必选参数，指定投票信息，approve或者reject。
* `--reason`：必选参数，指定投票的原因。

### 示例说明
比如进行fabric应用链的注册,现在 --doc-addr 和 --doc-hash 、--rule-url没有真正用到，使用下面示例的即可，验证规则需要提前注册（也可使用示例中内置验证规则地址）。验证者信息需要提前拷贝到repo目录下。注意在注册应用链时，确保已经正常启动中继链：
```shell
# 具体样例

$ cd <bitxhub_project> && make install

# 启动bitxhub
$ make cluster

$ pier --repo $(pwd) init relay
# 可以使用对应插件项目下的验证者信息，注意更改项目路径
$ cp ~/goproject/meshplus/pier-client-fabric/config/fabric.validators ./fabric/

$ id=$(pier --repo $(pwd) id)
# bitxhub管理员向pier管理员转账。注意更改为bitxhub项目地址
$ bitxhub client tx send --key ~/goproject/meshplus/bitxhub/scripts/build/node1/key.json --to $id --amount 100000000000000000000000000

$ pier --repo $(pwd) appchain register --appchain-id "fabappchain" --name "fabric1" --type "Fabric V1.4.3" --trustroot "fabric/fabric.validators" --broker-cid "mychannel" --broker-ccid "broker" --broker-v 1 --desc "test fabric" --master-rule "0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB" --rule-url "https://bitxhub.cn/" --admin $id --reason "reason"

Register appchain successfully, wait for proposal 0x0df6746854682fc8ce5A7729BBB14c7652ae4516-0 to finish.
```
如上例所示，应用链管理员提交应用链注册请求，提案号为0x0df6746854682fc8ce5A7729BBB14c7652ae4516-0。中继链管理员需要对该提案进行审核并进行投票。

**注意：如使用make cluster等方式启动中继链，默认的投票策略为ZeroPermission，即无需管理员进行投票。**

```shell
$ PROPOSAL_ID=上面得到的ID，如 0x0df6746854682fc8ce5A7729BBB14c7652ae4516-0

# 查询中继链上提案状态
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node1 client governance proposal query --id $PROPOSAL_ID
========================================================================================
Id                                            ManagedObjectId  Type          EventType  Status   A/R  IE/AE/TE  Special/Super  CreateTime           Reason  EndReason               extra
--                                            ---------------  ----          ---------  ------   ---  --------  -------------  ----------           ------  ---------               -----
0x0df6746854682fc8ce5A7729BBB14c7652ae4516-0  fabappchain      appchain_mgr  register   approve  0/0  4/4/0     false/false    1643101796307170000  reason  zero permission reason  {"chain_info":{"id":"fabappchain","chain_name":"fabric1","chain_type":"Fabric V1.4.3","trust_root":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNLVENDQWMrZ0F3SUJBZ0lSQUlCTzMxYVphU1pvRVlTeTJBSnVoSmN3Q2dZSUtvWkl6ajBFQXdJd2N6RUwKTUFrR0ExVUVCaE1DVlZNeEV6QVJCZ05WQkFnVENrTmhiR2xtYjNKdWFXRXhGakFVQmdOVkJBY1REVk5oYmlCRwpjbUZ1WTJselkyOHhHVEFYQmdOVkJBb1RFRzl5WnpJdVpYaGhiWEJzWlM1amIyMHhIREFhQmdOVkJBTVRFMk5oCkxtOXlaekl1WlhoaGJYQnNaUzVqYjIwd0hoY05NakF3TWpBMU1EZ3lNakF3V2hjTk16QXdNakF5TURneU1qQXcKV2pCcU1Rc3dDUVlEVlFRR0V3SlZVekVUTUJFR0ExVUVDQk1LUTJGc2FXWnZjbTVwWVRFV01CUUdBMVVFQnhNTgpVMkZ1SUVaeVlXNWphWE5qYnpFTk1Bc0dBMVVFQ3hNRWNHVmxjakVmTUIwR0ExVUVBeE1XY0dWbGNqRXViM0puCk1pNWxlR0Z0Y0d4bExtTnZiVEJaTUJNR0J5cUdTTTQ5QWdFR0NDcUdTTTQ5QXdFSEEwSUFCRzNqc3pGUFRiR20KZEFZZzJCeG1ITVRES2ZRUmVOdzNwOXR0TUsxMzBxRjVsUW81ekxCRzhTYTN2aU9DTG52ampnNkEvUCt5S253dgppc0kvakVWRThUMmpUVEJMTUE0R0ExVWREd0VCL3dRRUF3SUhnREFNQmdOVkhSTUJBZjhFQWpBQU1Dc0dBMVVkCkl3UWtNQ0tBSU1WTCtkYUs3bk1HcjIvQVFJWFRTUEZrZGQzVWlQVkRrV3RraDV1am5hbEVNQW9HQ0NxR1NNNDkKQkFNQ0EwZ0FNRVVDSVFETVlPUWlZZU1pUVpUeGxSa2ovMy9qall2d3dkQ2NYNUFXdUZtcmFpSGt1Z0lnRmtYLwo2dWlUU0QwbHo4UCt3d2xMZjI0Y0lBQnEyYVp5aThxNGdqMFlmd0E9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K","broker":"eyJjaGFubmVsX2lkIjoibXljaGFubmVsIiwiY2hhaW5jb2RlX2lkIjoiYnJva2VyIiwiYnJva2VyX3ZlcnNpb24iOiIxIn0=","desc":"test fabric","version":0,"status":"available","fsm":null},"master_rule":{"address":"0x173b4f47fd11d3f0EC48027C1F166D4Ae6b54BaB","rule_url":"https://bitxhub.cn/","chain_id":"","master":true,"builtIn":false,"create_time":0,"status":"available","fsm":null},"admin_addrs":"0x0df6746854682fc8ce5A7729BBB14c7652ae4516"}
========================================================================================
* A/R：approve num / reject num
* IE/AE/TE：the total number of electorate at the time of the initial proposal / the number of available electorate currently /the minimum threshold for votes to take effect
* Special/Super：is special proposal / is super admin voted

```
可以看到该提案所需投票个数为0，即提案已经通过，应用链注册成功。

## 3. 应用链服务注册

### Pier appchain service

`Pier appchain service`命令用于对应用链服务进行注册。

```shell
NAME:
   Pier appchain service - Command about appchain service

USAGE:
   Pier appchain service command [command options] [arguments...]

COMMANDS:
   register  Register appchain service info to bitxhub
   update    Update appchain service info to bitxhub
   activate  Activate appchain service to bitxhub
   logout    Logout appchain service to bitxhub
   list      List appchain service from bitxhub belong to pier self
```

**子命令**

#### pier appchain service register

```
NAME:
   Pier appchain service register - Register appchain service info to bitxhub

USAGE:
   Pier appchain service register [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain ID
   --service-id value   Specify service ID（address of Service Contract）
   --name value         Specify service name
   --intro value        Specify service description
   --type value         Specify service type, one of "CallContract", "DepositCertificate", "DataMigration", current only support CallContract (default: "CallContract")
   --ordered            Specify if the service should be ordered
   --permit value       Specify contracts which are not allowed to invoke the service. If there are multiple contract addresses, separate them with ','
   --details value      Specify service details
   --reason value       Specify governance reason
```

**参数解释**

各参数说明参考<a href="../../design/ibtp#serviceAnchor">应用链服务介绍</a>。

### 示例说明

```
pier --repo $(pwd) appchain service register --appchain-id fabappchain --service-id "mychannel&transfer" --name "testServer1" --intro "test" --type CallContract --permit "" --details "test" --reason "reason"
Register appchain service for fabappchain:mychannel&transfer successfully, wait for proposal 0x0df6746854682fc8ce5A7729BBB14c7652ae4516-1 to finish.
```



## 2. 更新应用链
### pier appchain update
如果应用链发生了更改，比如验证人信息发生了变化，需要更新其在中继链上的应用链信息。参数说明如下：
```shell
NAME:
   Pier appchain update - update appchain in bitxhub

USAGE:
   Pier appchain update [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
   --name value         Specify appchain name
   --trustroot value    Specify appchain trustroot path
   --desc value         Specify appchain description
   --admin value        Specify appchain admin addr list, multiple addresses are separated by ",". The current user is included by default.
   --reason value       Specify governance reason
```

该命令所有参数含义与应用链注册命令的部分参数描述相同，不再赘述。

### 示例说明
比如进行fabric应用链的应用链名称，需要更新应用链，命令执行如下：
```shell
# 具体样例
$ pier --repo $(pwd) appchain update --appchain-id "fabappchain" --name "fabric2" --trustroot "fabric/fabric.validators" --desc "test update fabric appchain"  --admin $id --reason "reason"
the update request was submitted successfully, proposal id is 0x0df6746854682fc8ce5A7729BBB14c7652ae4516-2

# 查询更新前的应用链name
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance chain info --name fabric1
# 控制台输出报错，说明appchain的name已更新
invoke BVM contract failed when get appchain by name fabric1: call error: 1020014:the appchain(fabric1) does not exist: not found chain fabric1

# 通过更新后的name在bitxhub进行应用链查询
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance chain info --name fabric2
# 控制台输出更新后的应用链注册信息
Id           Name     Type           Broker                                                                   Status     Desc                         Version
--           ----     ----           ------                                                                   ------     ----                         -------
fabappchain  fabric2  Fabric V1.4.3  {"channel_id":"mychannel","chaincode_id":"broker","broker_version":"1"}  available  test update fabric appchain  1
```

以上示例说明应用链更新成功。




## 3. 激活应用链
### pier appchain activate
如果冻结的应用链恢复正常，应用链管理员可以申请激活应用链。命令如下：
```shell
$ pier --repo <repository> appchain activate --admin-key <admin_key_json> --id <appchian_did>
```

### 示例说明
比如对之前已经冻结的应用链进行激活，命令执行如下：
```shell
# 中继链管理员冻结应用链
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance chain freeze --id fabappchain --reason "test freeze appchain"
proposal id is 0x79a1215469FaB6f9c63c1816b45183AD3624bE34-0

# 查询应用链状态，发现已经变为frozen
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance chain info --name fabric2
Id           Name     Type           Broker                                                                   Status  Desc                         Version
--           ----     ----           ------                                                                   ------  ----                         -------
fabappchain  fabric2  Fabric V1.4.3  {"channel_id":"mychannel","chaincode_id":"broker","broker_version":"1"}  frozen  test update fabric appchain  1

# 激活应用链
$ pier --repo $(pwd) appchain activate --appchain-id "fabappchain" --reason "activate appchain"
the activate request was submitted successfully, proposal id is 0x0df6746854682fc8ce5A7729BBB14c7652ae4516-3

# 查询应用链状态，发现从frozen变为available
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance chain info --name fabric2
Id           Name     Type           Broker                                                                   Status     Desc                         Version
--           ----     ----           ------                                                                   ------     ----                         -------
fabappchain  fabric2  Fabric V1.4.3  {"channel_id":"mychannel","chaincode_id":"broker","broker_version":"1"}  available  test update fabric appchain  1
```

以上示例说明应用链在冻结后激活成功。

## 5. 注销应用链
### pier appchain logout
如果应用链退出跨链系统，不再进行跨链，应用链管理员可以向中继链提交注销应用链的提案。命令如下：
```shell
NAME:
   Pier appchain logout - logout appchain in bitxhub

USAGE:
   Pier appchain logout [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
   --reason value       Specify governance reason
```

### 示例说明
比如对之前激活的应用链进行注销，命令执行如下：
```shell
# 注销应用链
$ pier --repo $(pwd) appchain logout --appchain-id "fabappchain" --reason "activate appchain"
the logout request was submitted successfully, proposal id is 0x0df6746854682fc8ce5A7729BBB14c7652ae4516-4

# 在中继链查询应用链状态，发现已经变为forbidden
$ bitxhub --repo=/Users/liruoxin/goproject/meshplus/bitxhub/scripts/build/node2 client governance chain info --name fabric2
Id           Name     Type           Broker                                                                   Status     Desc                         Version
--           ----     ----           ------                                                                   ------     ----                         -------
fabappchain  fabric2  Fabric V1.4.3  {"channel_id":"mychannel","chaincode_id":"broker","broker_version":"1"}  forbidden  test update fabric appchain  1
```

以上示例说明应用链注销成功。

### Pier appchain get 

获取应用链注册后的相关信息。命令如下：

```shell
pier --repo <repository> appchain get --admin-key <admin_key_json> --id <appchian_did>
```

### 示例说明

获取当前应用链相关信息，命令执行如下：

```shell
# 具体样例
$ pier --repo $(pwd) appchain get --appchain-id "fabappchain"

{"id":"fabappchain","chain_name":"fabric2","chain_type":"Fabric V1.4.3","trust_root":"TFMwdExTMUNSVWRKVGlCRFJWSlVTVVpKUTBGVVJTMHRMUzB0Q2sxSlNVTkxWRU5EUVdNclowRjNTVUpCWjBsU1FVbENUek14WVZwaFUxcHZSVmxUZVRKQlNuVm9TbU4zUTJkWlNVdHZXa2w2YWpCRlFYZEpkMk42UlV3S1RVRnJSMEV4VlVWQ2FFMURWbFpOZUVWNlFWSkNaMDVXUWtGblZFTnJUbWhpUjJ4dFlqTktkV0ZYUlhoR2FrRlZRbWRPVmtKQlkxUkVWazVvWW1sQ1J3cGpiVVoxV1RKc2Vsa3lPSGhIVkVGWVFtZE9Wa0pCYjFSRlJ6bDVXbnBKZFZwWWFHaGlXRUp6V2xNMWFtSXlNSGhJUkVGaFFtZE9Wa0pCVFZSRk1rNW9Da3h0T1hsYWVrbDFXbGhvYUdKWVFuTmFVelZxWWpJd2QwaG9ZMDVOYWtGM1RXcEJNVTFFWjNsTmFrRjNWMmhqVGsxNlFYZE5ha0Y1VFVSbmVVMXFRWGNLVjJwQ2NVMVJjM2REVVZsRVZsRlJSMFYzU2xaVmVrVlVUVUpGUjBFeFZVVkRRazFMVVRKR2MyRlhXblpqYlRWd1dWUkZWMDFDVVVkQk1WVkZRbmhOVGdwVk1rWjFTVVZhZVZsWE5XcGhXRTVxWW5wRlRrMUJjMGRCTVZWRlEzaE5SV05IVm14amFrVm1UVUl3UjBFeFZVVkJlRTFYWTBkV2JHTnFSWFZpTTBwdUNrMXBOV3hsUjBaMFkwZDRiRXh0VG5aaVZFSmFUVUpOUjBKNWNVZFRUVFE1UVdkRlIwTkRjVWRUVFRRNVFYZEZTRUV3U1VGQ1J6TnFjM3BHVUZSaVIyMEtaRUZaWnpKQ2VHMUlUVlJFUzJaUlVtVk9kek53T1hSMFRVc3hNekJ4UmpWc1VXODFla3hDUnpoVFlUTjJhVTlEVEc1MmFtcG5Oa0V2VUN0NVMyNTNkZ3BwYzBrdmFrVldSVGhVTW1wVVZFSk1UVUUwUjBFeFZXUkVkMFZDTDNkUlJVRjNTVWhuUkVGTlFtZE9Wa2hTVFVKQlpqaEZRV3BCUVUxRGMwZEJNVlZrQ2tsM1VXdE5RMHRCU1UxV1RDdGtZVXMzYmsxSGNqSXZRVkZKV0ZSVFVFWnJaR1F6VldsUVZrUnJWM1JyYURWMWFtNWhiRVZOUVc5SFEwTnhSMU5OTkRrS1FrRk5RMEV3WjBGTlJWVkRTVkZFVFZsUFVXbFpaVTFwVVZwVWVHeFNhMm92TXk5cWFsbDJkM2RrUTJOWU5VRlhkVVp0Y21GcFNHdDFaMGxuUm10WUx3bzJkV2xVVTBRd2JIbzRVQ3QzZDJ4TVpqSTBZMGxCUW5FeVlWcDVhVGh4TkdkcU1GbG1kMEU5Q2kwdExTMHRSVTVFSUVORlVsUkpSa2xEUVZSRkxTMHRMUzBL","broker":"eyJjaGFubmVsX2lkIjoibXljaGFubmVsIiwiY2hhaW5jb2RlX2lkIjoiYnJva2VyIiwiYnJva2VyX3ZlcnNpb24iOiIxIn0=","desc":"test update fabric appchain","version":1,"status":"forbidden","fsm":{}}
```

