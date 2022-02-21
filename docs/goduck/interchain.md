# 基本跨链操作

以一个基本的以太坊和fabric之间的跨链为例，其中bitxhub和pier均以二进制方式启动。

在进行跨链操作前，需要先安装好goduck比完成初始化
```shell
$ git checkout release-1.1
$ git pull
$ make install && goduck init
```

## 1 启动应用链
### 1.1 启动以太坊
我们提供了已经部署好跨链合约的以太坊镜像，使用如下goduck命令可以一键启动：
```shell
$ goduck ether start --bxh-version v1.11.2
exec:  /bin/bash ethereum.sh docker 1.3.0
start a new ethereum-node container
0516eaa0bae482018f60be3b8f92efa7bc311ba48b5ef764bbb645c8344ba1f8
start ethereum private chain with data directory in $goduck_repo/ethereum/datadir.
```

### 1.2 启动fabric
#### 1.2.1 启动fabric应用链
```shell
$ goduck fabric start
# 启动成功后最后的日志打印如下：
...
========= All GOOD, BYFN execution completed ===========


 _____   _   _   ____
| ____| | \ | | |  _ \
|  _|   |  \| | | | | |
| |___  | |\  | | |_| |
|_____| |_| \_| |____/

```
⚠️ 启动过程中可能需要科学上网。如果出现报错或fabric-samples下载失败，建议删除fabric-samples文件，重新执行fabric启动命令。

#### 1.2.2 部署跨链合约
我们会提供已经准备好的fabric跨链合约contract文件，执行以下命令可以直接下载合约并部署：
```shell
$ goduck fabric contract chaincode --bxh-version v1.11.2

# 部署成功后会依次打印出几个步骤的日志，日志中有部分报错不影响，只要没有红色报错，且能大致打印出以下日志即可：
install default interchain chaincode
===> Install chaincode
===> 1. Deploying broker, transfer and data_swapper chaincode
Installing chaincode broker on org[org2] peers:
-- grpcs://localhost:9051
-- grpcs://localhost:10051
...successfuly installed chaincode broker.v0 on peer localhost:9051.
...successfuly installed chaincode broker.v0 on peer localhost:10051.
...successfuly instantiated chaincode broker on channel mychannel.
Installing chaincode transfer on org[org2] peers:
-- grpcs://localhost:9051
-- grpcs://localhost:10051
...successfuly installed chaincode transfer.v0 on peer localhost:9051.
...successfuly installed chaincode transfer.v0 on peer localhost:10051.
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
===> 3. Set (key: path, value: /Users/fangbaozhu/.goduck) in data_swapper chaincode
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload:
***** |  Response[1]:
***** |  |  Payload:
****************************************************************************************************
===> 4. Register transfer and data_swapper chaincode to broker chaincode
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

## 2 启动bitxhub
```shell
$ goduck bitxhub start --version v1.11.2
# 启动成功后最后的日志打印如下：

...
Start bitxhub node1
Start bitxhub node2
Start bitxhub node3
Start bitxhub node4
You can use the "goduck status list" command to check the status of the startup BitXHub node.
```

## 3 启动pier
### 3.1 启动连接以太坊的pier
#### 3.1.1 启动pier
```shell
$ goduck pier start --version v1.11.2
======> Generate configuration files for pier_fabric
【1】generate configuration files
【2】copy pier plugin and appchain config
======> Rewrite config for pier_fabric
【1】rewrite pier.toml
【2】rewrite appchain config
【3】rewrite api
======> Start pier of fabric in binary...
You can use the "goduck status list" command to check the status of the startup pier.
```

#### 3.1.2 注册应用链

第一步：确保账户有钱  
注册应用链之前，首先需要保证应用链管理员有注册应用链的权限，即账户有钱，可以由中继链管理员给应用链管理员转账。
这一部分不属于goduck功能，可以使用bitxhub命令行实现，示例如下：
```shell
# 查看应用链管理员账户，应用链管理员的私钥在启动pier时已经初始化在pier的配置目录下
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub key show --path $goduck_repo/pier/.pier_ethereum/key.json
private key: b2d1ad8e78233cee31dcbc0fa7e4f3413897ae4dd91df527b6bc411357cd868e
public key: 04e9f3df1a701a577d52e145cbb17e4ba64bd87bf8a941592afccc3a7fb3b947bc60e6662012ae53c7b4260215441e2edc812386ebaa4d9ef470c265c9a3deb60d
address: 0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89
# address后的信息0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89即为应用链管理员地址

