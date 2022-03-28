# DApp管理
中继链提供了关于中继链上DApp的管理功能

## 1 概述
中继链支持任意用户在链上部署自己的DApp应用（本质为合约）的功能，用户部署DApp后可以通过治理来实现对DApp的注册、更新、冻结、激活、转让、评价等功能。

## 2 DApp注册
### 2.1 功能介绍
任意用户可以向中继链上注册DApp。注册过程中DApp的状态转换如下：

`unavailable` --> `registering` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 2.2 使用方法

#### bitxhub client governance dapp register

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--name value`:           指定dapp名称，可随意自定义，但不可以与其他dapp重名
- `--type value`:           指定dapp类型，目前支持`tool`, `application`, `game` 和 `others` 四种类型
- `--desc value`:           指定dapp描述信息
- `--url value`:            指定dapp内容url地址，该url可能为dapp的使用链接，系统不作检查，以下示例中以任意网址代替
- `--contractAddrs value`:  指定dapp合约地址，多个合约地址用`,`分隔
- `--permission value`:     可选参数，指定dapp用户地址黑名单，即不允许哪些用户看到当前dapp（仅支持治理层面的可用性），多个用户地址用`,`隔开，可以为空
- `--reason value`:         可选参数，说明注册理由

_（注意，注册DApp需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 2.3 示例说明

前提条件：

- bitxhub已经启动，使用默认`ZeroPermission`投票策略
- bitxhub已经部署好dapp的合约，合约地址为`0x615bAa02f2751f3378c8c57F9eB084daD6A55a92`

```shell
# 查询所有DApp：无
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
Id  Name  Type  Owner  Createtime  Score  Status  TranRec  EvaRec
--  ----  ----  -----  ----------  -----  ------  -------  ------

# 注册DApp
$ bitxhub --repo $bitxhub_node_repo client governance dapp register --name 示例应用 --type tool --desc 这是一个dapp使用示例 --url "https://bitxhub.cn/" --contractAddrs 0x615bAa02f2751f3378c8c57F9eB084daD6A55a92
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0, dapp id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
# ZeroPermission策略下管理员投票可省略，即已注册成功

# 查询所有dapp：一个新注册的dapp
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
Id                                            Name  Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----  ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  示例应用  tool  0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  1648199110148480000  0      available
```

## 3 DApp更新

### 3.1 功能介绍
DApp所有者可以对自己拥有的dapp信息进行更新，其中，更新`desc`是不需要投票治理的，更新`name`，`url`，`contractAddrs`及`permission`信息在非`ZeroPermission`的策略情况下需要治理管理员投票治理
   
需要治理的更新过程中DApp的状态转换如下：

`available` --> `updating` --> `available`

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 3.2 使用方法

#### bitxhub client governance dapp update

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待更新DApp的ID，该ID由系统生成，一般为「该DApp的注册用户地址」拼接「该注册用户注册DApp的个数」而成
- `--name value`:           可选参数，指定dapp名称，可随意自定义，但不可以与其他dapp重名
- `--desc value`:           可选参数，指定dapp描述信息
- `--url value`:            可选参数，指定dapp内容url地址，该url可能为dapp的使用链接，系统不作检查，以下示例中以任意网址代替
- `--contractAddrs value`:  可选参数，指定dapp合约地址，多个合约地址用`,`分隔
- `--permission value`:     可选参数，指定dapp用户地址黑名单，即不允许哪些用户看到当前dapp（仅支持治理层面的可用性），多个用户地址用`,`隔开，可以为空
- `--reason value`:         可选参数，说明更新理由

_（注意，更新DApp可能需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 3.3 示例说明

前提条件：

- 接2.3示例

```shell
# 查询当前拥有DApp：一个名为示例应用的dapp
$ bitxhub --repo $bitxhub_node_repo client governance dapp myDapps --addr 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013
========================================================================================
Id                                            Name  Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----  ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  示例应用  tool  0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  1648199110148480000  0      available

# 更新DApp
$ bitxhub --repo $bitxhub_node_repo client governance dapp update --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0 --name 示例应用2
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
# ZeroPermission策略下管理员投票可省略，即已更新成功

