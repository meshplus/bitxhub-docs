## 1.1. 应用链管理

### 1.1.1. 常量定义

**应用链类型：chainType**

|                          |                                                      |
| ------------------------ | ---------------------------------------------------- |
| **常量值**               | **常量说明**                                         |
| **"Fabric V1.4.3"**      | Fabric V1.4.3                                        |
| **"Fabric  V1.4.4"**     | Fabric V1.4.4                                        |
| **"Hyperchain V1.8.3"**  | Hyperchain V1.8.3                                    |
| **"Hyperchain  V1.8.6"** | Hyperchain V1.8.6                                    |
| **"Flato V1.0.0"**       | Flato V1.0.0                                         |
| **"Flato  V1.0.3"**      | Flato V1.0.3                                         |
| **"Flato V1.0.6"**       | Flato V1.0.6                                         |
| **"BCOS  V2.6.0"**       | BCOS V2.6.0                                          |
| **"CITA V20.2.2"**       | CITA V20.2.2                                         |
| **"ETH"**                | ETH                                                  |
| **自定义字符串**         | 其他，应用链类型选择其他时需要用户自行输入应用链类型 |

 

**应用链事件类型：EventType**

|                |              |
| -------------- | ------------ |
| **常量值**     | **常量说明** |
| **"register"** | 注册应用链   |
| **"update"**   | 更新应用链   |
| **"freeze"**   | 冻结应用链   |
| **"activate"** | 解冻应用链   |
| **"logout"**   | 注销应用链   |

 

### 1.1.2.  应用链注册接口

#### 1.1.2.1. 接口描述

RegisterAppchain：提供应用链注册的内部接口，返回提案ID用于中继链管理员审核。

 

#### 1.1.2.2. 请求参数

| 参数           | 类型   | 示例值                                       | 描述                                                         |
| -------------- | ------ | -------------------------------------------- | ------------------------------------------------------------ |
| chainID        | String | Appchain1                                    | 应用链ID                                                     |
| chainName      | String | Fabric                                       | 应用链名称                                                   |
| chainType      | String | FABRIC V1.4.4                                | 应用链描述                                                   |
| trustRoot      | []byte | nil                                          | 应用链信任根                                                 |
| broker         | String | 0x0df6746854682fc8c  e5A7729BBB14c7652ae4516 | 应用链broker合约地址，fabric类型应用链为如下结构的json字符串 |
| desc           | String | test fabric                                  | 应用链简介                                                   |
| masterRuleAddr | String | 0x173b4f47fd11d3f0EC480  27C1F166D4Ae6b54BaB | 主验证规则地址                                               |
| masterRuleUrl  | String | https://bitxhub.cn/                          | 主验证规则url                                                |
| adminAddrs     | String | 0x4d7…，0x42b…                               | 应用链管理员地址列表，逗号隔开                               |
| reason         | String | Register fabric                              | 申请理由                                                     |

如果主验证规则为默认验证规则，masterRuleUrl传为空字符串。

 

**FabricBroker**:

| 参数          | 类型   | 示例值    | 描述           |
| ------------- | ------ | --------- | -------------- |
| ChannelID     | String | mychannel | 通道ID         |
| ChaincodeID   | String | broker    | 链码ID         |
| BrokerVersion | String | V1.0      | Broker合约版本 |

 

#### 1.1.2.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.1.2.4. 示例

```go
// =========== RegisterAppchain registers appchain info, 
// returns proposal id and error
func (am *AppchainManager) RegisterAppchain(chainID string,
                                            
	chainName string, 
                                            
	chainType string, 
                                            
	rustRoot []byte, 
                                            
	broker string, 
                                            
	desc, masterRuleAddr, masterRuleUrl, adminAddrs, reason string) *boltvm.Response {

```



### 1.1.3.  应用链更新接口

#### 1.1.3.1. 接口描述

UpdateAppchain：提供更新应用链的内部接口，返回提案ID用于中继链管理员审核。

#### 1.1.3.2. 请求参数

| 参数       | 类型   | 示例值         | 描述                           |
| ---------- | ------ | -------------- | ------------------------------ |
| id         | String | Appchain1      | 应用链ID                       |
| name       | String | Fabric         | 应用链名称                     |
| desc       | String | Fabric V1.4.4  | 应用链描述                     |
| trustRoot  | []byte | nil            | 应用链信任根                   |
| adminAddrs | String | 0x4d7…，0x42b… | 应用链管理员地址列表，逗号隔开 |
| reason     | String | update fabric  | 申请理由                       |

 

#### 1.1.3.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.1.3.4. 示例

```go
func (am *AppchainManager) UpdateAppchain(

   id,

   name,

   desc string,

   trustRoot []byte,

   adminAddrs,

   reason string) *boltvm.Response {
```

 

### 1.1.4.  应用链冻结接口

#### 1.1.4.1. 接口描述

FreezeAppchain：提供冻结应用链的内部接口，用于管理员冻结相关应用链。返回提案ID用于中继链管理员审核。

#### 1.1.4.2. 请求参数

| 参数   | 类型   | 示例值          | 描述     |
| ------ | ------ | --------------- | -------- |
| id     | String | Appchain1       | 应用链ID |
| reason | String | Freeze appchain | 申请理由 |

 

#### 1.1.4.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.1.4.4. 示例

```go
func (am *AppchainManager) FreezeAppchain(id,

   reason string) *boltvm.Response { 
```



### 1.1.5.  应用链激活接口

#### 1.1.5.1. 接口描述

ActivateAppchain：提供激活应用链的内部接口，用于管理员激活相关应用链。返回提案ID用于中继链管理员审核。

#### 1.1.5.2. 请求参数

| 参数   | 类型   | 示例值            | 描述     |
| ------ | ------ | ----------------- | -------- |
| id     | String | Appchain1         | 应用链ID |
| reason | String | Activate appchain | 申请理由 |

 

#### 1.1.5.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.1.5.4. 示例

```go
func (am *AppchainManager) ActivateAppchain(id,

   reason string) *boltvm.Response { 
```



### 1.1.6.  应用链注销接口

#### 1.1.6.1. 接口描述

