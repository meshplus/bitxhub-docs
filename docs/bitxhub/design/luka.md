# 区块链浏览器后台设计方案

区块链浏览器通过中继链接口获取数据时，由于链上数据部分未做索引处理，在构建查询逻辑时过于复杂，影响查询效率。  

区块链浏览器后台整体采用 **SpringBoot** 框架，并通过 **Elasticsearch** 为从中继链上同步到的区块信息构建索引，在controller层为区块链浏览器提供个性化接口，返回所需数据。  

由于采用了Elasticsearch进行数据存储和搜索，并且对从中继链同步到的区块数据进行了缓存，不但支持复杂条件查询，而且查询效率高。

## 整体架构
区块链浏览器后台主要包括区块数据同步服务和浏览器接口服务，区块链数据同步模块定时向中继链获取区块数据并存储到ES中，浏览器接口服务主要用于构建接口以供区块链浏览器查询特定数据。  

## 区块数据同步服务
区块数据同步模块用于将中继链的所有区块数据同步到ES上。
```java
public void execute()
```
execute方法定时执行，每 **500** 毫秒执行一次，每次同步 **100** 个(可配置)区块。

针对内置合约BVM交易，比如应用链管理合约或者验证规则管理合约，是有具体的数据结构，因此需要分别进行同步。
```java
public void flushAopchains()

public void flushRules()

public void flushDapps()

public void flushServices() 

public void flushRoles()

public void flushNodes()

public void flushProposalStrategies()
```

## 浏览器接口服务

### 1. 应用链服务接口
应用链服务接口只提供根据状态查询应用链相关信息，对应用链的治理统一通过交易服务接口。

#### 1.1 查询所有应用链信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/appchain/all</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|   参数名   |  类型   |   描述   | 必输参数 |
| :--------: | :-----: | :------: | :------: |
| pageNumber | Integer |  分页号  |    否    |
|  pageSize  | Integer | 分页大小 |    否    |

返回参数

|   参数名   |   类型    |     描述      | 是否非空 |
|:-------:|:-------:|:-----------:|:----:|
|  code   | Integer |  错误码，0为正常   |  是   |
|  data   | String  | Application |  是   |
| message | String  |    错误信息     |  否   |

#### 1.2 查询特定应用链信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/appchain/{id}</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|   参数名    |   类型    |  描述   | 必输参数 |
|:--------:|:-------:|:-----:|:----:|
|    id    | String  | 应用链ID |  是   |

返回参数

|   参数名   |   类型    |     描述      | 是否非空 |
|:-------:|:-------:|:-----------:|:----:|
|  code   | Integer |  错误码，0为正常   |  是   |
|  data   | String  | Application |  是   |
| message | String  |    错误信息     |  否   |

#### 1.3 查询不同状态应用链信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/appchain/status</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |                                                 描述                                                  | 必输参数 |
|:----------:|:-------:|:---------------------------------------------------------------------------------------------------:|:----:|
| pageNumber | Integer |                                                 分页号                                                 |  否   |
|  pageSize  | Integer |                                                分页大小                                                 |  否   |
|   status   | String  | 状态:<br/>正常：available/freezing<br/>更新中：updating<br/>激活中：activating<br/>系统冻结：frozen<br/>已注销：forbidden |  是   |

返回参数

|   参数名   |   类型    |     描述      | 是否非空 |
|:-------:|:-------:|:-----------:|:----:|
|  code   | Integer |  错误码，0为正常   |  是   |
|  data   | String  | Application |  是   |
| message | String  |    错误信息     |  否   |

### 2. 交易服务接口
#### 2.1 查询特定交易信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/transaction/{hash}</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

| 参数名  |   类型    | 描述  | 必输参数 |
|:----:|:-------:|:---:|:----:|
| hash | String  | 哈希  |  是   |

返回参数

|   参数名   |   类型    |     描述      | 是否非空 |
|:-------:|:-------:|:-----------:|:----:|
|  code   | Integer |  错误码，0为正常   |  是   |
|  data   | String  | Transaction |  是   |
| message | String  |    错误信息     |  否   |

#### 2.3 查询IBTP列表信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/transaction/ibtps</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述   | 必输参数 |
|:----------:|:-------:|:-----:|:----:|
|    from    | String  | 来源链ID |  是   |
|     to     | String  | 目的链ID |  是   |
| pageNumber | Integer |  分页号  |  否   |
|  pageSize  | Integer | 分页大小  |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   IBTP   |  是   |
| message | String  |   错误信息   |  否   |

#### 2.4 查询特定地址nonce

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/transaction/nonce</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述   | 必输参数 |
|:----------:|:-------:|:-----:|:----:|
|  account   | String  |  地址   |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |  nonce   |  是   |
| message | String  |   错误信息   |  否   |

