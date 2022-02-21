# 应用链相关操作

为了方便用户体验跨链流程，GoDuck也提供快速启动应用链、部署应用链合约等功能。目前主要支持：

- 关于以太坊私链的启动、合约部署及调用；
- 关于fabric链的启动、合约部署及调用；
- 关于hyperchain/flato的合约部署及调用。

## 1 以太坊

关于以太坊应用链，我们对应不同bitxhub版本提供了已经部署好跨链合约的以太坊私链镜像，直接启动即可使用。

### 1.1 启动以太坊

#### goduck ether start

参数解释：

- `--type`：可选参数，指定启动模式，默认为`docker`模式，可指定为`binary`模式;
- `--bxh-version`：指定连接的bitxhub版本，不同版本的bitxhub对应的跨链合约可能有差别;

示例说明：

```shell

$ goduck ether start --bxh-version v1.11.2
exec:  /bin/bash ethereum.sh docker 1.3.0
start a new ethereum-node container
fc6b26e2507fb7da3ca04abea6d014968297383986596377cd42e8852b2b83e6
start ethereum private chain with data directory in $goduck_repo/ethereum/datadir.
```

以上示例该命令会以docker模式启动一条以太坊私链，切该链上已经部署好适配v1.11.2版本bitxhub的跨链合约，合约地址分别如下：

- `broker`: `0xD3880ea40670eD51C3e3C0ea089fDbDc9e3FBBb4`；
- `transfer`: `0x668a209Dc6562707469374B8235e37b8eC25db08`；
- `data_swapper`: `0x6db07a8b0f23367dc65c7DAD91da4F6bfD97D674`。

这些合约的abi文件分别在$goduck_repo/pier/ethereum/1.3.0/目录下，例如：

```shell

$ goduck_repo/pier/ethereum/1.3.0/

├── account.key
├── broker.abi
├── data_swapper.abi
├── ether.validators
├── ethereum.toml
├── password
├── transfer.abi
└── validating.wasm

0 directories, 8 files
```

### 1.2 关闭以太坊

#### goduck ether stop
#### goduck ether clean

关闭以太坊有stop和clean两个命令，推荐使用clean命令，在停止以太坊运行的同时会清除容器（如果是二进制模式启动会清除相关配置文件）。

示例说明：

```shell

$ goduck ether stop

exec:  /bin/bash ethereum.sh down
===> stop ethereum in binary...
===> stop ethereum in docker...
ethereum-node
ethereum docker container stopped

$ goduck ether clean

exec:  /bin/bash ethereum.sh clean
===> stop ethereum in binary...
===> stop ethereum in docker...
ethereum-node
ethereum docker container stopped
===> clean ethereum in binary...
===> clean ethereum in docker...
ethereum-node
ethereum docker container cleaned
```

### 1.3 以太坊合约部署

_1.1节中启动的以太坊以及有默认部署好的跨链合约，如果用户想要自定义solidity合约也可以使用本节命令进行合约部署_

#### goduck ether contract deploy

参数解释：

- `--address`：可选参数，指定以太坊应用链地址，默认启动地址为`http://localhost:8545`；
- `--key-path`：可选参数，指定部署合约账户私钥地址，默认为`$goduck_repo/ethereum/account.key`；
- `--psd-path`：可选参数，指定部署合约账户密码地址，默认为`$goduck_repo/ethereum/password`；
- `--code-path`：指定部署solidity合约地址，如有多个合约可以逗号隔开。

示例说明：