LogoutAppchain：提供注销应用链的内部接口，用于管理员注销相关应用链。返回提案ID用于中继链管理员审核。

#### 1.1.6.2. 请求参数

| 参数   | 类型   | 示例值          | 描述     |
| ------ | ------ | --------------- | -------- |
| id     | String | Appchain1       | 应用链ID |
| reason | String | Logout appchain | 申请理由 |

 

#### 1.1.6.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.1.6.4. 示例

```go
func (am *AppchainManager) LogoutAppchain(id,

	reason string) *boltvm.Response { 
```

 

## 1.2. 验证规则管理

### 1.2.1. 常量定义

规则地址：ruleAddress

|                                                  |                                                           |
| ------------------------------------------------ | --------------------------------------------------------- |
| **常量值**                                       | **常量说明**                                              |
| **"0x00000000000000000000000000000000000000a0"** | Fabric v1.4.3和Fabric  v1.4.4类型应用链的复杂验证规则地址 |
| **"0x00000000000000000000000000000000000000a1"** | Fabric v1.4.3和Fabric v1.4.4类型应用链的简单验证规则地址  |
| **"0x00000000000000000000000000000000000000a2"** | 所有应用链都有的无验证规则                                |
| **自定义字符串**                                 | 用户自己上传规则后，部署得到的地址                        |

规则事件类型：EventType

|                |                  |
| -------------- | ---------------- |
| **常量值**     | **常量说明**     |
| **"update"**   | 更新主验证规则   |
| **"register"** | 注册规则，无提案 |
| **"logout"**   | 注销规则，无提案 |

 

### 1.2.2.  验证规则注册接口

#### 1.2.2.1. 接口描述

RegisterRule：提供应用链管理员注册验证规则的接口。

#### 1.2.2.2. 请求参数

| 参数        | 类型   | 示例值                                       | 描述                                         |
| ----------- | ------ | -------------------------------------------- | -------------------------------------------- |
| chainID     | String | Appchain1                                    | 应用链ID                                     |
| ruleAddress | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 规则地址，部分取值参见规则地址常量表（上面） |
| ruleUrl     | String | https://bitxhub.cn/                          | 验证规则url                                  |

 

#### 1.2.2.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.2.2.4. 示例

```go
func (rm *RuleManager) RegisterRule(

   chainId string, 

   ruleAddress, 

   ruleUrl string) *boltvm.Response {

```

 

### 1.2.3.  主验证规则更新接口

#### 1.2.3.1. 接口描述

UpdateMasterRule：提供应用链管理员更新主验证规则的接口。

#### 1.2.3.2. 请求参数

| 参数                 | 类型   | 示例值                                       | 描述               |
| -------------------- | ------ | -------------------------------------------- | ------------------ |
| chainID              | String | Appchain1                                    | 应用链ID           |
| newMasterruleAddress | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 新的主验证规则地址 |
| reason               | String | Update master rule                           | 更新理由           |

 

#### 1.2.3.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.2.3.4. 示例

```go
func (rm *RuleManager) UpdateMasterRule(chainID string,

   newMasterruleAddress, 

   reason string) *boltvm.Response { 
```

 

### 1.2.4.  验证规则注销接口

#### 1.2.4.1. 接口描述

LogoutRule：提供应用链管理员注销验证规则的接口。

#### 1.2.4.2. 请求参数

| 参数        | 类型   | 示例值                                       | 描述         |
| ----------- | ------ | -------------------------------------------- | ------------ |
| chainID     | String | Appchain1                                    | 应用链ID     |
| ruleAddress | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 验证规则地址 |

 

#### 1.2.4.3. 返回参数

| 参数        | 类型   | 示例值 | 描述                                 |
| ----------- | ------ | ------ | ------------------------------------ |
| proposal_id | String | “”     | 注销规则不发提案，提案id返回空字符串 |
| extra       | []byte | nil    | 扩展字段                             |

 

#### 1.2.4.4. 示例

```go
func (rm *RuleManager) LogoutRule(chainID string, 

   ruleAddress string) *boltvm.Response {
```

 

## 1.3. 身份管理

中继链平台上可能出现的身份有三种：中继链治理管理员、中继链审计管理员、应用链管理员。 

-  **中继链治理管理员**：参与中继链上的投票治理，也可以发起中继链上的各种治理操作；

-  **中继链审计管理员**：不参与中继链上的投票治理，与nvpNode绑定并对节点上同步的数据进行审计。审计管理员在中继链上的直接管理功能除了查询信息外只有更新绑定审计节点权限；

-  **应用链管理员**：应用链上的管理员，中继链并不直接对应用链管理员进行管理，仅做身份的记录。应用链管理员在中继链上的直接管理功能主要在应用链管理员和验证规则管理模块。

其中中继链治理管理员又可以分为超级治理管理员和普通治理管理员，中继链初始状态至少有一个超级管理员，超级管理员后续不可以注册、冻结或注销，一些高优先级的提案（比如与管理员相关的提案、与冻结或注销之类会造成严峻后果的提案）需要超级管理员投票才能生效

中继链的身份管理功能主要提供关于中继链管理员的注册、冻结、解冻、注销功能，关于审计管理员的注册、绑定审计节点、注销功能。

### 1.3.1. 常量定义

身份模块合约地址：0x000000000000000000000000000000000000000d

身份类型：

|                            |                  |
| -------------------------- | ---------------- |
| **常量值**                 | **常量说明**     |
| **"governanceAdmin"**      | 中继链治理管理员 |
| **"auditAdmin"**           | 审计管理员       |
| **"appchainAdmin"**        | 应用链管理员     |
| **"superGovernanceAdmin"** | 创世管理员       |

身份事件类型：EventType：

|                |                        |
| -------------- | ---------------------- |
| **常量值**     | **常量说明**           |
| **"register"** | 注册身份               |
| **"freeze"**   | 冻结身份               |
| **"activate"** | 解冻身份               |
| **"logout"**   | 注销身份               |
| **"bind"**     | 审计管理员绑定审计节点 |

 

### 1.3.2.  身份注册接口

#### 1.3.2.1. 接口描述

RegisterRole：提供身份注册的内部接口，返回提案ID用于中继链管理员审核。

 

