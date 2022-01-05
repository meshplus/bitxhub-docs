`pier client`命令用于直连模式的应用链管理。

### 用法

```shell
$ pier client -h
NAME:
   Pier client - Command about appchain in pier

USAGE:
   Pier client command [command options] [arguments...]

COMMANDS:
   register  Register appchain in pier
   update    Update appchain in pier
   audit     Audit appchain in pier
   get       Get appchain info
   rule      register appchain validation rule

OPTIONS:
   --help, -h  show help

```

### 具体描述

#### `pier client register`

```shell
$ pier client register -h
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

```
idA=$(pier --repo ~/pier1 id)
idB=$(pier --repo ~/pier2 id)
pier --repo ~/pier1 client register --pier_id ${idB} --name fab --type fabric --desc simple --version 1 --validators ~/pier1/fabric/fabric.validators --consensusType raft
pier --repo ~/pier2 client register --pier_id ${idA} --name eth --type ethereum --desc simple --version 1 --validators ~/pier2/ethereum/ether.validators --consensusType raft
pier --repo ~/pier1 client rule --pier_id ${idB} --path ~/pier1/fabric/rule.wasm
pier --repo ~/pier2 client rule --pier_id ${idA} --path ~/pier2/ethereum/rule.wasm
```