```shell

$ goduck ether contract deploy --code-path ./transfer.sol

======= $(pwd)/transfer.sol:Transfer =======
Deployed contract address is 0xd590f0c82d1DAfeabEf43D4c793C1Ac1A4e070B2
Contract JSON ABI
[{"constant":true,"inputs":[{"internalType":"string","name":"id","type":"string"}],"name":"getBalance","outputs":[{"internalType":"uint64","name":"","type":"uint64"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"internalType":"string","name":"sender","type":"string"},{"internalType":"string","name":"receiver","type":"string"},{"internalType":"uint64","name":"val","type":"uint64"}],"name":"interchainCharge","outputs":[{"internalType":"bool","name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"internalType":"string","name":"sender","type":"string"},{"internalType":"uint64","name":"val","type":"uint64"}],"name":"interchainRollback","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"internalType":"string","name":"self","type":"string"}],"name":"parseInt","outputs":[{"internalType":"uint256","name":"_ret","type":"uint256"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":false,"inputs":[{"internalType":"string","name":"id","type":"string"},{"internalType":"uint64","name":"amount","type":"uint64"}],"name":"setBalance","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"internalType":"string","name":"destContractDID","type":"string"},{"internalType":"string","name":"sender","type":"string"},{"internalType":"string","name":"receiver","type":"string"},{"internalType":"string","name":"amount","type":"string"}],"name":"transfer","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"}]
```

上述示例中，合约部署地址为`0xd590f0c82d1DAfeabEf43D4c793C1Ac1A4e070B2`，同时也打印出了合约部署生成的abi详情，可拷贝保存下来，后续合约调用时需要。

### 1.4 以太坊合约调用

#### goduck ether contract invoke [contract_address] [function] [args(optional)]

参数解释：

- `--address`：可选参数，指定以太坊应用链地址，默认启动地址为`http://localhost:8545`；
- `--key-path`：可选参数，指定部署合约账户私钥地址，默认为`$goduck_repo/ethereum/account.key`；
- `--psd-path`：可选参数，指定部署合约账户密码地址，默认为`$goduck_repo/ethereum/password`；
- `--code-path`：指定合约部署生成的abi详情文件地址。

在实际调用时，除了指定以上参数外，后续需要继续携带调用合约地址、调用方法名称以及调用参数（如有多个用逗号隔开）。

示例说明：

```shell

# 查询当前应用链上Alice账户余额
# - 0x668a209Dc6562707469374B8235e37b8eC25db08 为调用的transfer合约地址
# - getBalance 为调用transfer合约中的方法的名称
# - Alice为 调用方法getBalance的参数
$ goduck ether contract invoke --abi-path $goduck_repo/pier/ethereum/1.3.0/transfer.abi \
      0x668a209Dc6562707469374B8235e37b8eC25db08 getBalance Alice

======= invoke function getBalance =======
call result: 10000
# 返回当前链上Alice账户余额为10000

# 调用跨链转账
# - 0x668a209Dc6562707469374B8235e37b8eC25db08 为调用的transfer合约地址
# - transfer 为调用transfer合约中同名方法的名称
# - did:bitxhub:appchain0xda8CDA6556D786c350c864A2E19324EeE635be0d:"mychannel&transfer",Alice,Alice,1 为调用方法transfer的参数，依次为：
#   目的链合约did格式地址（示例中目的链为fabric链，故did的第三部分合约id为fabric合约channel名称拼接合约id），来源链账户发送者名称，目的链接收者名称，转账数额

$ goduck ether contract invoke --abi-path ~/.goduck/pier/ethereum/1.3.0/transfer.abi \
      0x668a209Dc6562707469374B8235e37b8eC25db08 transfer did:bitxhub:appchain0xda8CDA6556D786c350c864A2E19324EeE635be0d:"mychannel&transfer",Alice,Alice,1

======= invoke function transfer =======
invoke contract success, tx hash is: 0xe42acbba632a254064eaf603993c7931686ff58649f18f80d22a7a96eb9f72a0
# 返回当前跨链转账交易哈希
```

## 2 fabric

关于fabric应用链，链启动和合约部署是分开的操作。

### 2.1 启动fabric

#### goduck fabric start

示例说明：

```shell

$ goduck fabric start

# .....（部分日志省略）

# 启动成功后最后会打印出fabric的logo如下：
========= All GOOD, BYFN execution completed ===========


 _____   _   _   ____
| ____| | \ | | |  _ \
|  _|   |  \| | | | | |
| |___  | |\  | | |_| |
|_____| |_| \_| |____/

```