#### 1.3.2.2. 请求参数

表格 3‑1存储数据缓存-请求参数

| 参数        | 类型   | 示例值                                       | 描述                                     |
| ----------- | ------ | -------------------------------------------- | ---------------------------------------- |
| roleId      | String | 0x96B5619A637639E16  51EADD49CDA5B8EF4882331 | 身份ID                                   |
| roleType    | String | auditAdmin                                   | 身份类型，取值参见身份类型常量表（上面） |
| nodeAccount | String | 0x96B5619A637639E1651  EADD49CDA5B8EF4882331 | 审计节点的账户信息                       |
| reason      | String | Register auditAdmin                          | 申请理由                                 |

身份类型为governanceAdmin时nodeAccount传空字符串

注册的roleType不可以为"appchainAdmin"和"superGovernanceAdmin"

 

#### 1.3.2.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.3.2.4. 示例

```go
 func (rm *RoleManager) RegisterRole(roleId, 

   roleType, 

   nodeAccount, 

   reason string) *boltvm.Response {
```



### 1.3.3.  身份冻结接口

#### 1.3.3.1. 接口描述

FreezeRole：提供冻结节点身份的内部接口。

#### 1.3.3.2. 请求参数

| 参数   | 类型   | 示例值            | 描述     |
| ------ | ------ | ----------------- | -------- |
| roleId | String | auditAdmin        | 身份ID   |
| reason | String | Freeze auditAdmin | 申请理由 |

 

#### 1.3.3.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.3.3.4. 示例

```go
func (rm *RoleManager) FreezeRole(roleId,

   reason string) *boltvm.Response { 
```



### 1.3.4.  身份激活接口

#### 1.3.4.1. 接口描述

ActivateRole：提供激活身份的内部接口。

#### 1.3.4.2. 请求参数

| 参数   | 类型   | 示例值              | 描述     |
| ------ | ------ | ------------------- | -------- |
| roleId | String | auditAdmin          | 身份ID   |
| reason | String | Activate auditAdmin | 申请理由 |

 

#### 1.3.4.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.3.4.4. 示例

```go
func (rm *RoleManager) ActivateRole(roleId,

   reason string) *boltvm.Response { 
```



### 1.3.5.  身份注销接口

#### 1.3.5.1. 接口描述

LogoutRole：提供注销身份的内部接口，用于管理员注销相关身份信息。返回提案ID用于中继链管理员审核。

#### 1.3.5.2. 请求参数

| 参数   | 类型   | 示例值            | 描述     |
| ------ | ------ | ----------------- | -------- |
| roleId | String | auditAdmin        | 身份ID   |
| reason | String | Logout auditAdmin | 申请理由 |

 

#### 1.3.5.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.3.5.4. 示例

```go
func (rm *RoleManager) LogoutRole(roleId,

   reason string) *boltvm.Response {  
```

### 1.3.6.  绑定审计节点接口

#### 1.3.6.1. 接口描述

BindRole：提供绑定审计节点的内部接口，返回提案ID用于中继链管理员审核。

 

#### 1.3.6.2. 请求参数

表格 3‑1存储数据缓存-请求参数

| 参数        | 类型   | 示例值                                       | 描述               |
| ----------- | ------ | -------------------------------------------- | ------------------ |
| roleId      | String | 0x96B5619A637639E16  51EADD49CDA5B8EF4882331 | 身份ID             |
| nodeAccount | String | 0xa365c19A637639E1651  EADD49CDA5B8EF4882331 | 审计节点的账户信息 |
| reason      | String | Bind auditAdmin                              | 申请理由           |

 

#### 1.3.6.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.3.6.4. 示例

```go
func (rm *RoleManager) BindRole(roleId, 

   nodeAccount, 

   reason string) *boltvm.Response {
```

 

## 1.4. 节点管理

节点管理合约地址：0x0000000000000000000000000000000000000018

### 1.4.1. 常量定义

节点类型：nodeType

|                   |                |
| ----------------- | -------------- |
| **常量值**        | **常量说明**   |
| **"vpNode"**      | 参与共识的节点 |
| **"nvpNode"**     | 审计节点       |
| **"primaryNode"** | 创世节点       |

节点事件类型：EventType

|                |                  |
| -------------- | ---------------- |
| **常量值**     | **常量说明**     |
| **"register"** | 注册节点         |
| **"logout"**   | 注销节点         |
| **"update"**   | 更新审计节点权限 |

 

### 1.4.2.  节点注册接口

#### 1.4.2.1. 接口描述

RegisterNode：提供应用链管理员注册节点的接口。

#### 1.4.2.2. 请求参数

| 参数        | 类型   | 示例值                                           | 描述                                       |
| ----------- | ------ | ------------------------------------------------ | ------------------------------------------ |
| nodeAccount | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea     | 节点的账户地址                             |
| nodeType    | String | vpNode                                           | 节点类型，取值参见节点类型常量表           |
| nodePid     | String | QmXi58fp9ZczF3Z5iz1yXA  ez3Hy5NYo1R8STHWKEM9XnTL | 共识节点的Pid                              |
| nodeVpId    | Uint64 | 5                                                | 共识节点的id                               |
| nodeName    | String | Node1                                            | 审计节点的名称                             |
| permitStr   | String | appchain1，appchain2                             | 审计节点的审计权限，逗号隔开的多个应用链id |
| reason      | String | Register vpNode                                  | 申请理由                                   |

- nodeType为共识节点（vpNode）时，nodeName和permitStr均传空字符串；

- nodeType为审计节点（nvpNode）时，nodePid传空字符串，nodeVpId传0；

-  注册的nodeType不可以为"primaryNode"。

#### 1.4.2.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.4.2.4. 示例

```go
func (nm *NodeManager) RegisterNode(nodeAccount, 

   nodeType, 

   nodePid string, 

   nodeVpId uint64, 

   nodeName, 

   permitStr, 

   reason string) *boltvm.Response {
```

 

### 1.4.3.  节点更新接口

#### 1.4.3.1. 接口描述

UpdateNode：提供应用链管理员更新节点的接口。

#### 1.4.3.2. 请求参数