#### 2.5 发送只读交易

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/transaction/view</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>POST</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

| 参数名 |   类型   |           描述            | 必输参数 |
|:---:|:------:|:-----------------------:|:----:|
| raw | String | Base64编码后的protobuf的交易结构 |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  | Receipt  |  是   |
| message | String  |   错误信息   |  否   |

#### 2.6 发送交易

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/transaction/tx</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>POST</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

| 参数名 |   类型   |           描述            | 必输参数 |
|:---:|:------:|:-----------------------:|:----:|
| raw | String | Base64编码后的protobuf的交易结构 |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  | Receipt  |  是   |
| message | String  |   错误信息   |  否   |

### 3. 区块服务接口
#### 3.1 查询区块链当前信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/block/chain_meta</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

返回参数

|   参数名   |   类型    |    描述     | 是否非空 |
|:-------:|:-------:|:---------:|:----:|
|  code   | Integer | 错误码，0为正常  |  是   |
|  data   | String  | ChainMeta |  是   |
| message | String  |   错误信息    |  否   |

#### 3.2 查询特定区块信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/block</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|  参数名  |   类型    |       描述        | 必输参数 |
|:-----:|:-------:|:---------------:|:----:|
| type  | Integer | 0: 高度<br/>1: 哈希 |  否   |
| value | String  |     哈希或者高度      |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |  Block   |  是   |
| message | String  |   错误信息   |  否   |

#### 3.3 查询特定范围区块信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/block/blocks</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述  | 必输参数 |
|:----------:|:-------:|:----:|:----:|
| pageNumber | Integer | 分页号  |  否   |
|  pageSize  | Integer | 分页大小 |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |  Block   |  是   |
| message | String  |   错误信息   |  否   |

### 4. WebSocket接口

```go 
// 订阅跨链事件
simpMessagingTemplate.convertAndSend("/topic/interchainEvent", interchainEvent);

//订阅区块头信息
simpMessagingTemplate.convertAndSend("/topic/blockHeader", blockHeader);

//订阅区块信息
simpMessagingTemplate.convertAndSend("/topic/block", block);
```

### 5. Dapp管理服务接口
#### 5.1 查询所有Dapp信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/dapp/all</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述  | 必输参数 |
|:----------:|:-------:|:----:|:----:|
| pageNumber | Integer | 分页号  |  否   |
|  pageSize  | Integer | 分页大小 |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Dapp   |  是   |
| message | String  |   错误信息   |  否   |

#### 5.2 查询特定Dapp信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/dapp/{id}</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|   参数名    |   类型    |   描述    | 必输参数 |
|:--------:|:-------:|:-------:|:----:|
|    id    | String  | Dapp ID |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Dapp   |  是   |
| message | String  |   错误信息   |  否   |

#### 5.3 查询不同状态Dapp信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/dapp/status</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |                                                                                 描述                                                                                  | 必输参数 |
|:----------:|:-------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:----:|
| pageNumber | Integer |                                                                                 分页号                                                                                 |  否   |
|  pageSize  | Integer |                                                                                分页大小                                                                                 |  否   |
|   status   | String  | 状态:<br/>正常：available/freezing<br/>审核中：registering<br/>已驳回：unavailable<br/>更新中：updating<br/>激活中：activating<br/>系统冻结：frozen<br/>转让中：transferring<br/> 已转让 transferred |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Dapp   |  是   |
| message | String  |   错误信息   |  否   |

### 6. Service管理服务接口
#### 6.1 查询所有Service信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/service/all</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述  | 必输参数 |
|:----------:|:-------:|:----:|:----:|
| pageNumber | Integer | 分页号  |  否   |
|  pageSize  | Integer | 分页大小 |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  | Service  |  是   |
| message | String  |   错误信息   |  否   |

#### 6.2 查询特定Service信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/service/{id}</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|   参数名    |   类型    |     描述     | 必输参数 |
|:--------:|:-------:|:----------:|:----:|
|    id    | String  | Service ID |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  | Service  |  是   |
| message | String  |   错误信息   |  否   |

#### 6.3 查询不同状态Service信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/service/status</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |                                                                描述                                                                 | 必输参数 |
|:----------:|:-------:|:---------------------------------------------------------------------------------------------------------------------------------:|:----:|
| pageNumber | Integer |                                                                分页号                                                                |  否   |
|  pageSize  | Integer |                                                               分页大小                                                                |  否   |
|   status   | String  | 状态：<br/>正常：available<br/>更新审核：updating<br/>冻结审核：freezing<br/>系统冻结：frozen<br/>解冻审核：activating<br/>注销审核：logouting<br/>已注销：forbidden |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  | Service  |  是   |
| message | String  |   错误信息   |  否   |

