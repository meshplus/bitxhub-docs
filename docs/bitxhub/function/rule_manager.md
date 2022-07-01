# 验证规则管理
中继链提供对应用链验证规则进行管理的功能。

## 1 概述

验证规则是应用链在中继链上自主部署的智能合约，应用链可以有多条验证规则，但只能有一个主验证规则，中继链的验证引擎会根据主验证规则验证应用链跨链请求的真伪。

中继链的验证规则管理主要包含 **验证规则注册**、**主验证规则切换**、**验证规则注销**。

## 2 验证规则注册
### 2.1 功能介绍

首次验证规则的注册和应用链注册（命令详见应用链管理-中继链模式管理文档）同时进行，即注册应用链时需要携带主验证规则信息，系统会将主规则注册绑定为该应用链的主验证规则，同时自动为该应用链注册所有默认规则。

关于 **默认验证规则**，目前我们提供如下三种默认验证规则（括号内为默认验证规则地址）：

- 无验证规则（0x00000000000000000000000000000000000000a2）：为所有类型应用链提供，该规则相当于验证引擎不对跨链请求进行验证
- fabric简单规则（0x00000000000000000000000000000000000000a1）：仅为`Fabric V1.4.3`和`Fabric V1.4.4`类型的应用链提供，该规则使用简单逻辑验证fabric链的跨链请求
- fabric复杂规则（0x00000000000000000000000000000000000000a0）：仅为`Fabric V1.4.3`和`Fabric V1.4.4`类型的应用链提供，该规则使用复杂逻辑验证fabric链的跨链请求


应用链注册成功后，应用链管理员可以继续为应用链注册其他规则，但中继链只是记录新规则，不会改变应用链的主验证规则。

### 2.2 使用方法

#### 第一步：应用链部署验证规则
#### pier rule deploy

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--path`：指定验证规则文件所在路径


#### 第二步：注册验证规则
#### pier rule register

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--appchain-id`：指定应用链id
- `--rule`：指定验证规则地址
- `--rule-url`：指定验证规则内容url地址，该url可供用户查看规则内容，系统不作检查，以下示例中以任意网址代替


### 2.3 示例说明

前提条件：

- bitxhub已经启动，使用默认`ZeroPermission`投票策略
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

# 查询应用链验证规则：指定的主规则已绑定，类型默认规则自动注册为可绑定状态
$ bitxhub --repo $bitxhub_node_repo client governance rule all --id appchain1
ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a0  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a1  bindable   false   1644992225287923000
appchain1  0x615bAa02f2751f3378c8c57F9eB084daD6A55a92  available  true    1644992225287923000

# 部署新的验证规则
$ pier --repo $pier_repo rule deploy --path $pier_repo/fabric/validating.wasm
Deploy rule successfully: 0x075214660D7021c071259Cc9dd69835A04491AC8

# 注册新的验证规则
$ pier --repo $pier_repo rule register --appchain-id appchain1 --rule 0x075214660D7021c071259Cc9dd69835A04491AC8 --rule-url "https://bitxhub.cn/"
Register rule to bitxhub for appchain appchain1 successfully.

# 查询应用链验证规则
$ bitxhub --repo $bitxhub_node_repo client governance rule all --id appchain1
ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a0  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a1  bindable   false   1644992225287923000
appchain1  0x615bAa02f2751f3378c8c57F9eB084daD6A55a92  available  true    1644992225287923000
appchain1  0x075214660D7021c071259Cc9dd69835A04491AC8  bindable   false   1644992395141307000
```

## 3 主验证规则切换
### 3.1 功能介绍
中继链上可能部署注册了应用链的多条验证规则（但只有一条主验证规则），应用链如果想更换主验证规则，可以使用主验证规则切换功能。  
切换主验证规则过程涉及到新旧两条主验证规则，新验证规则在切换之前必须是可绑定状态，切换过程中新旧主验证规则的状态转换如下：

- 新的主验证规则：`bindable` --> `binding` --> `available`
- 旧的主验证规则：`available` --> `unbinding` --> `bindable`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到规则绑定或解绑的过渡状态）_

### 3.2 使用方法

#### pier rule update

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--addr`：指定新的主验证规则地址
- `--appchain-id`：指定应用链id
- `--reason`：可选参数，说明切换原因

_（注意，验证规则切换需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 3.3 示例说明

前提条件：

