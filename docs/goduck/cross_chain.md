# 基本跨链操作

以一个基本的以太坊和fabric之间的跨链为例，其中bitxhub和pier均以二进制方式启动。

在进行跨链操作前，需要先安装好goduck并完成初始化
```shell
$ git checkout release-1.23.0
$ git pull
$ make install && goduck init
```

## 1 启动应用链
### 1.1 启动以太坊

#### 1.1.1启动以太坊镜像
我们提供了以太坊镜像，使用如下goduck命令可以一键启动：
```shell
$ goduck ether start 
exec:  /bin/bash ethereum.sh docker 8545 8546 30303 1.2.0
start a new ethereum-node container
e6688e08332e3ccb808b5e027b14c611ca914e34540ef120fc576586010a104b
start ethereum private chain with data directory in $goduck_repo/ethereum/datadir.
```

####  1.1.2 部署以太坊智能合约

在以太坊镜像中部署broker和transfer智能合约，示例如下
```shell
#部署broker合约
goduck ether contract deploy --code-path $HOME/goduck/scripts/example/broker.sol "1356^ethappchain1^["0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","0x79a1215469FaB6f9c63c1816b45183AD3624bE34","0x97c8B516D19edBf575D72a172Af7F418BE498C37","0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"]^1^["0x20F7Fac801C5Fc3f7E20cFbADaA1CDb33d818Fa3"]^1"

======= #HOME/goduck/scripts/example/broker.sol:Broker =======
Deployed contract address is 0x857133c5C69e6Ce66F7AD46F200B9B3573e77582
Contract JSON ABI
[{"inputs":[{"internalType":"string","name":"_bitxhubID","type":"string"},{"internalType":"string","name":"_appchainID","type":"string"},{"internalType":"address[]","name":"_validators","type":"address[]"},{"internalType":"uint64","name":"_valThreshold","type":"uint64"},{"internalType":"address[]","name":"_admins","type":"address[]"},{"internalType":"uint64","name":"_adminThreshold","type":"uint64"}],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"uint64","name":"index","type":"uint64"},{"indexed":false,"internalType":"string","name":"dstFullID","type":"string"},{"indexed":false,"internalType":"string","name":"srcFullID","type":"string"},{"indexed":false,"internalType":"string","name":"func","type":"string"},{"indexed":false,"internalType":"bytes[]","name":"args","type":"bytes[]"},{"indexed":false,"internalType":"bytes32","name":"hash","type":"bytes32"}],"name":"throwInterchainEvent","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"uint64","name":"index","type":"uint64"},{"indexed":false,"internalType":"string","name":"dstFullID","type":"string"},{"indexed":false,"internalType":"string","name":"srcFullID","type":"string"},{"indexed":false,"internalType":"uint64","name":"typ","type":"uint64"},{"indexed":false,"internalType":"bool","name":"status","type":"bool"},{"indexed":false,"internalType":"bytes[]","name":"result","type":"bytes[]"},{"indexed":false,"internalType":"bytes32","name":"hash","type":"bytes32"}],"name":"throwReceiptEvent","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"bool","name":"","type":"bool"}],"name":"throwReceiptStatus","type":"event"},{"inputs":[{"internalType":"address","name":"addr","type":"address"},{"internalType":"int64","name":"status","type":"int64"}],"name":"audit","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"destFullServiceID","type":"string"},{"internalType":"string","name":"funcCall","type":"string"},{"internalType":"bytes[]","name":"args","type":"bytes[]"},{"internalType":"string","name":"funcCb","type":"string"},{"internalType":"bytes[]","name":"argsCb","type":"bytes[]"},{"internalType":"string","name":"funcRb","type":"string"},{"internalType":"bytes[]","name":"argsRb","type":"bytes[]"},{"internalType":"bool","name":"isEncrypt","type":"bool"}],"name":"emitInterchainEvent","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"chainID","type":"string"}],"name":"getAppchainInfo","outputs":[{"internalType":"string","name":"","type":"string"},{"internalType":"bytes","name":"","type":"bytes"},{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getCallbackMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getChainID","outputs":[{"internalType":"string","name":"","type":"string"},{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"id","type":"string"}],"name":"getDirectTransactionMeta","outputs":[{"internalType":"uint256","name":"","type":"uint256"},{"internalType":"uint64","name":"","type":"uint64"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getDstRollbackMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getInnerMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getLocalServiceList","outputs":[{"internalType":"string[]","name":"","type":"string[]"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"addr","type":"address"}],"name":"getLocalWhiteList","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"outServicePair","type":"string"},{"internalType":"uint64","name":"idx","type":"uint64"}],"name":"getOutMessage","outputs":[{"internalType":"string","name":"","type":"string"},{"internalType":"bytes[]","name":"","type":"bytes[]"},{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getOuterMeta","outputs":[{"internalType":"string[]","name":"","type":"string[]"},{"internalType":"uint64[]","name":"","type":"uint64[]"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"remoteAddr","type":"string"}],"name":"getRSWhiteList","outputs":[{"internalType":"address[]","name":"","type":"address[]"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"string","name":"inServicePair","type":"string"},{"internalType":"uint64","name":"idx","type":"uint64"}],"name":"getReceiptMessage","outputs":[{"internalType":"bytes[]","name":"","type":"bytes[]"},{"internalType":"uint64","name":"","type":"uint64"},{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getRemoteServiceList","outputs":[{"internalType":"string[]","name":"","type":"string[]"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"initialize","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"srcFullID","type":"string"},{"internalType":"string","name":"destAddr","type":"string"},{"internalType":"uint64","name":"index","type":"uint64"},{"internalType":"uint64","name":"typ","type":"uint64"},{"internalType":"string","name":"callFunc","type":"string"},{"internalType":"bytes[]","name":"args","type":"bytes[]"},{"internalType":"uint64","name":"txStatus","type":"uint64"},{"internalType":"bytes[]","name":"signatures","type":"bytes[]"},{"internalType":"bool","name":"isEncrypt","type":"bool"}],"name":"invokeInterchain","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[{"internalType":"string","name":"srcAddr","type":"string"},{"internalType":"string","name":"dstFullID","type":"string"},{"internalType":"uint64","name":"index","type":"uint64"},{"internalType":"uint64","name":"typ","type":"uint64"},{"internalType":"bytes[]","name":"result","type":"bytes[]"},{"internalType":"uint64","name":"txStatus","type":"uint64"},{"internalType":"bytes[]","name":"signatures","type":"bytes[]"}],"name":"invokeReceipt","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[],"name":"register","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"chainID","type":"string"},{"internalType":"string","name":"broker","type":"string"},{"internalType":"address","name":"ruleAddr","type":"address"},{"internalType":"bytes","name":"trustRoot","type":"bytes"}],"name":"registerAppchain","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"registerDirectTransaction","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"chainID","type":"string"},{"internalType":"string","name":"serviceID","type":"string"},{"internalType":"address[]","name":"whiteList","type":"address[]"}],"name":"registerRemoteService","outputs":[],"stateMutability":"nonpayable","type":"function"}]
#记录broker合约地址，后续部署transfer合约以及调用合约时需要使用

#部署transfer合约
goduck ether contract deploy --code-path $HOME/goduck/scripts/example/transfer.sol 0x857133c5C69e6Ce66F7AD46F200B9B3573e77582

======= #HOME/goduck/scripts/example/transfer.sol:Transfer =======
Deployed contract address is 0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11
Contract JSON ABI
[{"inputs":[{"internalType":"address","name":"_brokerAddr","type":"address"}],"stateMutability":"nonpayable","type":"constructor"},{"inputs":[{"internalType":"bytes","name":"b","type":"bytes"}],"name":"bytesToUint64","outputs":[{"internalType":"uint64","name":"","type":"uint64"}],"stateMutability":"pure","type":"function"},{"inputs":[{"internalType":"string","name":"id","type":"string"}],"name":"getBalance","outputs":[{"internalType":"uint64","name":"","type":"uint64"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"bytes[]","name":"args","type":"bytes[]"},{"internalType":"bool","name":"isRollback","type":"bool"}],"name":"interchainCharge","outputs":[{"internalType":"bytes[]","name":"","type":"bytes[]"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"bytes[]","name":"args","type":"bytes[]"}],"name":"interchainRollback","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"register","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"id","type":"string"},{"internalType":"uint64","name":"amount","type":"uint64"}],"name":"setBalance","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"string","name":"destChainServiceID","type":"string"},{"internalType":"string","name":"sender","type":"string"},{"internalType":"string","name":"receiver","type":"string"},{"internalType":"uint64","name":"amount","type":"uint64"}],"name":"transfer","outputs":[],"stateMutability":"nonpayable","type":"function"}]
#记录transfer合约地址，后续调用合约函数时需要使用
```