# 中继链管理员向应用链管理员转账
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub client tx send --key $goduck_repo/bitxhub/.bitxhub/node1/key.json --to 0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89 --amount 100000000000000000
{"tx_hash":"0x84eA84904FbB963ed9aec4C2d056ED9B06Be96A384376555EBa417f6944DF77b"}

# 查询账户余额
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub client account 0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89
{
  "type": "normal",
  "balance": 100000000000000000,
  "contract_count": 1,
  "code_hash": "0xc5d2460186f7233C927e7db2dCc703c0E500B653cA82273b7bFaD8045d85A470"
}
```

第二步：提交注册提案  
确保账户有钱后，即可进行应用链注册操作。  
注册时应用链的method即应用链did中的method，在1.11.2版本中应为"appchain"拼接应用链管理员地址后的字符串，如本例中应为"appchain0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89"。_（只有1.11.2版本是如此要求的）_
```shell
$ goduck pier register --version v1.11.2 --method appchain0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89
======> pier_root: $goduck_repo/pier/.pier_ethereum
======> Register pier(ethereum) to bitxhub
Register appchain method info for did:bitxhub:appchain0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89:. successfully, wait for proposal 0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89-0 to finish.
Waiting for the administrators of BitXHub to vote for approval.
# 应用链did：did:bitxhub:appchain0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89:.
# 提案id：0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89-0
```

第三步：提案投票通过  
注册提案提交后需要进行投票，投票部分不属于goduck的功能，可以使用bitxhub命令行实现，示例如下：
```shell
# 管理员投票
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node1 client governance vote --id 0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89-0 --info approve --reason reason
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node2 client governance vote --id 0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89-0 --info approve --reason reason
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node3 client governance vote --id 0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89-0 --info approve --reason reason
# 提案默认使用简单多数制投票制，即四位管理员时需要三票可通过，三次投票后会分别打印出投票成功的提示，提案便通过，即应用链注册成功