- 接上文中2.3示例
```shell
# 查询应用链验证规则：主规则为 0x615bAa02f2751f3378c8c57F9eB084daD6A55a92
$ bitxhub --repo $bitxhub_node_repo client governance rule all --id appchain1
ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a0  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a1  bindable   false   1644992225287923000
appchain1  0x615bAa02f2751f3378c8c57F9eB084daD6A55a92  available  true    1644992225287923000
appchain1  0x075214660D7021c071259Cc9dd69835A04491AC8  bindable   false   1644992395141307000

# 切换验证规则
$ pier --repo $pier_repo rule update --addr 0x075214660D7021c071259Cc9dd69835A04491AC8 --appchain-id appchain1
Update master rule to bitxhub for appchain appchain1 successfully, wait for proposal 0x89cc4e498e073fe3A54524239226187f9Dc6414d-1 to finish.
# ZeroPermission策略下管理员投票可省略，即已切换成功

# 查询应用链验证规则：主规则为 0x075214660D7021c071259Cc9dd69835A04491AC8
$ bitxhub --repo $bitxhub_node_repo client governance rule all --id appchain1
ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a0  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a1  bindable   false   1644992225287923000
appchain1  0x615bAa02f2751f3378c8c57F9eB084daD6A55a92  bindable   false   1644992225287923000
appchain1  0x075214660D7021c071259Cc9dd69835A04491AC8  available  true    1644992395141307000
```

## 4 验证规则注销
### 4.1 功能介绍
应用链可以注销已经注册过的验证规则，但为保证应用链始终有一条可用的验证规则，主验证规则无法注销。  
注销过程中验证规则的状态转换如下：  
`bindable` --> `forbidden`

### 4.2 使用方法

#### pier rule logout

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--addr`：指定新的主验证规则地址
- `--appchain-id`：指定应用链id

### 4.3 示例说明

前提条件

- 接上文中3.3示例

```shell
# 查询应用链验证规则：主规则为 0x075214660D7021c071259Cc9dd69835A04491AC8，有4条可绑定规则
$ bitxhub --repo $bitxhub_node_repo client governance rule all --id appchain1
ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a0  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a1  bindable   false   1644992225287923000
appchain1  0x615bAa02f2751f3378c8c57F9eB084daD6A55a92  bindable   false   1644992225287923000
appchain1  0x075214660D7021c071259Cc9dd69835A04491AC8  available  true    1644992395141307000

# 注销验证规则
$ pier --repo $pier_repo rule logout --addr 0x615bAa02f2751f3378c8c57F9eB084daD6A55a92 --appchain-id appchain1
The logout request was submitted successfully

# 查询应用链验证规则：主规则为 0x075214660D7021c071259Cc9dd69835A04491AC8，有3条可绑定规则
$ bitxhub --repo $bitxhub_node_repo client governance rule all --id appchain1
ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a0  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a1  bindable   false   1644992225287923000
appchain1  0x615bAa02f2751f3378c8c57F9eB084daD6A55a92  forbidden  false   1644992225287923000
appchain1  0x075214660D7021c071259Cc9dd69835A04491AC8  available  true    1644992395141307000
```


## 5 其他功能
中继链还提供了其他查询验证规则信息的功能  
以下给出的示例说明的前提条件均为接上文4.3示例

### 5.1 查询应用链所有规则
示例说明：
```shell script
$ bitxhub --repo $bitxhub_node_repo client governance rule all --id appchain1
ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a0  bindable   false   1644992225287923000
appchain1  0x00000000000000000000000000000000000000a1  bindable   false   1644992225287923000
appchain1  0x615bAa02f2751f3378c8c57F9eB084daD6A55a92  forbidden  false   1644992225287923000
appchain1  0x075214660D7021c071259Cc9dd69835A04491AC8  available  true    1644992395141307000
```

### 5.2 查询应用链主验证规则
示例说明：
```shell script
$ bitxhub --repo $bitxhub_node_repo client governance rule master --id appchain1
available rule address is 0x075214660D7021c071259Cc9dd69835A04491AC8
```

### 5.3 查询应用链某条验证规则状态
示例说明：
```shell script
$ bitxhub --repo $bitxhub_node_repo client governance rule status --id appchain1 --addr 0x075214660D7021c071259Cc9dd69835A04491AC8
the rule 0x075214660D7021c071259Cc9dd69835A04491AC8 is available
```