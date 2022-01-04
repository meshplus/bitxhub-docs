直连模式下，网关pier支持对应用链的管理，包括注册、更新、审计应用链等功能。

注意：在进行应用链管理之前，请确保直连模式下跨链网关正常启动！！直连模式网关启动教程请参考[跨链网关直连模式部署](https://meshplus.github.io/bitxhub/bitxhub/usage/direct_mode_pier/pier_direct_mode_deploy/)

## 1. 应用链注册

### pier client register

对于直连模式下需要加入跨链网络应用链，需要首先由应用链管理员向需要跨链的目的链网关pier注册应用链，命令如下：

```shell
NAME:
   Pier client register - Register appchain in pier

USAGE:
   Pier client register [command options] [arguments...]

OPTIONS:
   --pier_id value        Specify target pier id
   --name value           Specify appchain name
   --type value           Specify appchain type
   --desc value           Specify appchain description
   --version value        Specify appchain version
   --validators value     Specify appchain validators path
   --consensusType value  Specify appchain consensus type
```

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录。

- `--pier_id`：必选参数，指定要连接的目的网关地址，网关地址可通过`pier [--repo <coonect pier's repository>] id`获得。

* `--name`：必选参数，指定应用链名称。
* `--type`：必选参数，指定应用链类型，如hyperchain、fabric等。
* `--validators`：必选参数，指定应用链的验证人信息所在的文件路径。
* `--desc`：必选参数，对应用链的描述信息。
* `--consensusType`：必选参数，指定p应用链的共识类型，如pbft、raft等。
* `--version`：必选参数，指定应用链版本信息。

### 示例说明

比如pier1与pier2进行ethereum应用链的注册，命令执行如下：

```shell
# 参数说明
pier [--repo <repository>] client register --pier_id <connect Appchain addr> --name <srcAppchain name> --type <appchain type> --validators <path of appchain validators file> --desc <description> --consensusType <appchain consensus type> --version <appchain version>

# 具体样例
export idA=$(pier --repo $HOME/.pier1 id)
export idB=$(pier --repo $HOME/.pier2 id)
# pier1在pier2上注册
$ pier --repo $HOME/.pier2 client register --pier_id ${idA} --name eth1 --type ethereum --validators $HOME/.pier1/ethereum/ether.validators --desc "test register direct eth1" --consensusType Pos --version 1.0.0
# 控制台输出
appchain register successfully, appchain is 0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb

# pier2在pier1上注册
$ pier --repo $HOME/.pier2 client register --pier_id ${idA} --name eth2 --type ethereum --validators $HOME/.pier2/ethereum/ether.validators --desc "test register direct eth2" --consensusType Pos --version 1.0.0
# 控制台输出
appchain register successfully, appchain is 0x23Fb0E7eF676467563d37D820F1b1Fddb0f9a2E1
```

可以看到应用链在双方网关注册成功。



## 2. 获取应用链信息

### pier client get

获取指定的应用链在本地pier的注册信息，命令如下：

```shell
$ pier [--repo <repository>] client get --pier_id <connect Appchain addr> 
```

- `--pier_id`：必选参数，注册在本地的应用链地址。

### 示例说明

比如在pier1往pier2成功注册后，在pier1执行如下命令：

```shell
$ pier --repo $HOME/.pier2 client get --pier_id 0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb
# 控制台输出
{"id":"0xfF8199Fae48C808b45667DA0CcaAEe839B1a10Cb","name":"eth1","validators":"0x000f1a7a08ccc48e5d30f80850cf1cf283aa3abd,0xe93b92f1da08f925bdee44e91e7768380ae83307,0xb18c8575e3284e79b92100025a31378feb8100d6,0x856E2B9A5FA82FD1B031D1FF6863864DBAC7995D","consensus_type":"Pos","status":"available","chain_type":"ethereum","desc":"test register direct eth1","version":"1.0.1","public_key":"…………","fsm":null}
```

说明pier1所绑定的eth1在pier2注册成功。

## 3. 部署验证规则

### pier client rule

在注册完应用链后，还需要在网关部署验证规则，命令如下：

```shell
pier [--repo <repository>] client rule --pier_id <connect Appchain addr> --path <rule_file>
```

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录。

- `--pier_id`：必选参数，指定要连接的目的网关地址，网关地址可通过`pier [--repo <coonect pier's repository>] id`获得。

* `--path`：必选参数，指定应用链的验证规则所在文件，一般为应用链插件配置文件下的`xxx.wasm`等。

### 示例说明

比如pier1与pier2进行ethereum应用链的注册，命令执行如下：

```shell
# pier1往pier2部署验证规则
$ pier --repo $HOME/.pier1 client rule --pier_id ${idB} --path $HOME/.pier1/ethereum/validating.wasm
# pier2往pier1部署验证规则
$ pier --repo $HOME/.pier2 client rule --pier_id ${idA} --path $HOME/.pier2/ethereum/validating.wasm
```



## 4. 更新应用链

### pier client update

如果应用链发生了更改，比如验证人信息发生了变化，需要更新其在对面应用链的网关信息。参数说明如下：

```shell
NAME:
   Pier client update - Update appchain in pier

USAGE:
   Pier client update [command options] [arguments...]

OPTIONS:
   --pier_id value        Specify target pier id
   --name value           Specify appchain name
   --type value           Specify appchain type
   --desc value           Specify appchain description
   --version value        Specify appchain version
   --validators value     Specify appchain validators path
   --consensusType value  Specify appchain consensus type
```

该命令参数含义与应用链注册命令的参数一致，不再赘述。

### 示例说明

比如进行ethereum应用链的共识算法类型发生变化，需要更新应用链，命令执行如下：

```shell
# 参数说明
$ pier [--repo <repository>] client update --pier_id <connect Appchain addr> --name <srcAppchain name> --type <appchain type> --validators <path of appchain validators file> --desc <description> --consensusType <appchain consensus type> --version <appchain version>

# 具体样例 
$ pier --repo $HOME/.pier2 client update --pier_id ${idA} --name eth2 --type ethereum --validators eth2/ethereum/ether.validators --desc "test register direct eth2" --consensusType Pow --version 1.0.2 

# 控制台输出
appchain update successfully, appchain is <nil>
```

对面pier收到后进行更新应用链信息。



## 5. 审计应用链

### pier client audit

**注意**：1.6.x版本此项非必须项，可不对其设置。

跨链网关对注册应用链信息进行审计，修改事务状态，参数说明如下：

```shell
USAGE:
   Pier client audit [command options] [arguments...]

OPTIONS:
   --id value          Specific appchain id
   --isApproved value  Specific approved signal
   --desc value        Specific audit description
```

参数解释：

- `--id`：必选参数，指定要连接的目的网关地址，网关地址可通过`pier [--repo <coonect pier's repository>] id`获得。
- `isApproved`：必选参数，是否通过注册。1为通过，0为不通过。
- `--desc`：必选参数，对应用链的描述信息。

### 示例说明

命令执行如下：

```shell
# 参数说明
$ pier [--repo <repository>] client audit --id <connect Appchain addr> ----isApproved <0 or 1> --desc <description>

# 具体样例
$ pier --repo $HOME/.pier1 client audit --id ${idB} --isApproved 1 --desc "test register direct eth2" 
# 控制台输出
audit appchain 0x23Fb0E7eF676467563d37D820F1b1Fddb0f9a2E1 successfully
```

pier1对pier2绑定的应用链注册提案审计通过。