| 参数        | 类型   | 示例值                                       | 描述                                       |
| ----------- | ------ | -------------------------------------------- | ------------------------------------------ |
| nodeAccount | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 节点的账户地址                             |
| nodeName    | String | Node1                                        | 审计节点的名称                             |
| permitStr   | string | appchain1，appchain2                         | 审计节点的审计权限，逗号隔开的多个应用链id |
| reason      | String | Update vpNode                                | 申请理由                                   |

 

#### 1.4.3.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.4.3.4. 示例

```go
func (nm *NodeManager) UpdateNode(nodeAccount, 

   nodeName, 

   permitStr, 

   reason string) *boltvm.Response {
```

 

### 1.4.4.  节点注销接口

#### 1.4.4.1. 接口描述

LogoutNode：提供应用链管理员注销节点的接口。

#### 1.4.4.2. 请求参数

| 参数        | 类型   | 示例值                                       | 描述           |
| ----------- | ------ | -------------------------------------------- | -------------- |
| nodeAccount | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 节点的账户地址 |
| reason      | String | Logout vpNode                                | 申请理由       |

 

#### 1.4.4.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案id   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.4.4.4. 示例

```go
func (nm *NodeManager) LogoutNode(

   nodeAccount, 

   reason string) *boltvm.Response { 
```

 

## 1.5. 服务管理

### 1.5.1. 常量定义

服务类型：typ

|                    |                    |
| ------------------ | ------------------ |
| **常量值**         | **常量说明**       |
| **"CallContract"** | 合约调用类型的服务 |

服务提案事件类型：EventType

|                |              |
| -------------- | ------------ |
| **常量值**     | **常量说明** |
| **"register"** | 注册服务     |
| **"update"**   | 更新服务     |
| **"freeze"**   | 冻结服务     |
| **"activate"** | 解冻服务     |
| **"logout"**   | 注销服务     |

 

### 1.5.2.  服务注册接口

#### 1.5.2.1. 接口描述

RegisterService：提供服务注册的内部接口，返回提案ID用于中继链管理员审核。

 

#### 1.5.2.2. 请求参数

| 参数      | 类型   | 示例值                                       | 描述                             |
| --------- | ------ | -------------------------------------------- | -------------------------------- |
| chainID   | String | Appchain1                                    | 服务ID                           |
| serviceID | String | 0x0df6746854682fc8c  e5A7729BBB14c7652ae4516 | 服务id，即服务合约地址           |
| name      | String | Transfer                                     | 服务名称                         |
| typ       | String | CallContract                                 | 服务类型，取值参见服务类型常量表 |
| intro     | String | Test111                                      | 服务简介                         |
| ordered   | Uint64 | 1                                            | 服务是否需要按序调用             |
| permits   | String | 0x4d7…，0x42b…                               | 服务管理员地址列表，逗号隔开     |
| details   | String | Test                                         | 服务详情                         |
| reason    | String | Register fabric service                      | 申请理由                         |

ordererd默认参数为1：按序调用。

#### 1.5.2.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.5.2.4. 示例

```go
func (sm *ServiceManager) RegisterService(chainID, 

   serviceID, 

   name, 

   typ, 

   intro string, 

   ordered uint64, 

   permits, 

   details, 

   reason string) *boltvm.Response { 
```

 

### 1.5.3.  服务更新接口

#### 1.5.3.1. 接口描述

UpdateService：提供更新服务的内部接口，返回提案ID用于中继链管理员审核。

#### 1.5.3.2. 请求参数

| 参数           | 类型   | 示例值                                       | 描述                         |
| -------------- | ------ | -------------------------------------------- | ---------------------------- |
| chainServiceID | String | 0x0df6746854682fc8c  e5A7729BBB14c7652ae4516 | 服务id，即服务合约地址       |
| name           | String | Transfer                                     | 服务名称                     |
| intro          | String | Test111                                      | 服务简介                     |
| permits        | String | 0x4d7…，0x42b…                               | 服务管理员地址列表，逗号隔开 |
| details        | String | Test                                         | 服务详情                     |
| reason         | String | Update fabric service                        | 申请理由                     |

 

#### 1.5.3.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.5.3.4. 示例

```go
func (sm *ServiceManager) UpdateService(chainServiceID, 

   name, 

   intro, 

   permits, 

   details, 

   reason string) *boltvm.Response { 
 
```



### 1.5.4.  服务冻结接口

#### 1.5.4.1. 接口描述

FreezeService：提供冻结服务的内部接口，用于管理员冻结相关服务。返回提案ID用于中继链管理员审核。

#### 1.5.4.2. 请求参数

| 参数           | 类型   | 示例值                                       | 描述     |
| -------------- | ------ | -------------------------------------------- | -------- |
| chainServiceID | String | 0x0df6746854682fc8c  e5A7729BBB14c7652ae4516 | 服务ID   |
| reason         | String | freeze fabric service                        | 申请理由 |

 

#### 1.5.4.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.5.4.4. 示例

```go
func (sm *ServiceManager) FreezeService(

   chainServiceID, 

   reason string) *boltvm.Response { 
```

 

### 1.5.5.  服务激活接口

#### 1.5.5.1. 接口描述

ActivateService：提供激活服务的内部接口，用于管理员激活相关服务。返回提案ID用于中继链管理员审核。

#### 1.5.5.2. 请求参数

| 参数           | 类型   | 示例值                                       | 描述     |
| -------------- | ------ | -------------------------------------------- | -------- |
| chainServiceID | String | 0x0df6746854682fc8c  e5A7729BBB14c7652ae4516 | 服务ID   |
| reason         | String | Activate fabric service                      | 申请理由 |

 