####  1.1.3 合约审计

目前transfer部署完成后需要在broker合约中对其进行审核，示例如下
```shell
#审核transfer合约
goduck ether contract invoke --key-path $HOME/goduck/scripts/docker/quick_start/account.key --abi-path $HOME/goduck/scripts/example/broker.abi --address http://localhost:8545 0x857133c5C69e6Ce66F7AD46F200B9B3573e77582 audit "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11^1"

======= invoke function audit =======
invoke contract success, tx hash is: 0xf9d58b285c394ea91d4b34d0d4f9c6c61931160c7b280fc375bb2100e0f75333
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

我们会提供已经准备好的fabric跨链合约contract文件，但部署时需要注明需要跨链的bitxhub版本，因为不同版本的bitxhub对应的跨链合约可能有查别。

执行以下命令可以直接下载相应版本合约并部署：
```shell
goduck fabric contract chaincode --bxh-version v1.23.0

# 部署成功后会依次打印出几个步骤的日志，日志中有部分报错不影响，只要没有红色报错，且能大致打印出以下日志即可：
install default interchain chaincode
===> Install chaincode
===> 1. Deploying broker, transfer and data_swapper chaincode
Installing chaincode broker on org[org2] peers:
-- grpcs://localhost:9051
-- grpcs://localhost:10051
...successfuly installed chaincode broker.v0 on peer localhost:10051.
...successfuly installed chaincode broker.v0 on peer localhost:9051.
...successfuly instantiated chaincode broker on channel mychannel.
Installing chaincode transfer on org[org2] peers:
-- grpcs://localhost:9051
-- grpcs://localhost:10051
...successfuly installed chaincode transfer.v0 on peer localhost:10051.
...successfuly installed chaincode transfer.v0 on peer localhost:9051.
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
===> 3. Set (key: path, value: /Users/weiyijie/.goduck) in data_swapper chaincode
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: 
***** |  Response[1]:
***** |  |  Payload: 
****************************************************************************************************
===> 4. Register transfer and data_swapper chaincode to broker chaincode
****************************************************************************************************
***** |  Response[0]:
***** |  |  Payload: mychannel&transfer
***** |  Response[1]:
***** |  |  Payload: mychannel&transfer
****************************************************************************************************
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
goduck bitxhub start

