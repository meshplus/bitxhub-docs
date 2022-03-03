# 身份管理
中继链提供了关于中继链上身份的管理功能

## 1 概述
中继链平台上可能出现的身份有三种：中继链治理管理员、中继链审计管理员、应用链管理员
- `中继链治理管理员`：参与中继链上的投票治理，也可以发起中继链上的各种治理操作
- `中继链审计管理员`：不参与中继链上的投票治理，与nvpNode绑定并对节点上同步的数据进行审计。审计管理员在中继链上的直接管理功能除了查询信息外只有更新绑定审计节点权限
- `应用链管理员`：应用链上的管理员，中继链并不直接对应用链管理员进行管理，仅做身份的记录。应用链管理员在中继链上的直接管理功能主要在应用链管理员和验证规则管理模块

其中中继链治理管理员又可以分为超级治理管理员和普通治理管理员，中继链初始状态至少有一个超级管理员，超级管理员后续不可以注册、冻结或注销，一些高优先级的提案（比如与管理员相关的提案、与冻结或注销之类会造成严峻后果的提案）需要超级管理员投票才能生效

中继链的身份管理功能主要提供关于中继链管理员的注册、冻结、解冻、注销功能，关于审计管理员的注册、绑定审计节点、注销功能。

## 2 身份注册
### 2.1 功能介绍
中继链管理员可以向中继链上注册治理管理员或审计管理员，需要注意的是注册审计管理员时需要绑定一个已经存在的审计节点（即需要现在注册审计节点再注册审计管理员）。注册过程中管理员的状态转换如下：  

`unavailable` --> `registering` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 2.2 使用方法

#### bitxhub client governance role register

参数解释：
- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--address`：指定新管理员的地址
- `--type`：指定新管理员的类型，默认为治理管理员
- `--nodeAccount`：仅在注册审计管理员时生效，指定初始绑定节点account
- `--reason`：可选参数，说明注册理由

_（注意，注册管理员需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 2.3 示例说明

#### 2.3.1 注册治理管理员示例

前提条件：
- bitxhub已经启动，使用默认`ZeroPermission`投票策略

```shell
# 查询所有管理员：四个超级管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 注册中继链管理员
$ bitxhub --repo $bitxhub_node_repo client governance role register --address 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F --type governanceAdmin
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 查询所有管理员：四个超级管理员，一个新注册的治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  governanceAdmin       available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
```

#### 2.3.2 注册审计管理员示例

注册审计管理员时需要初始绑定一个审计节点，故需要先注册审计节点  
注册审计节点是审计节点的审计权限不可以为空，故需要先注册一条应用链

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

# 注册审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node register --account 0x96B5619A637639E1651EADD49CDA5B8EF4882331 --type nvpNode --name node1 --permission appchain1
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 查询所有管理员：四个超级管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 注册审计管理员
$ bitxhub --repo $bitxhub_node_repo client governance role register --address 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F --type auditAdmin --nodeAccount 0x96B5619A637639E1651EADD49CDA5B8EF4882331
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 查询所有管理员：四个超级管理员，一个新注册的审计管理员，一个应用链管理员（注册应用链时注册）
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x89cc4e498e073fe3A54524239226187f9Dc6414d  appchainAdmin         available                                              appchain1
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  auditAdmin            available  0x96B5619A637639E1651EADD49CDA5B8EF4882331
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
```

## 3 治理管理员冻结及激活

### 3.1 功能介绍

**冻结**：中继链治理管理员可以对其他治理管理员进行冻结，管理员身份被冻结后将无法正常工作（参与投票或发起治理操作），但治理管理员不可以冻结自己  

冻结过程的状态转换如下：    
`available` --> `freezinging` --> `frozen`  
_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

**激活**：治理管理员被冻结后可以由其他治理管理员或自己发起解冻请求，解冻成功后管理员即可恢复正常

激活过程的状态转换如下：    
`frozen` --> `activating` --> `available`  
_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

超级治理管理员不可以被冻结或激活

### 3.2 使用方法

#### 冻结治理管理员
#### bitxhub client governance role freeze

参数解释：
- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待冻结治理管理员的地址
- `--reason`：可选参数，说明冻结理由

_（注意，冻结治理管理员需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

#### 解冻治理管理员
#### bitxhub client governance role activate

参数解释：
- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待解冻治理管理员的地址
- `--reason`：可选参数，说明冻结理由

_（注意，解冻治理管理员需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 3.3 示例说明

前提条件：
- 接2.3.1示例
```shell
# 查询所有管理员：四个超级管理员，一个可用的普通治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  governanceAdmin       available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 冻结治理管理员 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
$ bitxhub --repo $bitxhub_node_repo client governance role role freeze --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
# ZeroPermission策略下管理员投票可省略，即已冻结成功

# 查询所有管理员：四个超级管理员，一个被冻结的普通治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  governanceAdmin       frozen
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 解冻治理管理员 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
$ bitxhub --repo $bitxhub_node_repo client governance role role activate --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
# ZeroPermission策略下管理员投票可省略，即已解冻成功

# 查询所有管理员：四个超级管理员，一个可用的普通治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  governanceAdmin       available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
```

## 4 审计管理员绑定节点

### 4.1 功能介绍

中继链治理管理员可以给已冻结的审计管理员绑定审计节点。  

审计管理员注册时需要绑定一个可用的审计节点，如果绑定的审计节点出现问题（如注销）审计管理员就会被系统自动冻结，此时如果想要恢复审计管理员，就需要给审计管理员重新绑定一个审计节点。