# 查看应用链是否注册成功
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node1 client governance chain status --id did:bitxhub:appchain0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89:.
# 打印应用链状态
appchain did:bitxhub:appchain0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89:. is available
```

#### 3.1.3 检查pier是否准备完成
成功启动pier并成功注册应用链后pier启动即准备完成。  
我们可以通过查看pier日志的方式确定连接以太坊链的pier准备完成：
```shell
# 注意需要指定pier的启动目录，默认是$goduck_repo/pier/.pier_ethereum
$ goduck log pier --pier-repo $goduck_repo/pier/.pier_ethereum
...
# 以下部分是应用链未注册成功时的日志
time="2022-01-30T15:28:36.603" level=error msg="Get interchain meta receipt: call error: this appchain does not exist ... retry later" module=app
time="2022-01-30T15:28:41.610" level=error msg="Get interchain meta receipt: call error: this appchain does not exist ... retry later" module=app
# 以下部分是应用链注册提案投票成功后的日志
time="2022-01-30T15:28:47.787" level=info msg="Pier information" id="did:bitxhub:appchain0xF0e6B2D8F268aC2401cb2C065eFbBbb39230fb86:." interchain_counter="map[]" module=app receipt_counter="map[]" source_receipt_counter="map[]"
time="2022-01-30T15:28:47.797" level=info msg="Monitor started" module=monitor
time="2022-01-30T15:28:47.797" level=info msg="Executor started" module=executor
time="2022-01-30T15:28:47.798" level=info msg="BitXHub lite recover" begin=1 end=6 module=bxh_lite
time="2022-01-30T15:28:47.802" level=info msg="Persist block header" height=1 module=bxh_lite
time="2022-01-30T15:28:47.802" level=info msg="Persist block header" height=2 module=bxh_lite
time="2022-01-30T15:28:47.802" level=info msg="Persist block header" height=3 module=bxh_lite
time="2022-01-30T15:28:47.802" level=info msg="Persist block header" height=4 module=bxh_lite
time="2022-01-30T15:28:47.802" level=info msg="Persist block header" height=5 module=bxh_lite
time="2022-01-30T15:28:47.802" level=info msg="Persist block header" height=6 module=bxh_lite
time="2022-01-30T15:28:47.802" level=info msg="BitXHub lite started" bitxhub_height=6 current_height=6 module=bxh_lite
time="2022-01-30T15:28:47.803" level=info msg="Syncer recover" begin=1 end=6 module=syncer
time="2022-01-30T15:28:47.805" level=info msg="Handle interchain tx wrapper" count=0 height=1 index=0 module=syncer
time="2022-01-30T15:28:47.805" level=info msg="Handle interchain tx wrapper" count=0 height=2 index=0 module=syncer
time="2022-01-30T15:28:47.805" level=info msg="Handle interchain tx wrapper" count=0 height=3 index=0 module=syncer
time="2022-01-30T15:28:47.805" level=info msg="Handle interchain tx wrapper" count=0 height=4 index=0 module=syncer
time="2022-01-30T15:28:47.806" level=info msg="Handle interchain tx wrapper" count=0 height=5 index=0 module=syncer
time="2022-01-30T15:28:47.806" level=info msg="Handle interchain tx wrapper" count=0 height=6 index=0 module=syncer
time="2022-01-30T15:28:47.806" level=info msg="Syncer started" bitxhub_height=6 current_height=6 module=syncer
time="2022-01-30T15:28:47.816" level=info msg="Exchanger started" module=exchanger
```


### 3.2 启动连接fabric的pier
#### 3.2.1 启动pier
由于这里需要启动第二个pier，有些配置需要修改，可以前先复制一份可修改配置文件pier_modify_config1.toml，然后对pier_modify_config1.toml文件进行修改
```shell
$ cp $goduck_repo/pier_config/v1.11.2/pier_modify_config.toml $goduck_repo/pier_config/v1.11.2/pier_modify_config1.toml
$ vim $goduck_repo/pier_config/v1.11.2/pier_modify_config1.toml
# 主要需要修改端口信息，内容如下：
  httpPort = 44564
  pprofPort = 44560
  apiPort = 8081
```
然后启动指定fabric应用链类型及修改后的可修改配置文件启动pier：
```shell
$ goduck pier start --version v1.11.2 --appchain fabric --configPath $goduck_repo/pier_config/v1.11.2/pier_modify_config1.toml
======> Generate configuration files for pier_fabric
【1】generate configuration files
【2】copy pier plugin and appchain config
======> Rewrite config for pier_fabric
【1】rewrite pier.toml
【2】rewrite appchain config
【3】rewrite api
======> Start pier of fabric in binary...
You can use the "goduck status list" command to check the status of the startup pier.
```

#### 3.2.2 注册应用链
第一步：确保账户有钱  
注册应用链之前，首先需要保证应用链管理员有注册应用链的权限，即账户有钱，可以由中继链管理员给应用链管理员转账。
这一部分不属于goduck功能，可以使用bitxhub命令行实现，示例如下：
```shell
# 查看应用链管理员账户，应用链管理员的私钥在启动pier时已经初始化在pier的配置目录下
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub key show --path $goduck_repo/pier/.pier_fabric/key.json
private key: fa54d013ee9ea78d6c877493633ed8b8711829a2a1c144eef353481f681ee4e0
public key: 04fc2c2c30ac4e88e0a9321a83f0079e252975a45b3d2ba1bbc129ef2bf66055c2d7f33b17a9f77a3ed7577bc38b12ff2ba1118878a4a2abe2027a5343040f9c0e
address: 0xd70a91C5d747221115761Eb5eaA9DcDd815152Db
# address后的信息0xd70a91C5d747221115761Eb5eaA9DcDd815152Db即为应用链管理员地址

# 中继链管理员向应用链管理员转账
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub client tx send --key $goduck_repo/bitxhub/.bitxhub/node1/key.json --to 0xd70a91C5d747221115761Eb5eaA9DcDd815152Db --amount 100000000000000000
{"tx_hash":"0x51691D2FEc637c7d4D097323E21E5Bb54C5aABd6597e7AB980ebA886305e3d22"}

