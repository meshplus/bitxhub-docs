# 服务管理
中继链提供了关于应用链上服务的管理功能

## 1 概述
目前中继链的跨链单元就是基于服务的，只有中继链上可用状态的服务才可以参与跨链。

应用链管理员可以将自己链上的服务信息注册到中继链上，由中继链治理管理员和应用链管理员共同实现对服务的注册、更新、冻结、激活、评价、注销等治理功能。

## 2 服务注册
### 2.1 功能介绍
应用链管理员可以向中继链上注册自己的链上服务信息。注册过程中服务的状态转换如下：

`unavailable` --> `registering` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 2.2 使用方法

#### pier appchain service register

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--appchain-id value`：指定应用链id
- `--service-id value`   指定服务id（即服务合约地址，这是部署在应用链上的合约，系统无法检查，但如果合约地址错误，跨链调用时会报错）
- `--name value`         指定服务名称，不可以与其他服务重名
- `--intro value`        指定服务介绍信息
- `--type value`         可选参数，指定服务类型，目前仅支持`CallContract`类型，默认`CallContract`类型
- `--ordered`            可选参数，指定服务是否需要按序调用，默认不按序调用
- `--permit value`       可选参数，指定服务调用地址黑名单，即不允许哪些用户跨链调用当前服务，多个用户地址用`,`隔开，可以为空
- `--details value`      指定服务详情，可能包括服务接口调用方式等详细信息
- `--reason value`       可选参数，说明注册理由

_（注意，注册服务需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 2.3 示例说明

前提条件：

- bitxhub已经启动，使用默认`ZeroPermission`投票策略
- bitxhub上已经注册好了一条名称为"应用链1"的应用链

```shell
# 查询已注册应用链信息
$ bitxhub --repo $bitxhub_node_repo client governance chain info --name 应用链1
Id         Name  Type           Broker                                                                   Status     Desc     Version
--         ----  ----           ------                                                                   ------     ----     -------
appchain1  应用链1  Fabric V1.4.3  {"channel_id":"mychannel","chaincode_id":"broker","broker_version":"1"}  available  这是一条应用链  0

# 查询应用链1上的所有服务：无服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------

# 注册服务
$ pier --repo $pier_repo appchain service register --appchain-id appchain1 --service-id "mychannel&transfer" --name 服务1 --intro "这是应用链1上的一个服务" --details "这里可能有服务的接口文档、调用方法等详细信息"
Register appchain service for appchain1:mychannel&transfer successfully, wait for proposal 0x89cc4e498e073fe3A54524239226187f9Dc6414d-1 to finish.
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 查询应用链1上的所有服务：一个新注册的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务1   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available
```

## 3 服务更新

### 3.1 功能介绍
应用链管理员可以对自己注册的服务信息进行更新，其中，更新`permit`和`intro`是不需要投票治理的，更新`name`和`details`信息在非`ZeroPermission`的策略情况下需要治理管理员投票治理

需要治理的更新过程中服务的状态转换如下：

`available` --> `updating` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 3.2 使用方法

#### pier appchain service update

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--appchain-id value`  指定待更新服务所在应用链id
- `--service-id value`   指定待更新服务id
- `--name value`         可选参数，指定服务名称，不可以与其他服务重名
- `--intro value`        可选参数，指定服务介绍信息
- `--permit value`       可选参数，指定服务调用地址黑名单，即不允许哪些用户跨链调用当前服务，多个用户地址用`,`隔开，可以为空
- `--details value`      可选参数，指定服务详情，可能包括服务接口调用方式等详细信息
- `--reason value`       可选参数，说明更新理由

- _（注意，更新服务可能需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 3.3 示例说明

前提条件：

- 接2.3示例

```shell
# 查询应用链1上的所有服务：一个名为"服务1"的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务1   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available

# 更新服务
$ pier --repo $pier_repo appchain service update --appchain-id appchain1 --service-id "mychannel&transfer" --name 服务2
Update appchain service for appchain1:mychannel&transfer successfully, wait for proposal 0x89cc4e498e073fe3A54524239226187f9Dc6414d-2 to finish.
# ZeroPermission策略下管理员投票可省略，即已更新成功

# 查询应用链1上的所有服务：一个名为"服务1"的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available
```

## 4 服务冻结及解冻

### 4.1 功能介绍

**冻结**：中继链治理管理员可以对服务进行冻结操作，服务冻结不可以进行跨链调用

冻结过程的状态转换如下：

`available` --> `freezinging` --> `frozen`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

**解冻**：中继链治理管理员及服务对应的应用链管理员可以对已冻结的服务发起解冻请求，解冻成功后服务即可恢复可用性