绑定过程的审计管理员状态转换如下：  
`frozen` --> `binding` --> `available`    
绑定过程中审计节点状态转换如下：  
`available` --> `binding` --> `binded`   
_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 4.2 使用方法

#### bitxhub client governance role bind

参数解释：
- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待绑定审计管理员的地址
- `--account`：指定待绑定审计节点的account
- `--reason`：可选参数，说明绑定理由

_（注意，绑定审计管理员需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 4.3 示例说明

前提条件：
- 接2.3.2示例
```shell
# 查询所有管理员：四个超级管理员，一个可用的审计管理员，一个应用链管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x89cc4e498e073fe3A54524239226187f9Dc6414d  appchainAdmin         available                                              appchain1
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  auditAdmin            available  0x96B5619A637639E1651EADD49CDA5B8EF4882331
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 查询所有节点：四个共识节点，一个已绑定的审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node1  appchain1   binded     0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available

# 注销审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node logout --account 0x96B5619A637639E1651EADD49CDA5B8EF4882331
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
# ZeroPermission策略下管理员投票可省略，即已注销成功

# 查询所有管理员：四个超级管理员，一个已冻结的审计管理员，一个应用链管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x89cc4e498e073fe3A54524239226187f9Dc6414d  appchainAdmin         available                                              appchain1
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  auditAdmin            frozen     0x96B5619A637639E1651EADD49CDA5B8EF4882331
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 注册新的审计节点
$ bitxhub --repo $bitxhub_node_repo client governance node register --account 0x96B5619A637639E1651EADD49CDA5B8EF4882332 --type nvpNode --name node2 --permission appchain1
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 审计管理员绑定审计节点
$ bitxhub --repo $bitxhub_node_repo client governance role bind --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F --account 0x96B5619A637639E1651EADD49CDA5B8EF4882332
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-4
# ZeroPermission策略下管理员投票可省略，即已绑定成功

# 查询所有管理员：四个超级管理员，一个可用的审计管理员，一个应用链管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x89cc4e498e073fe3A54524239226187f9Dc6414d  appchainAdmin         available                                              appchain1
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  auditAdmin            available  0x96B5619A637639E1651EADD49CDA5B8EF4882332
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
```

## 5 身份注销
### 5.1 功能介绍

中继链管理员可以注销普通治理管理员或审计管理员，身份注销是不可逆操作。

注销审计管理员时，如果审计管理员已经绑定了审计节点，那么审计管理员注销成功后，审计节点后自动解绑。

注销过程的状态转换如下：    
`*` --> `logouting` --> `forbidden`  
注销是一种高优先级的提案，即管理员身份注册成功后处于任意状态时都可以被注销，故状态转换的起始状态是不确定的  
_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 5.2 使用方法

#### bitxhub client governance role logout

参数解释：
- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待注销管理员的地址
- `--reason`：可选参数，说明注销理由

_（注意，注销管理员需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 5.3 示例说明

#### 5.3.1 注销治理管理员示例

前提条件：
- 接3.3示例
```shell
# 查询所有管理员：四个超级管理员，一个可用的普通治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  governanceAdmin       available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 注销普通治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role logout --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3

# 查询所有管理员：四个超级管理员，一个已注销的普通治理管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  governanceAdmin       forbidden
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
```

#### 5.3.2 注销审计管理员示例

前提条件
- 接4.3示例
```shell
# 查询所有管理员：四个超级管理员，一个可用的审计管理员，一个应用链管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x89cc4e498e073fe3A54524239226187f9Dc6414d  appchainAdmin         available                                              appchain1
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  auditAdmin            available  0x96B5619A637639E1651EADD49CDA5B8EF4882332
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 查询所有节点：四个共识节点，一个已绑定的审计节点，一个已注销的审计节点（可忽略）
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node1  appchain1   forbidden  0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
0x96B5619A637639E1651EADD49CDA5B8EF4882332  nvpNode                                                  0         node2  appchain1   binded     0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available

# 注销审计管理员
$ bitxhub --repo $bitxhub_node_repo client governance role logout --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-5
# ZeroPermission策略下管理员投票可省略，即已注销成功

# 查询所有管理员：四个超级管理员，一个已注销的审计管理员，一个应用链管理员
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x89cc4e498e073fe3A54524239226187f9Dc6414d  appchainAdmin         available                                              appchain1
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  auditAdmin            forbidden  0x96B5619A637639E1651EADD49CDA5B8EF4882332
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available

# 查询所有节点：四个共识节点，一个未绑定的审计节点，一个已注销的审计节点（可忽略）
$ bitxhub --repo $bitxhub_node_repo client governance node all
Account                                     Type     Pid                                             VpNodeId  Name   Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----   ----------  ------     --------------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                            available
0x96B5619A637639E1651EADD49CDA5B8EF4882331  nvpNode                                                  0         node1  appchain1   forbidden  0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
0x96B5619A637639E1651EADD49CDA5B8EF4882332  nvpNode                                                  0         node2  appchain1   available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                            available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                            available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                            available
```



## 6 其他功能

中继链还提供了其他查询身份信息的功能
以下给出的示例说明的前提条件均为接上文5.3.2示例

### 6.1 查询所有身份
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance role all
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x89cc4e498e073fe3A54524239226187f9Dc6414d  appchainAdmin         available                                              appchain1
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F  auditAdmin            forbidden  0x96B5619A637639E1651EADD49CDA5B8EF4882332
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
```

### 6.2 查询某个身份的状态
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance role status --id 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F
role 0x9E1D8be61dee418B83A47BE54a1777ca70e10E0F is forbidden
```