# 查询账户余额
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub client account 0xd70a91C5d747221115761Eb5eaA9DcDd815152Db
{
  "type": "normal",
  "balance": 100000000000000000,
  "contract_count": 0,
  "code_hash": "0xc5d2460186f7233C927e7db2dCc703c0E500B653cA82273b7bFaD8045d85A470"
}
```

第二步：提交注册提案  
确保账户有钱后，即可进行应用链注册操作。  
注册时应用链的method即应用链did中的method，在1.11.2版本中应为"appchain"拼接应用链管理员地址后的字符串，如本例中应为"appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db"。_（只有1.11.2版本是如此要求的）_
由于默认注册的是以太坊应用链，fabric应用链需要用--appchain参数特别指明应用链类型
```shell
$ goduck pier register --version v1.11.2 --method appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db --appchain fabric
======> pier_root: $repo/pier/.pier_fabric
======> Register pier(fabric) to bitxhub
Register appchain method info for did:bitxhub:appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db:. successfully, wait for proposal 0xd70a91C5d747221115761Eb5eaA9DcDd815152Db-0 to finish.
Waiting for the administrators of BitXHub to vote for approval.
# 应用链did：did:bitxhub:appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db:.
# 提案id：0xd70a91C5d747221115761Eb5eaA9DcDd815152Db-0
```

第三步：提案投票通过  
注册提案提交后需要进行投票，投票部分不属于goduck的功能，可以使用bitxhub命令行实现，示例如下：
```shell
# 管理员投票
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node1 client governance vote --id 0xd70a91C5d747221115761Eb5eaA9DcDd815152Db-0 --info approve --reason reason
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node2 client governance vote --id 0xd70a91C5d747221115761Eb5eaA9DcDd815152Db-0 --info approve --reason reason
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node3 client governance vote --id 0xd70a91C5d747221115761Eb5eaA9DcDd815152Db-0 --info approve --reason reason
# 提案默认使用简单多数制投票制，即四位管理员时需要三票可通过，三次投票后会分别打印出投票成功的提示，提案便通过，即应用链注册成功

# 查看应用链是否注册成功
$ $goduck_repo/bin/bitxhub_darwin_v1.11.2/bitxhub --repo $goduck_repo/bitxhub/.bitxhub/node1 client governance chain status --id did:bitxhub:appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db:.
# 打印应用链状态
appchain did:bitxhub:appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db:. is available
```

#### 3.2.3 检查pier是否准备完成
成功启动pier并成功注册应用链后pier启动即准备完成。  
我们可以通过查看pier日志的方式确定连接以太坊链的pier准备完成：
```shell
# 注意需要指定pier的启动目录，默认是$repo/pier/.pier_fabric
$ goduck log pier --pier-repo $repo/pier/.pier_fabric
...
# 以下部分是应用链未注册成功时的日志
time="2022-01-30T21:48:27.552" level=error msg="Get interchain meta receipt: call error: this appchain does not exist ... retry later" module=app
time="2022-01-30T21:48:32.559" level=error msg="Get interchain meta receipt: call error: this appchain does not exist ... retry later" module=app
# 以下部分是应用链注册提案投票成功后的日志
time="2022-01-30T21:48:42.147" level=info msg="Pier information" id="did:bitxhub:appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db:." interchain_counter="map[]" module=app receipt_counter="map[]" source_receipt_counter="map[]"
time="2022-01-30T21:48:42.150" level=info msg="Monitor started" module=monitor
time="2022-01-30T21:48:42.151" level=info msg="Executor started" module=executor
time="2022-01-30T21:48:42.151" level=info msg="BitXHub lite recover" begin=1 end=12 module=bxh_lite
time="2022-01-30T21:48:42.153" level=info msg="Persist block header" height=1 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=2 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=3 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=4 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=5 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=6 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=7 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=8 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=9 module=bxh_lite
time="2022-01-30T21:48:42.154" level=info msg="Persist block header" height=10 module=bxh_lite
time="2022-01-30T21:48:42.155" level=info msg="Persist block header" height=11 module=bxh_lite
time="2022-01-30T21:48:42.155" level=info msg="Persist block header" height=12 module=bxh_lite
time="2022-01-30T21:48:42.155" level=info msg="BitXHub lite started" bitxhub_height=12 current_height=12 module=bxh_lite
time="2022-01-30T21:48:42.156" level=info msg="Syncer recover" begin=1 end=12 module=syncer
time="2022-01-30T21:48:42.157" level=info msg="Handle interchain tx wrapper" count=0 height=1 index=0 module=syncer
time="2022-01-30T21:48:42.157" level=info msg="Handle interchain tx wrapper" count=0 height=2 index=0 module=syncer
time="2022-01-30T21:48:42.157" level=info msg="Handle interchain tx wrapper" count=0 height=3 index=0 module=syncer
time="2022-01-30T21:48:42.157" level=info msg="Handle interchain tx wrapper" count=0 height=4 index=0 module=syncer
time="2022-01-30T21:48:42.157" level=info msg="Handle interchain tx wrapper" count=0 height=5 index=0 module=syncer
time="2022-01-30T21:48:42.157" level=info msg="Handle interchain tx wrapper" count=0 height=6 index=0 module=syncer
time="2022-01-30T21:48:42.157" level=info msg="Handle interchain tx wrapper" count=0 height=7 index=0 module=syncer
time="2022-01-30T21:48:42.158" level=info msg="Handle interchain tx wrapper" count=0 height=8 index=0 module=syncer
time="2022-01-30T21:48:42.158" level=info msg="Handle interchain tx wrapper" count=0 height=9 index=0 module=syncer
time="2022-01-30T21:48:42.158" level=info msg="Handle interchain tx wrapper" count=0 height=10 index=0 module=syncer
time="2022-01-30T21:48:42.158" level=info msg="Handle interchain tx wrapper" count=0 height=11 index=0 module=syncer
time="2022-01-30T21:48:42.158" level=info msg="Handle interchain tx wrapper" count=0 height=12 index=0 module=syncer
time="2022-01-30T21:48:42.158" level=info msg="Syncer started" bitxhub_height=12 current_height=12 module=syncer
time="2022-01-30T21:48:48.370" level=info msg="Exchanger started" module=exchanger
```

## 4 跨链交易

```shell
# 查询以太坊上Alice账户余额
$ goduck ether contract invoke --abi-path $goduck_repo/pier/ethereum/1.3.0/transfer.abi \
      0x668a209Dc6562707469374B8235e37b8eC25db08 getBalance Alice