### 7. 验证规则管理服务接口
#### 7.1 查询所有验证规则信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/rule/all</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述  | 必输参数 |
|:----------:|:-------:|:----:|:----:|
| pageNumber | Integer | 分页号  |  否   |
|  pageSize  | Integer | 分页大小 |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Rule   |  是   |
| message | String  |   错误信息   |  否   |

#### 7.2 查询特定验证规则信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/rule/{id}</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|   参数名    |   类型    |   描述    | 必输参数 |
|:--------:|:-------:|:-------:|:----:|
|    id    | String  | Rule ID |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Rule   |  是   |
| message | String  |   错误信息   |  否   |

#### 7.3 查询不同状态验证规则信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/rule/status</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |                                         描述                                         | 必输参数 |
|:----------:|:-------:|:----------------------------------------------------------------------------------:|:----:|
| pageNumber | Integer |                                        分页号                                         |  否   |
|  pageSize  | Integer |                                        分页大小                                        |  否   |
|   status   | String  | 状态：<br/>已部署：bindable<br/>已绑定：available/unbinding<br/>审核中：binding<br/>已注销：forbidden |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Rule   |  是   |
| message | String  |   错误信息   |  否   |

### 8.角色管理服务接口
#### 8.1 查询所有身份信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/role/all</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述  | 必输参数 |
|:----------:|:-------:|:----:|:----:|
| pageNumber | Integer | 分页号  |  否   |
|  pageSize  | Integer | 分页大小 |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Role   |  是   |
| message | String  |   错误信息   |  否   |

#### 8.2 查询特定身份信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/role/{id}</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|   参数名    |   类型    |   描述    | 必输参数 |
|:--------:|:-------:|:-------:|:----:|
|    id    | String  | Role ID |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Role   |  是   |
| message | String  |   错误信息   |  否   |

#### 8.3 查询不同状态身份信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/role/status</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |                                                                     描述                                                                      | 必输参数 |
|:----------:|:-------:|:-------------------------------------------------------------------------------------------------------------------------------------------:|:----:|
| pageNumber | Integer |                                                                     分页号                                                                     |  否   |
|  pageSize  | Integer |                                                                    分页大小                                                                     |  否   |
|   status   | String  | 状态：<br/>正常：available/freezing<br/>审核中：registering<br/>已驳回：unavailable<br/>更新中：updating<br/>激活中：activating<br/>系统冻结：frozen<br/>已注销：forbidden |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Role   |  是   |
| message | String  |   错误信息   |  否   |


### 9. 节点管理服务接口
#### 9.1 查询所有节点信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/node/all</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |  描述  | 必输参数 |
|:----------:|:-------:|:----:|:----:|
| pageNumber | Integer | 分页号  |  否   |
|  pageSize  | Integer | 分页大小 |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Node   |  是   |
| message | String  |   错误信息   |  否   |

#### 9.2 查询特定节点信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/node/{id}</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |   描述    | 必输参数 |
|:----------:|:-------:|:-------:|:----:|
| pageNumber | Integer |   分页号   |  否   |
|  pageSize  | Integer |  分页大小   |  否   |
|   status   | String  | Node ID |  是   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Node   |  是   |
| message | String  |   错误信息   |  否   |

#### 9.3 查询不同状态节点信息

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/node/status</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

请求参数

|    参数名     |   类型    |                                             描述                                             | 必输参数 |
|:----------:|:-------:|:------------------------------------------------------------------------------------------:|:----:|
| pageNumber | Integer |                                            分页号                                             |  否   |
|  pageSize  | Integer |                                            分页大小                                            |  否   |
|   status   | String  | 状态：<br/>正常：available<br/>审核中：registering<br/>已驳回：unavailable<br/>已注销：forbidden, **不可以再注册** |  否   |

返回参数

|   参数名   |   类型    |    描述    | 是否非空 |
|:-------:|:-------:|:--------:|:----:|
|  code   | Integer | 错误码，0为正常 |  是   |
|  data   | String  |   Node   |  是   |
| message | String  |   错误信息   |  否   |

### 10. 投票策略管理服务接口
##### 10.1 查询所有模块投票策略

基本信息

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/proposalStrategy/all</td>
    </tr>
    <tr>
        <td><b>请求方式</b></td>
        <td>GET</td>
    </tr>
    <tr>
        <td><b>Content-Type</b></td>
        <td>application/json</td>
    </tr>
</table>

返回参数

|   参数名   |   类型    |        描述        | 是否非空 |
|:-------:|:-------:|:----------------:|:----:|
|  code   | Integer |     错误码，0为正常     |  是   |
|  data   | String  | ProposalStrategy |  是   |
| message | String  |       错误信息       |  否   |