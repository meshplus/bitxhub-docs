# pier

`pier`命令有多组子命令，其作用是与应用链或中继链交互，`pier`命令描述如下：

```shell
$ pier -h

NAME:
   Pier - A Gateway Used To Cross the Appchain

USAGE:
   pier [global options] command [command options] [arguments...]

VERSION:
   Pier version: dev-master-dae0511


COMMANDS:
   appchain  Command about appchain in bitxhub
   id        Get appchain id
   init      Initialize pier local configuration
   network   Modify pier network configuration
   p2p       Command about p2p
   plugin    Initialize pier plugins configuration
   start     Start a long-running daemon process
   version   Show version about pier
   help, h   Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --repo value   Pier repository path
   --tls          enable tls between pier and bitxhub or not
   --help, -h     show help
   --version, -v  print the version
```

**参数解释**：

- `--repo`：可选参数，指定pier节点配置文件所在目录，如果不指定，默认使用`~/.pier`目录。
- `--tls`：可选参数，用于pier节点与bitxhub进行tls加密传输。如果不指定，默认为false。
- `--version`：可选参数，用于显示当前pier版本。

**子命令**：

## 1. pier appchain

`pier appchain`命令用于中继模式下，跨链网关Pier向中继链bitXHub实现应用链相关信息的注册、更新、变更等操作。

```shell
$ pier appchain -h

NAME:
   Pier appchain - Command about appchain in bitxhub

USAGE:
   Pier appchain command [command options] [arguments...]

COMMANDS:
   service     Command about appchain service
   register    Register appchain to bitxhub
   update      update appchain in bitxhub
   activate    activate appchain in bitxhub
   logout      logout appchain in bitxhub
   get         Get appchain info
   interchain  Query interchain info
   rule        Command about rule
   proposals   proposals manage command

OPTIONS:
   --help, -h  show help
```

**子命令**：

### pier appchain service

命令用于管理应用链上的服务。

```shell
$ pier appchain service -h

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

OPTIONS:
   --help, -h  show help
```

**子命令**：

#### pier appchain service register

命令用于注册应用链上的服务，比如应用链上一个用于交易的Transfer合约。

```shell
$ pier appchain service register -h

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
   --permit value       Specify contracts which are not allowed to invoke the service. If there are multiple contract addresses, separate them with ','
   --details value      Specify service details
   --reason value       Specify governance reason
```

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--service-id`: 服务ID，即应用链上提供服务的合约的地址；
- `--name`: 服务名称；
- `--intro`: 服务简介；
- `--type`: 服务类型，包括："CallContract"、"DepositCertificate"、"DataMigration"，目前仅支持 CallContract 类型；
- `--permit`: 指明不允许调用该合约的合约地址，若有多个合约地址，用","分割；
- `--details`: 服务细节描述；
- `--reason`: 注册理由。

**示例**：

```shell
$ pier --repo xxx/.pier appchain service register --appchain-id ethappchain --service-id "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11" --name "ethService1" --intro "" --type CallContract --permit "" --details "test" --reason "reason"

Register appchain service for ethappchain:0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 successfully, wait for proposal 0x1E2A3A840CA0C4d45f30fFDF90916c3Ae1300185-5 to finish.
```

#### pier appchain service update

命令用于更新应用链服务的信息。

```shell
$ pier appchain service update -h

NAME:
   Pier appchain service update - Update appchain service info to bitxhub