======> Start bitxhub cluster
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
goduck pier start

======> Generate configuration files for pier_ethereum
【1】generate configuration files
【2】copy pier plugin and appchain config
======> Rewrite config for pier_ethereum
【1】rewrite pier.toml
【2】rewrite appchain config
【3】rewrite api
======> Start pier of ethereum in binary...
You can use the "goduck status list" command to check the status of the startup pier.

```

#### 3.1.2 注册应用链以及服务

第一步：确保账户有钱  
注册应用链之前，首先需要保证应用链管理员有注册应用链的权限，即账户有钱，可以由中继链管理员给应用链管理员转账。
这一部分不属于goduck功能，可以使用bitxhub命令行实现，示例如下：
```shell
#查看网关id
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub key show --path $HOME/.goduck/pier/.pier_ethereum/key.json

private key: 2dbac8637045b71c1f03462a919cf8ccd84828974e4dc3fa5bf5d2583ca4f677
public key: 04c63aea5849852aa406515bf8a88893a2c8e9eda2b1d8d952b50816a7367061c952e3f74d4639205e699bc9a8b977377d89640f3a4a70d7fd5d48deb431a1b0f8
address: 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed
#记录下网关账户地址以便后续使用

#中继链转账
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub client transfer --key $HOME/.goduck/bitxhub/.bitxhub/node1/key.json --to 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed --amount 100000000000000000