#### 1.5.5.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.5.5.4. 示例

   func (sm *ServiceManager) ActivateService(

   chainServiceID, 

   reason string) *boltvm.Response { 

### 1.5.6.  服务注销接口

#### 1.5.6.1. 接口描述

LogoutService：提供注销服务的内部接口，用于管理员注销相关服务。返回提案ID用于中继链管理员审核。

#### 1.5.6.2. 请求参数

| 参数           | 类型   | 示例值                                     | 描述     |
| -------------- | ------ | ------------------------------------------ | -------- |
| chainServiceID | String | 0x0df6746854682fc8ce5A7729BBB14c7652ae4516 | 服务ID   |
| reason         | String | freeze fabric service                      | 申请理由 |

 

#### 1.5.6.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.5.6.4. 示例

```go
func (sm *ServiceManager) LogoutService(

   chainServiceID, 

   reason string) *boltvm.Response { 
```

 

### 1.5.7.  服务评价接口

#### 1.5.7.1. 接口描述

EvaluateService：提供服务评价的内部接口，用于评价相关服务。不会返回提案，默认返回空字符串。

#### 1.5.7.2. 请求参数

| 参数           | 类型    | 示例值                                       | 描述           |
| -------------- | ------- | -------------------------------------------- | -------------- |
| chainServiceID | String  | 0x0df6746854682fc8c  e5A7729BBB14c7652ae4516 | 服务ID         |
| desc           | String  | Very good                                    | 服务的评价内容 |
| score          | Float64 | 97.2                                         | 服务的评分     |

 

#### 1.5.7.3. 返回参数

| 参数        | 类型   | 示例值 | 描述                   |
| ----------- | ------ | ------ | ---------------------- |
| proposal_id | String | “”     | 不发提案，返回空字符串 |
| extra       | []byte | nil    | 扩展字段               |

 

#### 1.5.7.4. 示例

```go
func (sm *ServiceManager) EvaluateService(

   chainServiceID, 

   desc string, 

   score float64) *boltvm.Response {
```

 

## 1.6. Dapp管理

### 1.6.1. 常量定义

dapp类型： type

|                   |              |
| ----------------- | ------------ |
| **常量值**        | **常量说明** |
| **"tool"**        | 工具类       |
| **"application"** | 应用类       |
| **"game"**        | 游戏类       |
| **"others"**      | 其他类       |

dapp提案事件类型：EventType

|                |              |
| -------------- | ------------ |
| **常量值**     | **常量说明** |
| **"register"** | 注册dapp     |
| **"update"**   | 更新dapp     |
| **"freeze"**   | 冻结dapp     |
| **"activate"** | 激活dapp     |

 

### 1.6.2.  Dapp注册接口

#### 1.6.2.1. 接口描述

RegisterDapp：提供Dapp注册的内部接口，返回提案ID用于中继链管理员审核。

 

#### 1.6.2.2. 请求参数

| 参数     | 类型   | 示例值                  | 描述                             |
| -------- | ------ | ----------------------- | -------------------------------- |
| name     | String | Super Mario             | Dapp名称                         |
| typ      | String | game                    | dapp类型，取值参见dapp类型常量表 |
| desc     | String | test game               | Dapp描述                         |
| url      | String | https://superMario.com/ | dapp的url                        |
| conAddrs | String | 0x1df…，0x42a…          | dapp的合约地址，多个逗号隔开     |
| permits  | String | 0xaba…，0x4ag…          | dapp的黑名单，多个逗号隔开       |
| reason   | String | Register dapp           | 申请理由                         |

#### 1.6.2.3. 返回参数

| 参数        | 类型   | 示例值                                     | 描述                 |
| ----------- | ------ | ------------------------------------------ | -------------------- |
| proposal_id | String | 0x546b4f47fd1753f0EC48027C1F166D4Ae6b54fea | 提案ID               |
| extra       | []byte | []byte({0x834ad2dd…)-0)                    | 扩展字段,返回Dapp Id |

DappID为Dapp注册者地址加上index，index为该注册者所拥有所有的dapp长度。

#### 1.6.2.4. 示例

```go
func (dm *DappManager) RegisterDapp(

   name, 

   typ, 

   desc, 

   url, 

   conAddrs, 

   permits, 

   reason string) *boltvm.Response { 
```

 

### 1.6.3.  Dapp更新接口

#### 1.6.3.1. 接口描述

UpdateDapp：提供更新Dapp的内部接口，返回提案ID用于中继链管理员审核。

#### 1.6.3.2. 请求参数

| 参数     | 类型   | 示例值                                       | 描述                         |
| -------- | ------ | -------------------------------------------- | ---------------------------- |
| id       | String | 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f1-0 | Dapp ID                      |
| name     | String | Super Mario                                  | Dapp名称                     |
| desc     | String | test update game                             | Dapp描述                     |
| url      | String | https://superMario.com/                      | dapp的url                    |
| conAddrs | String | 0x1df…，0x42a…                               | dapp的合约地址，多个逗号隔开 |
| permits  | String | 0xaba…，0x4ag…                               | dapp的黑名单，多个逗号隔开   |
| reason   | String | Update dapp                                  | 申请理由                     |

只更新desc时reason传空字符串。

#### 1.6.3.3. 返回参数

| 参数        | 类型   | 示例值                                     | 描述     |
| ----------- | ------ | ------------------------------------------ | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC48027C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                        | 扩展字段 |

 

#### 1.6.3.4. 示例

```go
func (dm *DappManager) UpdateDapp(

   id, 

   name, 

   desc, 

   url, 

   conAddrs, 

   permits, 

   reason string) *boltvm.Response { 
```

 

### 1.6.4.  Dapp冻结接口

#### 1.6.4.1. 接口描述

FreezeDapp：提供冻结Dapp的内部接口，用于管理员冻结相关Dapp。返回提案ID用于中继链管理员审核。

#### 1.6.4.2. 请求参数

| 参数   | 类型   | 示例值                                       | 描述     |
| ------ | ------ | -------------------------------------------- | -------- |
| id     | String | 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f1-0 | DappID   |
| reason | String | Freeze dapp                                  | 申请理由 |

 

#### 1.6.4.3. 返回参数

| 参数        | 类型   | 示例值                                     | 描述     |
| ----------- | ------ | ------------------------------------------ | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC48027C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                        | 扩展字段 |

 

#### 1.6.4.4. 示例

```go
func (dm *DappManager) FreezeDapp(

   id, 

   reason string) *boltvm.Response { 
```

 

### 1.6.5.  Dapp激活接口

#### 1.6.5.1. 接口描述

ActivateDapp：提供激活Dapp的内部接口，用于管理员激活相关Dapp。返回提案ID用于中继链管理员审核。

#### 1.6.5.2. 请求参数

| 参数   | 类型   | 示例值                                       | 描述     |
| ------ | ------ | -------------------------------------------- | -------- |
| id     | String | 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f1-0 | DappID   |
| reason | String | Activate dapp                                | 申请理由 |

 

#### 1.6.5.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.6.5.4. 示例

```go
func (dm *DappManager) ActivateDapp(

   id, 

   reason string) *boltvm.Response { 
```

 

### 1.6.6.  Dapp转让接口

#### 1.6.6.1. 接口描述

TransferDapp：提供Dapp转让的内部接口，用于管理员转让相关Dapp。返回提案ID用于中继链管理员审核。

#### 1.6.6.2. 请求参数

 

| 参数         | 类型   | 示例值                                            | 描述           |
| ------------ | ------ | ------------------------------------------------- | -------------- |
| id           | String | 0xc0Ff2e0b3189132D815b8</br>eb325bE17285AC898f1-0 | DappID         |
| newOwnerAddr | String | 0x12342e0b3189132D815b8</br>eb325bE17285AC898f2   | 转让接收者地址 |
| reason       | String | Transfer dapp                                     | 申请理由       |

 

#### 1.6.6.3. 返回参数

| 参数        | 类型   | 示例值                                     | 描述     |
| ----------- | ------ | ------------------------------------------ | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC48027C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                        | 扩展字段 |

 

#### 1.6.6.4. 示例

```go
func (dm *DappManager) TransferDapp(

   id, 

   newOwnerAddr, 

   reason string) *boltvm.Response {
```

 

### 1.6.7.  Dapp评价接口

#### 1.6.7.1. 接口描述

EvaluateDapp：提供评价Dapp的内部接口，用于评价相关Dapp。不会返回提案，默认返回空字符串。

#### 1.6.7.2. 请求参数

| 参数  | 类型    | 示例值                                       | 描述           |
| ----- | ------- | -------------------------------------------- | -------------- |
| id    | String  | 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f1-0 | Dapp ID        |
| desc  | String  | Very good                                    | 服务的评价内容 |
| score | Float64 | 97.2                                         | 服务的评分     |

 

#### 1.6.7.3. 返回参数

| 参数        | 类型   | 示例值 | 描述                   |
| ----------- | ------ | ------ | ---------------------- |
| proposal_id | String | “”     | 不发提案，返回空字符串 |
| extra       | []byte | nil    | 扩展字段               |

 

#### 1.6.7.4. 示例

  

```go
func (dm *DappManager) EvaluateDapp(

   id, 

   desc string, 

   score float64) *boltvm.Response {
```

 

## 1.7. 提案管理

### 1.7.1. 常量定义

投票信息：approve

|               |              |
| ------------- | ------------ |
| **常量值**    | **常量说明** |
| **"approve"** | 赞成票       |
| **"reject"**  | 反对票       |

### 1.7.2.  提案投票接口

#### 1.7.2.1. 接口描述

Vote：提供管理员投票的内部接口，用于为相关提案进行投票。

#### 1.7.2.2. 请求参数

| 参数    | 类型   | 示例值                                       | 描述           |
| ------- | ------ | -------------------------------------------- | -------------- |
| id      | String | 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f3-2 | 提案ID         |
| approve | String | approve                                      | 服务的评价内容 |
| reason  | String | Approve this proposal                        | 理由           |

 

#### 1.7.2.3. 返回参数

| 参数 | 类型   | 示例值 | 描述                          |
| ---- | ------ | ------ | ----------------------------- |
| data | []byte | nil    | 成功返回nil，失败返回错误信息 |

 

#### 1.7.2.4. 示例

   

```go
func (g *Governance) Vote(

   id, 

   approve, 

   reason string) *boltvm.Response {
```

 

## 1.8. 策略管理

### 1.8.1. 常量定义

治理模块名称：module

|                             |              |
| --------------------------- | ------------ |
| **常量值**                  | **常量说明** |
| **"appchain_mgr"**          | 应用链管理   |
| **"rule_mgr"**              | 验证规则管理 |
| **"node_mgr"**              | 节点管理     |
| **"service_mgr"**           | 服务管理     |
| **"role_mgr"**              | 身份管理     |
| **"proposal_strategy_mgr"** | 投票策略管理 |
| **"dapp_mgr"**              | Dapp管理     |

策略名称：typ

|                      |                    |
| -------------------- | ------------------ |
| **常量值**           | **常量说明**       |
| **"SimpleMajority"** | 简单多数制         |
| **"ZeroPermission"** | 零投票制，无需投票 |

### 1.8.2.  更新模块策略接口

#### 1.8.2.1. 接口描述

UpdateProposalStrategy：提供更新各模块的投票策略内部接口，用于为相关提案投票策略更新。

#### 1.8.2.2. 请求参数

| 参数          | 类型   | 示例值                   | 描述                                 |
| ------------- | ------ | ------------------------ | ------------------------------------ |
| module        | String | service_mgr              | 治理模块名称，取值参见策略管理常量表 |
| typ           | String | SimpleMajority           | 策略名称，取值参加策略管理常量表     |
| strategyExtra | String | a>0.5*t                  | 策略公式                             |
| reason        | String | update proposal strategy | 理由                                 |

 

#### 1.8.2.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.8.2.4. 示例

   

```go
func (g *GovStrategy) UpdateProposalStrategy(

   module string, 

   typ string, 

   strategyExtra string, 

   reason string) *boltvm.Response { 
```

 

### 1.8.3.  更新全局策略接口

#### 1.8.3.1. 接口描述

UpdateAllProposalStrategy：提供更新全局的投票策略内部接口，用于为相关提案投票策略更新。

#### 1.8.3.2. 请求参数

| 参数          | 类型   | 示例值                       | 描述                             |
| ------------- | ------ | ---------------------------- | -------------------------------- |
| typ           | String | SimpleMajority               | 策略名称，取值参加策略管理常量表 |
| strategyExtra | String | a>0.5*t                      | 策略公式                         |
| reason        | String | update all proposal strategy | 理由                             |

 

#### 1.8.3.3. 返回参数

| 参数        | 类型   | 示例值                                       | 描述     |
| ----------- | ------ | -------------------------------------------- | -------- |
| proposal_id | String | 0x546b4f47fd1753f0EC480  27C1F166D4Ae6b54fea | 提案ID   |
| extra       | []byte | nil                                          | 扩展字段 |

 

#### 1.8.3.4. 示例

   

```go
func (g *GovStrategy) UpdateAllProposalStrategy(

   typ string, 

   strategyExtra string, 

   reason string) *boltvm.Response { 
```



## 1.9. 跨链管理

### 1.9.1.  获取中继链ID接口

#### 1.9.1.1. 接口描述

从账本获取当前中继链的ID。

#### 1.9.1.2. 请求参数

无

#### 1.9.1.3. 返回参数

| 参数 | 类型   | 示例值         | 描述                      |
| ---- | ------ | -------------- | ------------------------- |
| data | []byte | []byte(“1356”) | 返回bitxhubID的序列化数据 |

#### 1.9.1.4. 示例

   

```go
func (x *InterchainManager) GetBitXHubID() *boltvm.Response {

```

1.9.2.  获取中继链服务ID接口

#### 1.9.2.1. 接口描述

从账本获取当前中继链的所有已注册的服务的ServiceID。

#### 1.9.2.2. 请求参数

无

#### 1.9.2.3. 返回参数

| 参数 | 类型   | 示例值   | 描述                              |
| ---- | ------ | -------- | --------------------------------- |
| data | []byte | Too long | 返回所有serviceID集合的序列化数据 |

#### 1.9.2.4. 示例

```go
func (x *InterchainManager) GetAllServiceIDs() *boltvm.Response {
```

 

### 1.9.3.  获取交易哈希接口

#### 1.9.3.1. 接口描述

根据IBTP的ID从中继链获得Interchain/Receipt交易哈希。

#### 1.9.3.2. 请求参数

| 参数  | 类型   | 示例值                                       | 描述                           |
| ----- | ------ | -------------------------------------------- | ------------------------------ |
| id    | String | {1356:eth1:0x48a…}-{1356:fabric1:0xd26…}-{2} | IBTP ID                        |
| isReq | String | true                                         | Interchain:true  Receipt:false |

#### 1.9.3.3. 返回参数

| 参数 | 类型   | 示例值   | 描述                 |
| ---- | ------ | -------- | -------------------- |
| data | []byte | Too long | 返回哈希值的byte数组 |

#### 1.9.3.4. 示例

   

```go
func (x *InterchainManager) GetIBTPByID(

   id string, 

   isReq bool) *boltvm.Response {

```

 

### 1.9.4.  获取跨链信息接口

#### 1.9.4.1. 接口描述

根据fullServiceID从中继链获取InterchainCounter、ReceiptCounter、SourceInterchainCounter、SourceReceiptCounter。其中：

-  InterchainCounter维护了当前服务作为来源服务发往各个目的服务的Interchain类型的交易数目；

- ReceiptCounter维护了当前服务作为目的服务发往各个来源服务的Receipt类型的交易数目；

- SourceInterchainCounter维护了当前服务作为目的服务收到各个来源服务的Interchain类型的交易数目；

- SourceReceiptCounter维护了当前服务作为来源服务收到各个目的服务的Receipt类型的交易数目。

#### 1.9.4.2. 请求参数

| 参数 | 类型   | 示例值              | 描述                                              |
| ---- | ------ | ------------------- | ------------------------------------------------- |
| id   | String | 1356:eth1:0x48a25e… | Full service ID：结构为bxhId:appchainId:serviceId |

#### 1.9.4.3. 返回参数

| 参数 | 类型   | 示例值   | 描述                                                 |
| ---- | ------ | -------- | ---------------------------------------------------- |
| data | []byte | Too long | 返回interchain类型的byte数组，Interchain类型如下所示 |

 

**Interchain：**

| 参数                    | 类型              | 示例值           | 描述                                                   |
| ----------------------- | ----------------- | ---------------- | ------------------------------------------------------ |
| ID                      | String            | 1356:eth1:0x45e… | Full service ID：结构为</br>bxhId:appchainId:serviceId |
| InterchainCounter       | map[string]uint64 | Too long         | 详见接口描述                                           |
| ReceiptCounter          | map[string]uint64 | Too long         | 详见接口描述                                           |
| SourceInterchainCounter | map[string]uint64 | Too long         | 详见接口描述                                           |
| SourceReceiptCounter    | map[string]uint64 | Too long         | 详见接口描述                                           |

 

#### 1.9.4.4. 示例

   

```go
func (x *InterchainManager) GetInterchain(

   id string) *boltvm.Response {
```

 

### 1.9.5.  跨链交易处理接口

#### 1.9.5.1. 接口描述

对于IBTP进行处理。返回具体的回执内容。

#### 1.9.5.2. 请求参数

| 参数 | 类型    | 示例值   | 描述                 |
| ---- | ------- | -------- | -------------------- |
| ibtp | pb.IBTP | Too long | 提取交易中的IBTP字段 |

 

#### 1.9.5.3. 返回参数

| 参数 | 类型   | 示例值 | 描述         |
| ---- | ------ | ------ | ------------ |
| ret  | []byte | nil    | 返回处理结果 |

如果目的链是中继链构造回执IBTP并返回，如果目的链处理错误返回begin_failure的结果，正常执行返回nil。

 

#### 1.9.5.4. 示例

   

```go
func (x *InterchainManager) HandleIBTP(

   ibtp *pb.IBTP) *boltvm.Response {
```

 

## 1.10.  事务管理

跨链事务方案具有三个特性：**一对一跨链**；**一对多跨链**；**超时回滚**。

对于一对多跨链的场景，其状态转换时需要注意子事务的状态情况：

1. 如果收到一个失败的子事务，则事务状态转换成失败；

2. 需要等所有子事务都成功，才能将事务状态转换成成功。

另外，每一次事务状态转换，中继链都需要抛出IBTP来通知应用链：

·    `BEGIN`：通知目的链；

·    `BEGIN_FAILURE`: 通知来源链和目的链；

·    `BEGIN_ROLLBACK`：通知来源链和目的链；

·    `BEGIN -> FAILURE`： 通知来源链；

·    `BEGIN_FAILURE -> FAILURE`: 无需通知；

·    `SUCCESS`: 通知来源链；

·    `ROLLBACK`：无需通知。

 

### 1.10.1. 一对多事务接口

一对多跨链是指，在一个来源链的业务合约的交易中，抛出了多个指向不同目的链的跨链事件。 一对多跨链和一对一跨链的整体流程一样，只是其中事务分为全局事务和子事务的概念：

-  子事务：指来源链和某个目的链的跨链事务；

-  全局事务：综合各个子事务的整体事务。

因此在中继链的跨链事务合约中，为一对多跨链事务特别设计了一个事务信息结构来支持该场景下的跨链事务状态记录和更新。

#### 1.10.1.1.   接口描述

开启一对多事务。

#### 1.10.1.2.   请求参数

| 参数          | 类型   | 示例值                                            | 描述                                             |
| ------------- | ------ | ------------------------------------------------- | ------------------------------------------------ |
| globalID      | String | 0x546b4f47fd1753f0EC480</br>27C1F166D4Ae6b54fea   | 解析IBTP的group，构造所有目的链的map，返回哈希值 |
| ibtpID        | String | {1356:eth1:0x48a25e…}-{1356:fabric1:0xd26ac…}-{2} | Ibtp的ID                                         |
| timeoutHeight | Uint64 | 10                                                | 超时块高                                         |
| isFailed      | Bool   | false                                             | 中继链检查目的链是否有报错                       |
| count         | Uint64 | 2                                                 | 一对多的目的链数目                               |

 

#### 1.10.1.3.   返回参数

| 参数 | 类型   | 示例值   | 描述                                         |
| ---- | ------ | -------- | -------------------------------------------- |
| data | []byte | Too long | 返回change的序列化数据，change的数据结构如下 |

 

**StatusChange**：

| 参数         | 类型              | 示例值                                      | 描述                          |
| ------------ | ----------------- | ------------------------------------------- | ----------------------------- |
| prevStatus   | TransactionStatus | TransactionStatus_BEGIN                     | 之前的状态                    |
| curStatus    | TransactionStatus | TransactionStatus_SUCCESS                   | 转换后的状态                  |
| otherIBTPIDs | []string          | 1356:chain0:service0-1356:chain1:service1-1 | 其他的ibtpID集合，一对一为nil |

 

**TransactionStatus**

```go
  const (

​    TransactionStatus_BEGIN     TransactionStatus = 0

​    TransactionStatus_BEGIN_FAILURE TransactionStatus = 1

​    TransactionStatus_BEGIN_ROLLBACK TransactionStatus = 2

​    TransactionStatus_SUCCESS    TransactionStatus = 3

​    TransactionStatus_FAILURE    TransactionStatus = 4

​    TransactionStatus_ROLLBACK    TransactionStatus = 5

   )

```

 

#### 1.10.1.4.   示例

```go
func (t *TransactionManager) BeginMultiTXs(

   globalID, 

   ibtpID string, 

   timeoutHeight uint64, 

   isFailed bool, 

   count uint64) *boltvm.Response { 

```



### 1.10.2. 一对一事务接口

#### 1.10.2.1.   接口描述

开启一对一事务。

#### 1.10.2.2.   请求参数

| 参数          | 类型   | 示例值                                            | 描述                       |
| ------------- | ------ | ------------------------------------------------- | -------------------------- |
| txId          | String | {1356:eth1:0x48a25e…}-{1356:fabric1:0xd26ac…}-{2} | Ibtp的ID                   |
| timeoutHeight | Uint64 | 10                                                | 超时块高                   |
| isFailed      | Bool   | false                                             | 中继链检查目的链是否有报错 |

 

#### 1.10.2.3.   返回参数

| 参数 | 类型   | 示例值   | 描述                                                         |
| ---- | ------ | -------- | ------------------------------------------------------------ |
| data | []byte | Too long | 返回change的序列化数据，change的数据结构见一对多事务接口返回参数部分 |

 

#### 1.10.2.4.   示例

```go
func (t *TransactionManager) Begin(

   txId string, 

   timeoutHeight uint64, 

   isFailed bool) *boltvm.Response {
```



### 1.10.3. 回执事务处理接口

#### 1.10.3.1.   接口描述

开启回执事务处理流程。

#### 1.10.3.2.   请求参数

| 参数   | 类型   | 示例值                                            | 描述         |
| ------ | ------ | ------------------------------------------------- | ------------ |
| txId   | String | {1356:eth1:0x48a25e…}-{1356:fabric1:0xd26ac…}-{2} | Ibtp的ID     |
| result | Int32  | int32(pb.IBTP_RECEIPT_SUCCESS)                    | 回执IBTP类型 |

 

#### 1.10.3.3.   返回参数

| 参数 | 类型   | 示例值   | 描述                                                         |
| ---- | ------ | -------- | ------------------------------------------------------------ |
| data | []byte | Too long | 返回change的序列化数据，change的数据结构见一对多事务接口返回参数部分 |

 

#### 1.10.3.4.   示例

```go
func (t *TransactionManager) Report(

   txId string, 

   result int32) *boltvm.Response {
```

 

### 1.10.4. 查询事务状态接口

#### 1.10.4.1.   接口描述

获取ibtpID对应的当前事务状态。

#### 1.10.4.2.   请求参数

| 参数 | 类型   | 示例值                                            | 描述     |
| ---- | ------ | ------------------------------------------------- | -------- |
| txId | String | {1356:eth1:0x48a25e…}-{1356:fabric1:0xd26ac…}-{2} | Ibtp的ID |

 

#### 1.10.4.3.   返回参数

| 参数   | 类型   | 示例值                                               | 描述         |
| ------ | ------ | ---------------------------------------------------- | ------------ |
| status | []byte | []byte(strconv.Itoa(int(TransactionStatus_SUCCESS))) | 返回当前状态 |

 

#### 1.10.4.4.   示例

```go
func (t *TransactionManager) GetStatus(txId string) *boltvm.Response {
```

 