======= invoke function getBalance =======
call result: 10000

# 查询fabric上Alice账户余额
$ goduck fabric contract invoke transfer getBalance Alice
[fabric] invoke function "getBalance", receipt is 10000

# 以太坊向fabric转账
goduck ether contract invoke --abi-path $goduck_repo/pier/ethereum/1.3.0/transfer.abi \
      0x668a209Dc6562707469374B8235e37b8eC25db08 transfer did:bitxhub:appchain0xd70a91C5d747221115761Eb5eaA9DcDd815152Db:"mychannel&transfer",Alice,Alice,1

======= invoke function transfer =======
invoke contract success, tx hash is: 0xe42acbba632a254064eaf603993c7931686ff58649f18f80d22a7a96eb9f72a0

# 查询以太坊上Alice账户余额
$ goduck ether contract invoke --abi-path $goduck_repo/pier/ethereum/1.3.0/transfer.abi \
      0x668a209Dc6562707469374B8235e37b8eC25db08 getBalance Alice

======= invoke function getBalance =======
call result: 9999

# 查询fabric上Alice账户余额
$ goduck fabric contract invoke transfer getBalance Alice
[fabric] invoke function "getBalance", receipt is 10001

# fabric向以太坊转账
$ goduck fabric contract invoke transfer transfer did:bitxhub:appchain0xFa5C3047E8f4d095749F7D87411E18Ac20dD2a89:0x668a209Dc6562707469374B8235e37b8eC25db08,Alice,Alice,1
[fabric] invoke function "transfer", receipt is

# 查询以太坊上Alice账户余额
$ goduck ether contract invoke --abi-path $goduck_repo/pier/ethereum/1.3.0/transfer.abi \
      0x668a209Dc6562707469374B8235e37b8eC25db08 getBalance Alice

======= invoke function getBalance =======
call result: 10000

# 查询fabric上Alice账户余额
$ goduck fabric contract invoke transfer getBalance Alice
[fabric] invoke function "getBalance", receipt is 10000
```