解冻过程的状态转换如下：

`frozen` --> `activating` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 4.2 使用方法

#### 冻结DApp
#### bitxhub client governance service freeze

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待冻结服务的chainServiceID，即chainID冒号拼接serviceID，如：`appchain1:mychannel&transfer`
- `--reason`：可选参数，说明冻结理由

_（注意，冻结服务需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

#### 中继链管理员解冻服务
#### bitxhub client governance service activate

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待解冻服务的chainServiceID，即chainID冒号拼接serviceID，如：`appchain1:mychannel&transfer`
- `--reason`：可选参数，说明冻结理由

_（注意，解冻服务需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

#### 应用链管理员解冻服务
#### pier appchain service activate

参数解释：
- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--appchain-id value`  指定待解冻服务所在应用链id
- `--service-id value`   指定待解冻服务id
- `--reason`：可选参数，说明解冻理由

_（注意，解冻服务需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_


### 4.3 示例说明

前提条件：

- 接3.3示例
```shell
# 查询应用链1上的所有服务：一个可用的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available

# 中继链管理员冻结服务
$ bitxhub --repo $bitxhub_node_repo client governance service freeze --id "appchain1:mychannel&transfer"
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
# ZeroPermission策略下管理员投票可省略，即已冻结成功

# 查询应用链1上的所有服务：一个已冻结的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available

# 应用链管理员解冻服务
$ pier --repo $pier_repo appchain service activate --appchain-id appchain1 --service-id "mychannel&transfer"
Activate appchain service for mychannel&transfer successfully, wait for proposal 0x89cc4e498e073fe3A54524239226187f9Dc6414d-3 to finish.
# ZeroPermission策略下管理员投票可省略，即已解冻成功


# 查询应用链1上的所有服务：一个可用服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available
```

## 5 服务评价
### 5.1 功能介绍

任意用户可以对服务进行评价，但用户不可以重复评价，评价由评分和评价内容两个部分组成，系统将综合服务的所有历史评分给服务打出一个平均分，初始为0。
评价过程不需要投票治理

### 5.2 使用方法

#### bitxhub client governance service evaluate

参数解释：
- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待评价服务的chainServiceID，即chainID冒号拼接serviceID，如：`appchain1:mychannel&transfer`
- `--desc`: 说明评价内容
- `--score`: 可选参数，评分，在[0,5]区间内，默认为0

### 5.3 示例说明

前提条件：
- 接5.3示例
```shell
# 查询应用链1上的所有服务：一个初始评分为0的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available

# 评分5
$ bitxhub --repo $bitxhub_node_repo client governance service evaluate --id "appchain1:mychannel&transfer" --desc "这是一个很好的服务" --score 5
evaluate service success

# 查询应用链1上的所有服务：一个平均评分为5的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  5      available
```

## 6 服务注销

### 6.1 功能介绍
应用链管理员可以对自己注册的服务信息进行注销，服务注销后不可以再参与跨链，且注销为不可逆操作

注销成功过程中服务的状态转换如下：

`*` --> `logouting` --> `forbidden`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 6.2 使用方法

#### pier appchain service update

参数解释：

- `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录
- `--appchain-id value`  指定待注销服务所在应用链id
- `--service-id value`   指定待注销服务id
- `--reason value`       可选参数，说明注销理由

- _（注意，注销服务可能需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 6.3 示例说明

前提条件：

- 接5.3示例

```shell
# 查询应用链1上的所有服务：一个可用服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      available

# 注销服务
$ pier --repo $pier_repo appchain service logout --appchain-id appchain1 --service-id "mychannel&transfer"
Logout appchain service for mychannel&transfer successfully, wait for proposal 0x89cc4e498e073fe3A54524239226187f9Dc6414d-4 to finish.
# ZeroPermission策略下管理员投票可省略，即已注销成功

# 查询应用链1上的所有服务：一个已注销的服务
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      forbidden
```

## 7 其他功能

中继链还提供了其他查询服务信息的功能
以下给出的示例说明的前提条件均为接上文6.3示例

### 7.1 查询指定应用链上所有服务
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance service appServices --chainID appchain1
========================================================================================
ChainID    ServiceID           Name  Type          Intro         Ordered  Createtime           Score  Status
-------    ---------           ----  ----          -----         -------  ----------           -----  ------
appchain1  mychannel&transfer  服务2   CallContract  这是应用链1上的一个服务  false    1648208352624895000  0      forbidden
```

### 7.2 查询某个服务的状态
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance service status --id "appchain1:mychannel&transfer"
service appchain1:mychannel&transfer is forbidden
```