USAGE:
   Pier appchain service update [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain ID
   --service-id value   Specify service ID
   --name value         Specify service name
   --intro value        Specify service description
   --permit value       Specify contracts which are not allowed to invoke the service. If there are multiple contract addresses, separate them with ','
   --details value      Specify service details
   --reason value       Specify governance reason
```

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--service-id`: 服务ID，即应用链上提供服务的合约的地址；
- `--name`: 服务名称；
- `--intro`: 服务简介；
- `--permit`: 指明不允许调用该合约的合约地址？？；
- `--details`: 服务细节描述？？；
- `--reason`: 更新理由。

**示例**：

```shell
$ pier --repo xxx/.pier appchain service update --appchain-id ethappchain --service-id "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11" --name "ethService1" --intro "" --permit "" --details "update" --reason "reason"

Update appchain service for ethappchain:0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 successfully, wait for proposal 0x1E2A3A840CA0C4d45f30fFDF90916c3Ae1300185-6 to finish.
```

#### pier appchain service activate

命令用于激活应用链服务。

```shell
$ pier appchain service activate -h

NAME:
   Pier appchain service activate - Activate appchain service to bitxhub

USAGE:
   Pier appchain service activate [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain ID
   --service-id value   Specify service ID
   --reason value       Specify governance reason
```

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--service-id`: 服务ID，即应用链上提供服务的合约的地址；
- `--reason`: 激活理由。

**示例**：

```shell
# 处于冻结状态的应用链服务才能被激活
$ pier --repo xxx/.pier appchain service activate --appchain-id ethappchain --service-id "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11" --reason "reason"
```

#### pier appchain service logout

命令用于注销应用链服务。

```shell
$ pier appchain service logout -h

NAME:
   Pier appchain service logout - Logout appchain service to bitxhub

USAGE:
   Pier appchain service logout [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain ID
   --service-id value   Specify service ID
   --reason value       Specify governance reason
```

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--service-id`: 服务ID，即应用链上提供服务的合约的地址；
- `--reason`: 激活理由。

**示例**：

```shell
$ pier --repo xxx/.pier appchain service logout --appchain-id ethappchain --service-id "0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11" --reason "reason"

Logout appchain service for 0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11 successfully, wait for proposal 0x1E2A3A840CA0C4d45f30fFDF90916c3Ae1300185-7 to finish.
```

#### pier appchain service list

命令用于查询应用链上的服务。

```shell
$ pier appchain service list -h

NAME:
   Pier appchain service list - List appchain service from bitxhub belong to pier self

USAGE:
   Pier appchain service list [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain ID
```

**参数解释**：

- `--appchain-id`: 应用链ID。

**示例**：

```shell
$ pier --repo xxx/.pier appchain service list --appchain-id ethappchain

List appchain service by self successfully.
 [{"chain_id":"ethappchain","service_id":"0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11","name":"ethService1","type":"CallContract","intro":"","ordered":true,"permission":{},"pubkey_type":"","details":"test","create_time":1655870394658350000,"score":0,"evaluation_records":{},"invoke_count":0,"invoke_success_rate":0,"transfer_records":{},"status":"updating","fsm":{}}]
```

### pier appchain register

命令用于向bitxhub注册应用链。

```shell
$ pier appchain register

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

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--name`: 应用链名称；
- `--type`: 应用链类型，ETH、FABRIC；
- `--trustroot`: 应用链的验证者（暂时未使用）；
- `--broker`: 应用链上的broker合约地址；
- `--broker-cid`: fabric应用链上broker合约的channel ID；
- `--broker-ccid`: fabric应用链上broker合约的chaincode ID；
- `--broker-v`: fabric应用链上broker合约的版本；
- `--desc`: 应用链描述；
- `--master-rule`: 应用链主规则；
- `--rule-url`: 应用规则URL；
- `--admin`: 应用链管理员，多个管理员地址用","隔开，默认包含当前注册应用链的pier节点；
- `--reason`: 注册理由；
- `--addr`: 对接的bitxhub节点，可不指定，会在--repo目录下的pier.toml中寻找。

**示例**：

```shell
# 注册应用链前，需保证当前pier节点余额充足（可用bitxhub client transfer命令对当前pier节点地址进行转账）
# bitxhub管理员节点需对提案投票通过后，应用链才注册成功
$ PIER_CONFIG_PATH="xxx/.pier"
$ pier --repo $PIER_CONFIG_PATH appchain register --appchain-id "ethappchain" --name "ethereum" \
 --type "ETH" --trustroot $PIER_CONFIG_PATH/ethereum/ether.validators --broker \
 0x857133c5C69e6Ce66F7AD46F200B9B3573e77582  --desc "desc" --master-rule \
 "0x00000000000000000000000000000000000000a2" --rule-url "http://github.com" --reason "reason"

appchain register successfully, chain id is 0xcb33b10104cd217aAB4b302e9BbeEd1957EDaA31, proposal id is 0xcb33b10104cd217aAB4b302e9BbeEd1957EDaA31-0
```

### pier appchain update

命令用于更新应用链信息。

```shell
$ pier appchain update -h

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

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--name`: 应用链名称；
- `--trustroot`: 应用链的验证者（暂时未使用）；
- `--desc`: 应用链描述；
- `--admin`: 应用链管理员，多个管理员地址用","隔开，默认包含当前注册应用链的pier节点；
- `--reason`: 理由。

**示例**：

```shell
# 同样管理员节点投票通过
$ pier --repo xxx/.pier appchain update --appchain-id "ethappchain" --name "ether"
```

### pier appchain activate

命令用于激活应用链。

```shell
$ pier appchain activate -h

NAME:
   Pier appchain activate - activate appchain in bitxhub

USAGE:
   Pier appchain activate [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
   --reason value       Specify governance reason
```

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--reason`: 理由。

**示例**：

```shell
# 处于冻结的应用链才能被激活
$ pier --repo xxx/.pier1 appchain activate --appchain-id appchain
```

### pier appchain logout

命令用于注销应用链。

```shell
$ pier appchain logout -h

NAME:
   Pier appchain logout - logout appchain in bitxhub

USAGE:
   Pier appchain logout [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
   --reason value       Specify governance reason
```

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--reason`: 理由。

### pier appchain get

命令用于获取应用链信息。

```shell
$ pier appchain get -h

NAME:
   Pier appchain get - Get appchain info

USAGE:
   Pier appchain get [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
```

**参数解释**：

- `--appchain-id`: 应用链ID。

**示例**：

```shell
$ pier --repo xxx/.pier  appchain get --appchain-id "ethappchain"

{"id":"ethappchain","chain_name":"ethereum","chain_type":"ETH","trust_root":"MHgwMDBmMWE3YTA4Y2NjNDhlNWQzMGY4MDg1MGNmMWNmMjgzYWEzYWJkLDB4ZTkzYjkyZjFkYTA4ZjkyNWJkZWU0NGU5MWU3NzY4MzgwYWU4MzMwNywweGIxOGM4NTc1ZTMyODRlNzliOTIxMDAwMjVhMzEzNzhmZWI4MTAwZDYsMHg4NTZFMkI5QTVGQTgyRkQxQjAzMUQxRkY2ODYzODY0REJBQzc5OTVE","broker":"MHg4NTcxMzNjNUM2OWU2Q2U2NkY3QUQ0NkYyMDBCOUIzNTczZTc3NTgy","desc":"desc","version":0,"status":"logouting","fsm":{}}
```

### pier appchain interchain

查询应用链的跨链消息。

```shell
$ pier appchain interchain -h

NAME:
   Pier appchain interchain - Query interchain info

USAGE:
   Pier appchain interchain command [command options] [arguments...]

COMMANDS:
   ibtp  Query ibtp by id

OPTIONS:
   --key value  Specify key.json path
   --help, -h   show help
```

**参数解释**：

- `--key`: pier私钥key.json的路径。

**子命令**：

#### pier appchain interchain ibtp

查询应用链的跨链消息。

```shell
$ pier appchain interchain --key xxx/.pier/key.json ibtp -h

NAME:
   Pier appchain interchain ibtp - Query ibtp by id

USAGE:
   Pier appchain interchain ibtp [command options] [arguments...]

OPTIONS:
   --id value  Specific ibtp id
```

**参数解释**：

- `--id`: 跨链消息的IBTP id，格式为 `<IBTP.From>-<IBTP.To>`。

**示例**：

```shell
# 需先产生一条跨链消息
$ PIER_CONFIG_PATH="xxx/.pier"
$ pier --repo $PIER_CONFIG_PATH appchain interchain --key $PIER_CONFIG_PATH/key.json ibtp --id 1356:appchain1:0x9D189953252f751DAef58f91CF620Df2A3c122e1-1356:appchain2:0xAb8A1888D6141c2589E270854Ffdc27C79E7a7fb
```

### pier appchain rule

命令用于应用链验证规则的管理。

```shell
$ pier appchain rule -h

NAME:
   Pier appchain rule - Command about rule

USAGE:
   Pier appchain rule command [command options] [arguments...]

COMMANDS:
   deploy    Deploy validation rule
   register  Register validation rule
   update    update master rule
   logout    logout validation rule

OPTIONS:
   --help, -h  show help
```

**子命令**：

#### pier appchain rule deploy

命令用于应用链验证规则的部署。

```shell
$ pier appchain rule deploy -h

NAME:
   Pier appchain rule deploy - Deploy validation rule

USAGE:
   Pier appchain rule deploy [command options] [arguments...]

OPTIONS:
   --path value  Specify rule path
```

**参数解释**：

- `--path`: 待部署的验证规则的路径，若使用pier plugin生成应用链插件，则会在插件目录下，比如repo/ethereum/validating.wasm。

**示例**：

```shell
# 部署成功会输出验证规则的地址
$ PIER_CONFIG_PATH="xxx/.pier"
$ pier --repo $PIER_CONFIG_PATH appchain rule deploy --path=$PIER_CONFIG_PATH/ethereum/validating.wasm

Deploy rule successfully: 0xE03De4acF88FB2158b9cAA189A560d51d0927060
```

#### pier appchain rule register

命令用于应用链验证规则的注册。

```shell
$ pier appchain rule register -h

NAME:
   Pier appchain rule register - Register validation rule

USAGE:
   Pier appchain rule register [command options] [arguments...]

OPTIONS:
   --appchain-id value  Specify appchain id
   --rule value         Specify appchain rule addr
   --rule-url value     Specify appchain rule url
```

**参数解释**：

- `--appchain-id`: 应用链ID；
- `--rule`: 验证规则的地址，使用pier appchain rule deploy命令部署规则后，会输出规则地址；
- `--rule-url`: 验证规则的URL。

**示例**：

```shell
$ pier --repo xxx/.pier appchain rule register --appchain-id ethappchain --rule 0xE03De4acF88FB2158b9cAA189A560d51d0927060

Register rule to bitxhub for appchain ethappchain successfully.


# 可通过如下命令查看规则是否注册成功
$ bitxhub client governance rule all --id ethappchain

ChainID      RuleAddress                                 Status     Master  CreateTime
-------      -----------                                 ------     ------  ----------
ethappchain  0x00000000000000000000000000000000000000a2  available  true    1655866515834622000
ethappchain  0xE03De4acF88FB2158b9cAA189A560d51d0927060  bindable   false   1655868691765624000

```

#### pier appchain rule update

命令用于将指定的应用链验证规则更新为该应用链的主规则。

```shell
$ pier appchain rule update -h

NAME:
   Pier appchain rule update - update master rule

USAGE:
   Pier appchain rule update [command options] [arguments...]

OPTIONS:
   --addr value         Specify rule addr
   --appchain-id value  Specify appchain id
   --reason value       Specify governance reason
```

**参数解释**：

- `--addr`: 待更新的验证规则的地址；
- `--appchain-id`: 应用链ID；
- `--reason`: 更新理由。

**示例**：

```shell
# bitxhub管理员节点需对提案通过后，才更新成功
$ pier --repo xxx/.pier appchain rule update --appchain-id ethappchain --addr 0xE03De4acF88FB2158b9cAA189A560d51d0927060

Update master rule to bitxhub for appchain ethappchain successfully, wait for proposal 0x1E2A3A840CA0C4d45f30fFDF90916c3Ae1300185-3 to finish.


# 可通过如下命令查看该规则是否成功更新为主规则
$ bitxhub client governance rule all --id ethappchain

ChainID      RuleAddress                                 Status     Master  CreateTime
-------      -----------                                 ------     ------  ----------
ethappchain  0x00000000000000000000000000000000000000a2  bindable   false   1655866515834622000
ethappchain  0xE03De4acF88FB2158b9cAA189A560d51d0927060  available  true    1655868691765624000
```

#### pier appchain rule logout

命令用于注销应用链验证规则。

```shell
$ pier appchain rule logout -h

NAME:
   Pier appchain rule logout - logout validation rule

USAGE:
   Pier appchain rule logout [command options] [arguments...]

OPTIONS:
   --addr value         Specify rule addr
   --appchain-id value  Specify appchain id
```

**参数解释**：

- `--addr`: 待注销的验证规则的地址；
- `--appchain-id`: 应用链ID。

**示例**：

```shell
# 不能注销主验证规则，若执意要注销，需先将其它规则更新为主规则
$ pier --repo .pier1 appchain rule logout --appchain-id ethappchain --addr 0xE03De4acF88FB2158b9cAA189A560d51d0927060

The logout request was submitted successfully
```

### pier appchain proposals

命令用于提案的管理，目前仅包括提案的撤回。

```shell
$ pier appchain proposals -h

NAME:
   Pier appchain proposals - proposals manage command

USAGE:
   Pier appchain proposals command [command options] [arguments...]

COMMANDS:
   withdraw  withdraw a proposal

OPTIONS:
   --help, -h  show help
```

**子命令**：

#### pier appchain proposals withdraw

命令用于提案的撤回。

```shell
$ pier appchain proposals withdraw -h

NAME:
   Pier appchain proposals withdraw - withdraw a proposal

USAGE:
   Pier appchain proposals withdraw [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --reason value  Specify governance reason
```

**参数解释**：

- `--id`: 提案ID；
- `--reason`: 撤回理由。

**示例**：

```shell
# 若成功无输出
$ pier --repo xxx/.pier appchain proposals withdraw --id 0x1E2A3A840CA0C4d45f30fFDF90916c3Ae1300185-1
```

## 2. pier id

`pier id`命令用于获取所绑定的应用链地址。

```shell
$ pier id -h

NAME:
   pier id - Get appchain id

USAGE:
   pier id [arguments...]
```

**示例**：

```shell
# pier配置文件目录路径。若不指定，则默认 ~/.pier 目录
$ pier --repo xxx/.pier id

0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb
```

## 3. pier init

`pier init`命令用于初始化pier相关配置，若不指定`--repo`，则默认会在`~/.pier`目录下生成公私钥、ca证书等配置文件。

```shell
$ pier init -h

NAME:
   Pier init - Initialize pier local configuration

USAGE:
   Pier init command [command options] [arguments...]

COMMANDS:
   relay   Initialize pier relay mode configuration
   direct  Initialize pier direct mode configuration
   union   Initialize pier union mode configuration

OPTIONS:
   --algo value         Specify crypto algorithm (default: "Secp256k1")
   --http-port value    Specify http port (default: 44544)
   --pprof-port value   Specify pprof port (default: 44555)
   --enable-tls         Enable TLS or not
   --tlsca value        Specify TLS CA certificate path (default: "certs/agency.cert")
   --common-name value  Specify common name to verify (default: "BitXHub")
   --ha value           Specify if pier will run in single mode or high availability mode (default: "single")
   --help, -h           show help
```

**参数解释**：

- `--algo`:指定加密算法类型，目前支持RSA、SM2、ECDSA_P256、Secp256k1等常见的加密算法，默认为Secp256k1。
- `--http-port`：可选参数，指定监听的http端口号，默认为44544。
- `--pprof-port`：可选参数，指定pprof的端口号，默认为44555.
- `--enable-tls`：可选参数，是否进行TLS加密传输，默认为false。
- `--tlsca` ：可选参数，TLS CA路径，默认为certs/ca.pem。
- `--common-name` ：可选参数，指定ca证书的域名。默认为localhost。
- `--ha`：可选参数，选择网关是否高可用，高可用功能为商业版本，默认只有单个网关。

**子命令**：

### pier init relay

初始化中继模式下pier节点的配置。

```shell
$ pier init relay -h

NAME:
   Pier init relay - Initialize pier relay mode configuration

USAGE:
   Pier init relay [command options] [arguments...]

OPTIONS:
   --addrs value   Specify bitxhub nodes' address (default: "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014")
   --quorum value  Specify the quorum number of BitXHub (default: "2")
```

**参数解释**：

- `--addrs`：可选参数，指定bixhub各节点地址及端口号。
- `--quorum`：可选参数，指定bixhub集群的quorum个数，与具体共识算法与节点规模相关。

**示例**：

```shell
# 若不指定 --repo ，则默认 ~/.pier 目录
$ pier --repo xxx/.pier init relay
```

### pier init direct

初始化直连模式pier节点的配置。

```shell
$ pier init direct -h

NAME:
   Pier init direct - Initialize pier direct mode configuration

USAGE:
   Pier init direct [command options] [arguments...]

OPTIONS:
   --addPier value  Specify counter party piers to connect, input looks like: [ip]:[port]#[Pid]
```

**参数解释**：

- `--addPier`：可选参数，指定直连模式下pier的网络信息（包含自己），可暂不设置，一般init过后，在pier配置目录下的network.toml中配置。

**示例**：

```shell
# 若不指定 --repo ，则默认 ~/.pier 目录
$ pier --repo xxx/.pier init direct
```

### pier init union

初始化大规模模式下pier节点的配置。

```shell
$ pier init union -h

NAME:
   Pier init union - Initialize pier union mode configuration

USAGE:
   Pier init union [command options] [arguments...]

OPTIONS:
   --addrs value    Specify bitxhub nodes' address (default: "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014")
   --addPier value  Specify the remote union piers to connect, input looks like: [ip]:[port]#[Pid]
```

**参数解释**：

- `--addrs`：可选参数，指定bixhub各节点地址及端口号。
- `--addPier`：可选参数，指定直连模式下pier的网络信息（包含自己），可暂不设置，一般init过后，在pier配置目录下的network.toml中配置。

**示例**：

```shell
# 若不指定 --repo ，则默认 ~/.pier 目录
$ pier --repo xxx/.pier init union
```

## 4. pier network

`pier network`命令用于配置直连模式或者大规模模式下节点的网络信息。

```shell
$ pier network -h

NAME:
   Pier network - Modify pier network configuration

USAGE:
   Pier network command [command options] [arguments...]

COMMANDS:
   relay   Modify pier relay mode network configuration
   direct  Modify pier direct mode network configuration
   union   Modify pier union mode network configuration

OPTIONS:
   --help, -h  show help
```

**子命令**：

### pier network relay

用于修改中继模式下pier节点的network.toml配置。

```shell
$ pier network relay -h

NAME:
   Pier network relay - Modify pier relay mode network configuration

USAGE:
   Pier network relay [command options] [arguments...]

OPTIONS:
   --addNodes value  add bitxhub node address
   --delNodes value  delete bitxhub node address
```

**参数解释**：

- `--addNodes`：可选参数，指定新增的bixhub节点地址及端口号，如localhost:60015。
- `--delNodes`：可选参数，指定需要删除的bixhub节点地址及端口号，如localhost:60011。

**示例**：

其中地址为pier启动时所在地址，端口号可任意指定可用端口，

```shell
$ PIER_CONFIG_PATH="xxx/.pier"
$ pier --repo $PIER_CONFIG_PATH network relay --addNodes localhost:60015

# 查看配置文件是否有更改
$ cat $PIER_CONFIG_PATH/pier.toml
…………
[mode.relay]
    addrs = ["localhost:60015", "localhost:60011", "localhost:60012", "localhost:60013", "localhost:60014"]
…………
```

可以看到配置文件pier.toml下的addr在原有的4个节点基础上新增了localhost:60015。

### pier network direct

用于直连模式下pier节点的network.toml配置。

```shell
$ pier network direct -h

NAME:
   Pier network direct - Modify pier direct mode network configuration

USAGE:
   Pier network direct [command options] [arguments...]

OPTIONS:
   --addPier value  add pier address and pid, split with "#"
   --delPier value  delete pier address and pid, split with "#"
```

**参数解释**：

- `--addPier`：必选选参数，添加pier的网络配置（包括自己），如：127.0.0.1:5554#QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ。其中地址为pier启动时所在地址，端口号可任意指定可用端口。
- `--delPier`：必选参数，指定需要删除的bixhub节点地址及端口号，如127.0.0.1:5554#QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ。

**示例**：

```shell
# pier1 pier2配置文件目录路径，pier init时生成
$ PIER1_CONFIG_PATH="xxx/.pier1"
$ PIER2_CONFIG_PATH="xxx/.pier2"

# 获取pier1和pier2的pid
$ PID1=$(pier --repo $PIER1_CONFIG_PATH p2p id)
$ PID2=$(pier --repo $PIER2_CONFIG_PATH p2p id)

# 修改pier1的network.toml
$ pier --repo $PIER1_CONFIG_PATH network direct --addPier "127.0.0.1:5554#$PID1" --addPier "127.0.0.1:5555#$PID2"

# 修改pier2的network.toml
$ pier --repo $PIER2_CONFIG_PATH network direct --addPier "127.0.0.1:5554#$PID1" --addPier "127.0.0.1:5555#$PID2"

# 查看配置文件是否有更改
$ cat $PIER1_CONFIG_PATH/network.toml
$ cat $PIER2_CONFIG_PATH/network.toml

[[piers]]
  hosts = ["/ip4/127.0.0.1/tcp/5554/p2p/"]
  pid = "QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ"

[[piers]]
  hosts = ["/ip4/127.0.0.1/tcp/5555/p2p/"]
  pid = "Qmd5VnC2j5Mix28eqVPEm5gWKKSp8nQWkxq2DE1DecEzXX"
```

### pier network union

用于大规模模式的bitxhub节点修改与network.toml配置，命令描述如下：

```shell
$ pier network union -h

NAME:
   Pier network union - Modify pier union mode network configuration

USAGE:
   Pier network union [command options] [arguments...]

OPTIONS:
   --addNodes value  add bitxhub node address
   --delNodes value  delete bitxhub node address
   --addPier value   add pier address and pid, split with "#"
   --delPier value   add pier address and pid, split with "#"
```

**示例**：

参考 pier network rela 与 pier network direct 的示例。

## 5. pier p2p

`pier p2p`命令用于直连模式下pier网络通信的pid。

```shell
$ pier p2p -h

NAME:
   Pier p2p - Command about p2p

USAGE:
   Pier p2p command [command options] [arguments...]

COMMANDS:
   id  get pier unique id in p2p network

OPTIONS:
   --help, -h  show help
```

**示例**：

```shell
$ pier --repo xxx/.pier p2p id

QmNpQvh8Lm7gwt3sDqwJ1pgNn6taJLqdgtRVyZWhsD5bND
```

## 6. pier plugin

`pier plugin`命令用于配置应用链插件相关信息。

```shell
$ pier plugin -h

NAME:
   Pier plugin - Initialize pier plugins configuration

USAGE:
   Pier plugin command [command options] [arguments...]

COMMANDS:
   fabric    Initialize pier and fabric plugin configuration
   ethereum  Initialize pier and ethereum plugin configuration

OPTIONS:
   --redownload  Re-download plugin or not
   --help, -h    show help
```

**参数解释**：

- `--redownload`：可选参数，是否重新下载插件，默认为true。

**子命令**：

### pier plugin fabric

配置fabric插件相关配置信息，并下载`pier-client-fabric`二进制文件。

```shell
$ pier plugin fabric -h

NAME:
   Pier plugin fabric - Initialize pier and fabric plugin configuration

USAGE:
   Pier plugin fabric [command options] [arguments...]

OPTIONS:
   --crypto-config value  Specify the path to crypto-config directory
   --config value         Specify the path to fabric config.yaml
   --event-filter value   Specify the event filter on fabric chaincode
   --username value       Specify the username to invoke fabric chaincode
   --ccid value           Specify chaincode id to invoke
   --channel-id value     Specify channel id
   --org value            Specify the organization
```

**参数解释**：

- `--crypto-config`：必选参数，指定fabric的文件目录，一般与repo目录一致，需传入绝对路径；
- `--config`：必选参数，指定fabric的config.yaml文件目录，默认在repo/fabric目录下，需传入绝对路径；
- `--event-filter`：可选参数，根据eventName过滤所需的事件；
- `--username`：可选参数，指定调用合约的用户名，默认为Admin；
- `--ccid`：可选参数，CCID 应匹配fabric的peer节点上链码的包名；
- `--channel-id`：可选参数，fabric链码部署所在的通道名；
- `--org`：可选参数，fabric上指定的peer节点所属的组织。

**示例**：

```shell
# 注意，由于安装包的地址为github，下载可能较慢，可以重试几次或通过设置代理
$ PIER_CONFIG_PATH="/xxx/xxx/.pier" # 此处需使用绝对路径
$ pier --repo $PIER_CONFIG_PATH plugin fabric --crypto-config $PIER_CONFIG_PATH --config $PIER_CONFIG_PATH/fabric

Start downloading https://github.com/meshplus/pier-client-fabric/releases/download/v1.18.1/fabric-client-v1.18.1-darwin
Finish downloading https://github.com/meshplus/pier-client-fabric/releases/download/v1.18.1/fabric-client-v1.18.1-darwin
```

### pier plugin ethereum

配置ethereum插件相关配置信息，并下载`pier-client-ethereum`二进制文件，命令描述如下：

```shell
NAME:
   Pier plugin ethereum - Initialize pier and ethereum plugin configuration

USAGE:
   Pier plugin ethereum [command options] [arguments...]

OPTIONS:
   --addr value            Specify ethereum websocket address
   --broker value          Specify ethereum broker contract address
   --key value             Specify the ethereum key to sign the transaction
   --password value        Specify the password of the key
   --min-confirm value     Specify minimum blocks to confirm the transaction (default: 0)
   --transfer value        Specify the transfer contract address
   --data-swapper value    Specify the data swapper contract address
   --timeout-height value  Specify the ibtp timeout block height
```

**参数解释**：

- `--addr`：以太坊的websocket地址，如果是自己搭建的私链，一般为ws://localhost:8546；
- `--broker`：必选参数，broker合约部署地址；
- `--key`：以太坊用于交易签名的账户私钥。若不指定，可自行将账户私钥复制到repo/ethereum目录下的account.key文件中；
- `--password`：以太坊账户密码。若不指定，可自行将密码填入repo/ethereum目录下的password文件中；
- `--min-confirm`：最小确认数，用于本地测试建议设置为0；
- `--transfer`：transfer合约地址；
- `--data-swapper`：data swapper合约地址；
- `--timeout-height`：设置超时块高H，如跨链交易在中继链的区块高度为h的区块上链后，h+H的高度还未收到目的链回执，将触发超时回滚。

**示例**：

```shell
# 注意，由于安装包的地址为github，下载可能较慢，可以重试几次或通过设置代理
# 需先在以太坊链上部署好broker合约
# 可先不指定key和password，后续自行修改repo/ethereum目录下的account.key文件和password文件
$ pier --repo xxx/.pier plugin ethereum --broker 0x8d0522826c787c20fEDA2A2E3B3557D9644a1CE5 --addr ws://localhost:8546 --key xxx/datadir/keystore/xxx --password "123" --min-confirm 0 --timeout-height 10

Start downloading https://github.com/meshplus/pier-client-ethereum/releases/download/v1.18.0/eth-client-v1.18.0-darwin
Finish downloading https://github.com/meshplus/pier-client-ethereum/releases/download/v1.18.0/eth-client-v1.18.0-darwin
```

## 7. pier start

`pier start`命令用于启动pier。

```shell
$ pier start -h

NAME:
   pier start - Start a long-running daemon process

USAGE:
   pier start [arguments...]
```

**示例**：

```shell
$ pier --repo xxx/.pier start
Pier version: dev-release-1.18-3826f39
App build date: 2022-01-06T17:04:07
System version: darwin/amd64
Golang version: go1.16.6

2022-01-06T19:50:21.504+0800 [DEBUG] plugin: starting plugin: path=/bin/sh args="[sh -c /Users/liruoxin/testDirect/release1.18/.pier2/plugins/eth-client start]"
2022-01-06T19:50:21.506+0800 [DEBUG] plugin: plugin started: path=/bin/sh pid=22551
2022-01-06T19:50:21.506+0800 [DEBUG] plugin: waiting for RPC address: path=/bin/sh
2022-01-06T19:50:21.549+0800 [DEBUG] plugin: using plugin: version=4
2022-01-06T19:50:21.549+0800 [DEBUG] plugin.sh: 2022-01-06T19:50:21.549+0800 [DEBUG] client: plugin address: network=unix address=/var/folders/xr/pnhf9j6n2k5_fysm6s0xkgt00000gn/T/plugin713572722
2022-01-06T19:50:21.550+0800 [TRACE] plugin.stdio: waiting for stdio data
2022-01-06T19:50:21.551+0800 [DEBUG] plugin.sh: 2022-01-06T19:50:21.551+0800 [INFO ] client: Basic appchain info: broker address=0xFb23Af09e3E8D83fd5575De9558920Bf351F05E8 ethereum node ip=ws://localhost:8546
Pprof on localhost:44556

2022-01-06 19:50:21 [INFO] [/Users/liruoxin/goproject/meshplus/pier/internal/app/pier.go:233] pier HA manager start
2022-01-06T19:50:21.624+0800 [DEBUG] plugin.sh: 2022-01-06T19:50:21.624+0800 [INFO ] client: Consumer started
INFO[2022-01-06T19:50:21.624] appchain adapter start                        module=appchain_adapter
INFO[2022-01-06T19:50:21.625] start p2p success                             module=lightp2p
INFO[2022-01-06T19:50:21.637] Connect successfully                          address:=appchain1 module=peer_mgr node=QmSQSh5uDBeKtjHzmECdazvrEWfYBQJAGTKFrWbaZFPnxZ
INFO[2022-01-06T19:50:21.637] direct adapter start                          module=direct_adapter
INFO[2022-01-06T19:50:21.654] Start To Recover IBTPs!                       module=exchanger
INFO[2022-01-06T19:50:21.655] End To Recover IBTPs!                         module=exchanger
INFO[2022-01-06T19:50:21.655] Exchanger started                             module=exchanger
INFO[2022-01-06T19:50:21.655] listenIBTPFromSrcAdaptToServicePairCh appchain:appchain2 Start!  module=exchanger
INFO[2022-01-06T19:50:21.655] listenIBTPFromDestAdaptToServicePairCh direct:appchain2 Start!  module=exchanger
…………
```

## 8. pier version

`pier version`命令用于查询版本号。

```shell
$ pier version -h

NAME:
   pier version - Show version about ap

USAGE:
   pier version [arguments...]
```

**示例**：

```shell
$ pier version

Pier version: dev-release-1.18-3826f39
App build date: 2022-01-06T17:04:07
System version: darwin/amd64
Golang version: go1.16.6
```