{"tx_hash":"0xB86a421169Fa30AC19Dad443E34E91Cd4bcBF80D21DF35CA4c5d5239AC57fA0B"}

#余额查询
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub client account --address 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed

{
  "type": "normal",
  "balance": 100000000000000000,
  "contract_count": 0,
  "code_hash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}

```

第二步：提交注册提案
确保账户有钱后，即可进行应用链注册操作，示例如下：

```shell
#将编译后的1.23.0版本pier二进制文件拷贝到pier生成目录 并切换到对应位置
cp $HOME/.goduck/bin/pier_darwin_v1.23.0/pier $HOME/.goduck/pier/.pier_ethereum/ 
cd $HOME/.goduck/pier/.pier_ethereum/ 

#网关执行应用链注册操作
pier --repo ./ appchain register --appchain-id "ethappchain1" --name "eth1" --type "ETH" --trustroot $HOME/.goduck/pier/.pier_ethereum/ethereum/ether.validators --broker 0x857133c5C69e6Ce66F7AD46F200B9B3573e77582 --desc "desc" --master-rule "0x00000000000000000000000000000000000000a2" --rule-url "http://github.com" --admin 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed --reason "reason" 

#应用链注册成功，等待投票提案通过
Register appchain successfully, wait for proposal 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-0 to finish.
```
第三步：提案投票通过  
注册提案提交后需要进行投票，投票部分不属于goduck的功能，可以使用bitxhub命令行实现，示例如下：
```shell
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node1 client governance vote --id 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-0 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node2 client governance vote --id 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-0 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node3 client governance vote --id 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-0 --info approve --reason reason
vote successfully!
```

完成投票提案后查看应用链状态
```shell
#根据应用链名称查询状态
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo  $HOME/.goduck/bitxhub/.bitxhub/node1 client governance appchain info --name "eth1"

Id            Name  Type  Broker                                      Status     Desc  Version
--            ----  ----  ------                                      ------     ----  -------
ethappchain1  eth1  ETH   0x857133c5C69e6Ce66F7AD46F200B9B3573e77582  available  desc  0

#根据应用链Id查询状态
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo  $HOME/.goduck/bitxhub/.bitxhub/node1 client governance appchain status --id "ethappchain1"

appchain ethappchain1 is available
```
第四步：提交注册服务提案
完成应用链注册后，即可对应用链服务进行注册操作，示例如下
```shell
#网关向中继链注册服务
pier --repo ./ appchain service register --appchain-id "ethappchain1" --service-id "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11" --name "ethService" --intro "" --type CallContract --permit "" --details "test"--reason "reason"

Register appchain service for ethappchain1:0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 successfully, wait for proposal 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-1 to finish.
```

第五步：服务提案投票通过 
投票过程同第三步，示例如下
```shell
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node1 client governance vote --id 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-1 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node2 client governance vote --id 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-1 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node3 client governance vote --id 0xDa5cF4F0adb9B75bc6FEADeb7ddbf0769399a2ed-1 --info approve --reason reason
vote successfully!
```

完成投票提案后查看服务状态
```shell
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo  $HOME/.goduck/bitxhub/.bitxhub/node1 client governance service status --id "ethappchain1:0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11"

service ethappchain1:0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 is available
```

#### 3.1.3 启动pier
成功注册应用链后pier启动即准备完成。  
我们可以在对应路径下启动pier
```shell
# 注意需要指定pier的启动目录，默认是$HOME/.goduck/pier/.pier_ethereum
cd $HOME/.goduck/pier/.pier_ethereum
pier --repo ./ start

```


### 3.2 启动连接fabric的pier
#### 3.2.1 启动pier
```shell
#复制出一份新的pier配置文件
cp $HOME/.goduck/pier_config/v1.23.0/pier_modify_config.toml $HOME/.goduck/pier_config/v1.23.0//pier_modify_config1.toml
#修改网关端口配置
vim $HOME/.goduck/pier_config/v1.23.0//pier_modify_config1.toml
httpPort = 44564
pprofPort = 44560
apiPort = 8081
```
然后启动指定fabric应用链类型及修改后的可修改配置文件启动pier：
```shell
goduck pier start --version v1.23.0 --appchain fabric --configPath $HOME/.goduck/pier_config/v1.23.0//pier_modify_config1.toml
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
#### 3.2.2 注册应用链以及服务
第一步：确保账户有钱  
注册应用链之前，首先需要保证应用链管理员有注册应用链的权限，即账户有钱，可以由中继链管理员给应用链管理员转账。
这一部分不属于goduck功能，可以使用bitxhub命令行实现，示例如下：
```shell
#查看网关id
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub key show --path $HOME/.goduck/pier/.pier_fabric/key.json

private key: 1909b0c48cc00d55ef0ffd9fa33198222f6157122cfcda7b45831881240ca560
public key: 040837c7382070c09027fc467344c0386066f25f318d044aae65c9bd6f8254eda5b9e4e2b092d89f16d0e94a805900665473cf3a5759263a9b73dac47f61891d96
address: 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D

#中继链转账
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub client transfer --key $HOME/.goduck/bitxhub/.bitxhub/node1/key.json --to 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D --amount 100000000000000000

{"tx_hash":"0xa80A372D7eAEF1985d6ef5c9F4D93dbfedf92136aDa5568202B02Fd6200ac6B0"}

#余额查询
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub client account --address 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D

{
  "type": "normal",
  "balance": 100000000000000000,
  "contract_count": 0,
  "code_hash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

第二步：提交注册提案

```shell
#将编译后的1.23.0版本pier二进制文件拷贝到pier生成目录 并切换到对应位置
cp $HOME/.goduck/bin/pier_darwin_v1.23.0/pier $HOME/.goduck/pier/.pier_fabric/ 
cd $HOME/.goduck/pier/.pier_fabric/ 

#网关执行应用链注册操作
pier --repo ./ appchain register --appchain-id "appchain1" --name "fabric1" --type "Fabric V1.4.3" --trustroot $HOME/.goduck/pier/.pier_fabric/fabric/fabric.validators --broker-cid mychanncel --broker-ccid broker --broker-v 1 --desc "desc" --master-rule "0x00000000000000000000000000000000000000a2" --rule-url "http://github.com" --admin 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D --reason "reason" 

#应用链注册成功，等待投票提案通过
Register appchain successfully, wait for proposal 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-0 to finish.
```

第三步：提案投票通过 
```shell
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node1 client governance vote --id 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-0 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node2 client governance vote --id 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-0 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node3 client governance vote --id 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-0 --info approve --reason reason
vote successfully!
```

完成投票提案后查看应用链状态
```shell
#根据应用链名称查询状态
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo  $HOME/.goduck/bitxhub/.bitxhub/node1 client governance appchain info --name "fabric1"

Id               Name     Type           Broker                                                                    Status     Desc  Version
--               ----     ----           ------                                                                    ------     ----  -------
fabricappchain1  fabric1  Fabric V1.4.3  {"channel_id":"mychanncel","chaincode_id":"broker","broker_version":"1"}  available  desc  0
#根据应用链Id查询状态
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo  $HOME/.goduck/bitxhub/.bitxhub/node1 client governance appchain status --id "fabricappchain1"

appchain fabricappchain1 is available
```

第四步：提交注册服务提案
完成应用链注册后，即可对应用链服务进行注册操作，示例如下
```shell
#网关向中继链注册服务
pier --repo ./ appchain service register --appchain-id "appchain1" --service-id "mychannel&transfer" --name "fabServer1" --intro "" --type CallContract --permit "" --details "test"--reason "reason"

Register appchain service for fabricappchain1:mychannel&transfer successfully, wait for proposal 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-1 to finish.
```


第五步：服务提案投票通过
投票过程同第三步，示例如下
```shell
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node1 client governance vote --id 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-1 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node2 client governance vote --id 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-1 --info approve --reason reason
vote successfully!
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo $HOME/.goduck/bitxhub/.bitxhub/node3 client governance vote --id 0x02E2De5aF4E06fC74D2EB34B87539F6fD57d777D-1 --info approve --reason reason
vote successfully!
```

完成投票提案后查看服务状态
```shell
$HOME/.goduck/bin/bitxhub_darwin_v1.23.0/bitxhub --repo  $HOME/.goduck/bitxhub/.bitxhub/node1 client governance service status --id "appchain1:mychannel&transfer"

service fabricappchain1:mychannel&transfer is available
```

#### 3.2.3 启动pier
成功注册应用链后pier启动即准备完成。  
我们可以在对应路径下启动pier
```shell
# 注意需要指定pier的启动目录，默认是$HOME/.goduck/pier/.pier_fabric
cd $HOME/.goduck/pier/.pier_fabric
pier --repo ./ start
```

## 4 跨链交易
```shell
#以太坊上Alice余额设置为10000
goduck ether contract invoke --key-path $HOME/goduck/scripts/docker/quick_start/account.key --address http://localhost:8545 --abi-path $HOME/goduck/scripts/example/transfer.abi 0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 setBalance Alice^10000

======= invoke function setBalance =======
invoke contract success, tx hash is: 0x332eac16be794d0c7ffdddc30ae304f1ebf56409e4fb766f3f8c2718f0f9e850

# 查询以太坊上Alice账户余额
goduck ether contract invoke --key-path $HOME/goduck/scripts/docker/quick_start/account.key --address http://localhost:8545 --abi-path $HOME/goduck/scripts/example/transfer.abi 0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 getBalance Alice

======= invoke function getBalance =======
call result: 10000

# 查询fabric上Alice账户余额
goduck fabric contract invoke transfer getBalance Alice

[fabric] invoke function "getBalance", receipt is 10000

# 以太坊向Fabric转账
goduck ether contract invoke --key-path $HOME/goduck/scripts/docker/quick_start/account.key --address http://localhost:8545 --abi-path $HOME/goduck/scripts/example/transfer.abi 0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 transfer "1356:appchain1:mychannel&transfer"^Alice^Alice^1000  
              
======= invoke function transfer =======
invoke contract success, tx hash is: 0x460cb0b8955b4bbb0b05e0e2c824324f9c79953c2dcd0d38995183c1ad85dd95

# 查询以太坊上Alice账户余额
goduck ether contract invoke --key-path $HOME/goduck/scripts/docker/quick_start/account.key --address http://localhost:8545 --abi-path $HOME/goduck/scripts/example/transfer.abi 0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 getBalance Alice

======= invoke function getBalance =======
call result: 9000

# 查询fabric上Alice账户余额
goduck fabric contract invoke transfer getBalance Alice

[fabric] invoke function "getBalance", receipt is 11000

```