⚠️ 启动过程中可能需要科学上网。 如果出现报错或fabric-samples下载失败，建议删除fabric-samples文件，重新执行fabric启动命令。

### 2.2 关闭fabric

#### goduck fabric stop
#### goduck fabric clean

关闭fabric有stop和clean两个命令，推荐使用clean命令，在停止fabric相关容器的同时会清除所有相关镜像

示例说明：

```shell
# 关闭并清除应用链容器
goduck fabric stop

# 关闭并清除应用链容器，同时清除应用链及合约相关镜像
goduck fabric clean
```

### 2.3 fabric合约部署

#### goduck fabric contract chaincode

参数解释：

- `--config-path`：可选参数，指定fabric链的`config.yaml`文件，默认为`$goduck_repo/fabric/config.yaml`；
- `--code-path`：可选参数，指定部署合约文件，默认为`$goduck_repo/contracts`，如合约文件不存在会根据`--bxh-version`参数下载相应版本的默认合约文件；
- `--bxh-version`：指定连接的bitxhub版本，不同版本的bitxhub对应的跨链合约可能有差别。

示例说明：

```shell

$ goduck fabric contract chaincode --bxh-version v1.11.2

#......（部分日志省略）

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

### 2.4 fabric合约调用

#### goduck fabric contract invoke [chaincode_id] [function] [args(optional)]

参数解释：
- `--config-path`：可选参数，指定fabric链的`config.yaml`文件，默认为`$goduck_repo/fabric/config.yaml`。

示例说明：

```shell

# 查询当前应用链上Alice账户余额
# - transfer 为调用的transfer合约id
# - getBalance 为调用transfer合约中的方法的名称
# - Alice为 调用方法getBalance的参数
$ goduck fabric contract invoke transfer getBalance Alice

[fabric] invoke function "getBalance", receipt is 10000
# 返回当前链上Alice账户余额为10000

# 调用跨链转账
# - transfer 为调用的transfer合约id
# - transfer 为调用transfer合约中同名方法的名称
# - did:bitxhub:appchain0x74e52824fE3edf258C111257ba33f8e05cb755FC:0x668a209Dc6562707469374B8235e37b8eC25db08,Alice,Alice,1 为调用方法transfer的参数，依次为：
#   目的链合约did格式地址，来源链账户发送者名称，目的链接收者名称，转账数额
$ goduck fabric contract invoke transfer transfer did:bitxhub:appchain0x74e52824fE3edf258C111257ba33f8e05cb755FC:0x668a209Dc6562707469374B8235e37b8eC25db08,Alice,Alice,1

[fabric] invoke function "transfer", receipt is
```

## 3 hyperchain(flato)

关于hyperchain（flato同，下文省略说明），goduck提供合约部署调用功能。

### 3.1 hpc合约部署

#### goduck hpc deploy

参数解释：

- `--config-path`：可选参数，指定hyperchain链的配置文件目录，该目录需要包含hyperchain的hpc.account、hpc.toml及certs文件，默认为`$goduck_repo/hpc`；
- `--code-path`：指定部署合约文件；
- `--type`：指定合约类型，支持solc、java、jvm三种类型；
- `--local`：指定合约是否本地编译。

### 3.2 hpc合约调用

#### goduck hpc invoke [contract_address] [function] [args(optional)]

参数解释：

- `--config-path`：可选参数，指定hyperchain链的配置文件目录，该目录需要包含hyperchain的hpc.account、hpc.toml及certs文件，默认为`$goduck_repo/hpc`；
- `--abi-path`：指定solc类型合约的abi详情文件；
- `--type`：指定合约类型，支持solc、java、jvm三种类型。

示例说明：

```shell

# 查询当前应用链上Alice账户余额
# - 0x55f34bd04b2ea8047b46c2d891b1c2a0aff15102 为调用的transfer合约地址
# - getBalance 为调用transfer合约中的方法的名称
# - Alice为 调用方法getBalance的参数
$ goduck hpc invoke --abi-path ./transfer.abi 0x55f34bd04b2ea8047b46c2d891b1c2a0aff15102 getBalance Alice
```