# 查询当前拥有DApp：dapp已经更名为示例应用2
$ bitxhub --repo $bitxhub_node_repo client governance dapp myDapps --addr 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013
========================================================================================
Id                                            Name  Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----  ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  示例应用2  tool  0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  1648199110148480000  0      available
```

## 4 DApp冻结及解冻

### 4.1 功能介绍

**冻结**：中继链治理管理员可以对除自己所有DApp以外的任意DApp进行冻结（不可以冻结自己的DApp），DApp冻结后不可转让且不可用（仅表示治理浏览器层面的不可用）

冻结过程的状态转换如下：    

`available` --> `freezinging` --> `frozen`  

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

**解冻**：中继链治理管理员及DApp所有者可以对DApp发起解冻请求，解冻成功后DApp即可恢复正常

解冻过程的状态转换如下：    

`frozen` --> `activating` --> `available`  

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 4.2 使用方法

#### 冻结DApp
#### bitxhub client governance dapp freeze

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待冻结DApp的ID，该ID由系统生成，一般为「该DApp的注册用户地址」拼接「该注册用户注册DApp的个数」而成
- `--reason`：可选参数，说明冻结理由

_（注意，冻结DApp需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

#### 解冻DApp
#### bitxhub client governance dapp activate

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待解冻DApp的ID，该ID由系统生成，一般为「该DApp的注册用户地址」拼接「该注册用户注册DApp的个数」而成
- `--reason`：可选参数，说明冻结理由

_（注意，解冻DApp需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 4.3 示例说明

前提条件：

- 接3.3示例
```shell
# 查询所有DApp：一个可用的DApp
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                            Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  示例应用2  tool  0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  1648199110148480000  0      available

# 冻结DApp 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
$ bitxhub --repo $bitxhub_node2_repo client governance dapp freeze --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
proposal id is 0x79a1215469FaB6f9c63c1816b45183AD3624bE34-0
# ZeroPermission策略下管理员投票可省略，即已冻结成功

# 查询所有DApp：一个被冻结的DApp
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                            Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  示例应用2  tool  0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  1648199110148480000  0      frozen

# 解冻DApp 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
$ bitxhub --repo $bitxhub_node_repo client governance dapp activate --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
# ZeroPermission策略下管理员投票可省略，即已解冻成功

# 查询所有DApp：一个可用的DApp
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                            Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  示例应用2  tool  0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  1648199110148480000  0      available
```

## 5 DApp转让

### 5.1 功能介绍

DApp所有者可以将自己可用的DApp转让给其他用户

转让过程的DApp状态转换如下，其中`transferring`状态不影响DApp的治理可用性：  
`available` --> `transferring` --> `available`    

_（注意，如果使用`ZeroPermission`投票策略，无法明显看到中间过渡状态）_

### 5.2 使用方法

#### bitxhub client governance dapp transfer

参数解释：

- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待转让DApp的ID，该ID由系统生成，一般为「该DApp的注册用户地址」拼接「该注册用户注册DApp的个数」而成
- `--addr`：指定待接收DApp用户的地址
- `--reason`：可选参数，说明转让理由

_（注意，转让DApp需要投票治理流程，如果使用`ZeroPermission`投票策略，投票过程可省略）_

### 5.3 示例说明

前提条件：

- 接4.3示例
```shell
# 查询所有DApp：一个 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013 用户所有的DApp
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                            Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0  示例应用2  tool  0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  1648199110148480000  0      available

# 转让DApp：转让给 0x79a1215469FaB6f9c63c1816b45183AD3624bE34 用户
$ bitxhub --repo $bitxhub_node_repo client governance dapp transfer --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0 --addr 0x79a1215469FaB6f9c63c1816b45183AD3624bE34
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
# ZeroPermission策略下管理员投票可省略，即已转让成功

# 查询所有DApp：一个 0x79a1215469FaB6f9c63c1816b45183AD3624bE34 用户所有的DApp
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                                                                                                    Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                                                                                                    ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0                                                                          示例应用2  tool  0x79a1215469FaB6f9c63c1816b45183AD3624bE34  1648199110148480000  0      available
0x79a1215469FaB6f9c63c1816b45183AD3624bE34, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, , 1648201053750218000, false
# 可以看到该dapp增加了一条转让记录
```

## 6 DApp评价
### 6.1 功能介绍

任意用户可以DApp进行评价，但用户不可以重复评价，评价由评分和评价内容两个部分组成，系统将综合DApp的所有历史评分给DApp打出一个平均分，初始为0。
评价过程不需要投票治理

### 6.2 使用方法

#### bitxhub client governance dapp evaluate

参数解释：
- `--repo`：指定中继链管理员私钥的路径，通常为bitxhub任意节点配置文件所在目录
- `--id`：指定待评价DApp的ID，该ID由系统生成，一般为「该DApp的注册用户地址」拼接「该注册用户注册DApp的个数」而成
- `--desc`: 说明评价内容
- `--score`: 可选参数，评分，在[0,5]区间内，默认为0

### 6.3 示例说明

前提条件：
- 接5.3示例
```shell
# 查询所有DApp：一个评分为0的DApp，初始评分为0
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                                                                                                    Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                                                                                                    ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0                                                                          示例应用2  tool  0x79a1215469FaB6f9c63c1816b45183AD3624bE34  1648199110148480000  0      available
0x79a1215469FaB6f9c63c1816b45183AD3624bE34, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, , 1648201053750218000, false

# 评分5
$ bitxhub --repo $bitxhub_node_repo client governance dapp evaluate --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0 --desc 这是一个非常好的DApp --score 5
evaluate dapp success

# 查询所有DApp：一个评分为0的DApp，初始评分为0
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                                                                                                    Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                                                                                                    ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0                                                                          示例应用2  tool  0x79a1215469FaB6f9c63c1816b45183AD3624bE34  1648199110148480000  5      available
0x79a1215469FaB6f9c63c1816b45183AD3624bE34, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, , 1648201053750218000, false
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 这是一个非常好的DApp, 1648201937767840000, 5
# 可以看到该DApp增加了一条评价记录
```

## 7 其他功能

中继链还提供了其他查询DApp信息的功能
以下给出的示例说明的前提条件均为接上文6.3示例

### 7.1 查询所有DApp
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance dapp all
========================================================================================
Id                                                                                                                    Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                                                                                                    ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0                                                                          示例应用2  tool  0x79a1215469FaB6f9c63c1816b45183AD3624bE34  1648199110148480000  5      available
0x79a1215469FaB6f9c63c1816b45183AD3624bE34, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, , 1648201053750218000, false
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 这是一个非常好的DApp, 1648201937767840000, 5
```

### 7.2 查询某个DApp的状态
示例说明：
```shell
$ bitxhub --repo $bitxhub_node_repo client governance dapp status --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
dapp 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0 is available
```

### 7.3 查询用户可见的DApp
这里的可见针对DApp的黑名单而言，及治理层面的可见性，示例中的DApp黑名单为空
```shell
$ bitxhub --repo $bitxhub_node_repo client governance dapp permission --caller 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013
========================================================================================
Id                                                                                                                    Name   Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                                                                                                    ----   ----  -----                                       ----------           -----  ------     -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0                                                                          示例应用2  tool  0x79a1215469FaB6f9c63c1816b45183AD3624bE34  1648199110148480000  5      available
0x79a1215469FaB6f9c63c1816b45183AD3624bE34, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, , 1648201053750218000, false
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 这是一个非常好的DApp, 1648201937767840000, 5
```

### 7.4 查询用户所有的DApp
这里可以查询到某个用户曾经及现在拥有的所有dapp，如果是曾经用户的状态为`transferred`，由于在上述示例中该用户已经将这个dapp转让给他人，故这里可以看到dapp的状态为`transferred`

```shell
$ bitxhub --repo $bitxhub_node_repo client governance dapp myDapps --addr 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013
========================================================================================
Id                                                                                                                    Name   Type  Owner                                       Createtime           Score  Status       TranRec  EvaRec
--                                                                                                                    ----   ----  -----                                       ----------           -----  ------       -------  ------
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0                                                                          示例应用2  tool  0x79a1215469FaB6f9c63c1816b45183AD3624bE34  1648199110148480000  5      transferred
0x79a1215469FaB6f9c63c1816b45183AD3624bE34, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, , 1648201053750218000, false
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013, 这是一个非常好的DApp, 1648201937767840000, 5
```