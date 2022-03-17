### 查询账户余额

该接口用于查询指定账户的余额。

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/account_balance/{address}</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| address | path | 账户地址 | Yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbResponse](#pbresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' \
  'http://127.0.0.1:9091/v1/account_balance/0x79a1215469FaB6f9c63c1816b45183AD3624bE34' \
  -H 'accept: application/json'
{
	"data": "eyJ0eXBlIjoibm9ybWFsIiwiYmFsYW5jZSI6MTAwMDAwMDAwMDAwMDAwMDAxOTk5OTk5OTk5NDc0OTk5OTk2LCJjb250cmFjdF9jb3VudCI6NCwiY29kZV9oYXNoIjoiMHgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwIn0="
}

# base64 decode
$ echo eyJ0eXBlIjoibm9ybWFsIiwiYmFsYW5jZSI6MTAwMDAwMDAwMDAwMDAwMDAxOTk5OTk5OTk5NDc0OTk5OTk2LCJjb250cmFjdF9jb3VudCI6NCwiY29kZV9oYXNoIjoiMHgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwIn0=| base64 -d | jq
{
  "type": "normal",
  "balance": 1e+35,
  "contract_count": 4,
  "code_hash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}

# error Response
$ curl -X 'GET' \
  'http://127.0.0.1:9091/v1/account_balance/error_account' \
  -H 'accept: application/json'
{
	"error": "invalid account address: 6683AD3624bE34",
	"code": 2,
	"message": "invalid account address: 6683AD3624bE34"
}
```

### 获得审计节点参与的应用链所有信息

该接口将实时推送中继链所有当前审计节点审计应用链的数据，包括每个区块的交易与交易回执、当前交易所在区块高度等。主要用于浏览器后台同步区块数据。

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>v1/audit_subscription</td>
    </tr>
    <tr>
        <td><b>接口类型</b></td>
    	<td>Websocket</td>
	</tr>
</table>

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| type | query | 查询类型，默认为0 | yes | string |
| audit_node_id | query | 审计节点地址 | yes | string |
| block_height | query | 查询的起始高度 | yes | string (uint64) |
| extra | query | 扩展字段 | No | byte |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response.(streaming responses) | object |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |



### 查询区块

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/block</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| type | query | 查询类型，默认为0；<br/>0：根据区块高度查询区块；<br/>1：根据区块哈希查询区块 | No | string |
| value | query | 区块高度/区块哈希 | Yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbBlock](#pbblock) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' \
  'http://127.0.0.1:9091/v1/block?value=5' \
  -H 'accept: application/json'
  
$ curl -X 'GET' \
  'http://127.0.0.1:9091/v1/block?type=1&value=0x2770809A891Db83E49BF3901E3d13A6d32E20A4c5E3854c36c612cECfa2ca76A' \
  -H 'accept: application/json'
{"block_header":{"number":"5","state_root":"0x285E623118D74A3b0b1C62d95B9995Fec6B37Ac1B89Cd68cd7326e34d828F95C","tx_root":"0xce3Db318f5BD1e175d87e45594Afe7C69B1E29cFD178CA12CCEeEf60101D5681","receipt_root":"0x29756d7AAc9f5dAB860f80657D7bb850e5C2A69D4F350FBCF3694FA01a03A26a","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x4833F61A2af1ACfCcbE8cc8ff722aD8cABe2DFB609eae768E77a048768Bf8210","timestamp":"1646184994224598000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"transactions":{"Transactions":[{"from":"0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8","to":"0xDd519C7b8B881b29c96dF6Dac64F893708Cc368a","timestamp":1646184994023266000,"transaction_hash":"0xA2aACA5C381c61ee9e6c7444c5d2b7b3714d5688a6a95Cb6D9C0492FDd1bF38F","payload":"EgEx","signature":"AyVi3f6Af/UNU3qCD5L6mFavWgvElLlqEvdDQk62VfLDLDyw/EjoRHZf05A181uFWPaLB3vLL3xO74GGaPpJoL8A"}]},"block_hash":"0x2770809A891Db83E49BF3901E3d13A6d32E20A4c5E3854c36c612cECfa2ca76A","signature":"n1cCNyDEQwaG39K5zF7US4BPF+QLJk1YRyTMXRe2rU5cDDUcRijUOP7dCGnBFl4GKrOU2R7U4E5NLtUdFe74YwA="}

# error Response
$ curl -X 'GET' \
  'http://127.0.0.1:9091/v1/block?value=0x2770809A891Db83E49BF3901E3d13A6d32E20A4c5E3854c36c612cECfa2ca76A' \
  -H 'accept: application/json'
{"error":"wrong block number: 0x2770809A891Db83E49BF3901E3d13A6d32E20A4c5E3854c36c612cECfa2ca76A","code":2,"message":"wrong block number: 0x2770809A891Db83E49BF3901E3d13A6d32E20A4c5E3854c36c612cECfa2ca76A"}
```



### 获得指定高度区块头信息

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>v1/block_header</td>
    </tr>
    <tr>
        <td><b>接口类型</b></td>
    	<td>Websocket</td>
	</tr>
</table>

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| begin | query | 查询区块起始高度 | yes | string (uint64) |
| end | query | 查询区块结束高度 | yes | string (uint64) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response.(streaming responses) | object |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |



### 查询多个区块头信息

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/block_headers</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| start | query | 查询区块起始高度 | yes | string (uint64) |
| end | query | 查询区块结束高度 | Yes | string (uint64) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbGetBlockHeadersResponse](#pbgetblockheadersresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/block_headers?start=0&end=3' -H 'accept: application/json'
{"blockHeaders":[{"number":"1","state_root":"0xaa3ac2e5127e4F3aCAC9d2fceE82f3A621c15b673849BCedFbBd359C3B00052c","tx_root":"0x0000000000000000000000000000000000000000000000000000000000000000","receipt_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x0000000000000000000000000000000000000000000000000000000000000000","timestamp":"1646201909318901000","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},{"number":"2","state_root":"0x4FfA2dd03ecDAf157846F974b96D658Bd91e02F67b671b7b405d2E5694127656","tx_root":"0x27063A944fF013634725E6800d7AD2f36f592C71aAc51C5Ffbb564Cf66A6f79e","receipt_root":"0x70da91a06702a039ae4966D7F920b5E9B4DF8af0095b1FC5C77F42Ba6958BA59","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0xfB041b3D98d317C209d9782e97E873B24f42c3D0C93B24feDA94f6A244e73fEb","timestamp":"1646201922579843000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},{"number":"3","state_root":"0x8329E6cb8864D635Fe9f45ddf11EfB3427bBB8bE4fdC5f58A191B454226471a8","tx_root":"0x750b3933c679d0aEe0e7aE96DadB47536FfDea35b4d9433825db911e18310aC0","receipt_root":"0x56cD0510dD28160b48856bf68FBdfdA80B7d3de11d68115Dcd47E46a82BE9938","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0xF8334E6F3A38AA8707C66943Be251389E7eaa1f53275B882C2353569E720F3a0","timestamp":"1646201922880627000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"}]}

# error Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/block_headers?start=5&end=3' -H 'accept: application/json'
{"error":"get block headers failed: start and end input err","code":2,"message":"get block headers failed: start and end input err"}%
```



### 查询多个区块

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/blocks</td>
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
**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| start | query | 查询区块起始高度 | No | string (uint64) |
| end | query | 查询区块结束高度 | No | string (uint64) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbGetBlocksResponse](#pbgetblocksresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/blocks?start=1&end=3' -H 'accept: application/json'
{"blocks":[{"block_header":{"number":"1","state_root":"0xaa3ac2e5127e4F3aCAC9d2fceE82f3A621c15b673849BCedFbBd359C3B00052c","tx_root":"0x0000000000000000000000000000000000000000000000000000000000000000","receipt_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x0000000000000000000000000000000000000000000000000000000000000000","timestamp":"1646203069189034000","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"transactions":{},"block_hash":"0xfB041b3D98d317C209d9782e97E873B24f42c3D0C93B24feDA94f6A244e73fEb","signature":"1/DpyH8jllrE+r+wlUDzZ45MHAFzXdtSpzd8ZyzXAlYtyoKjhVqR1j+Ty+bi1dEuMt7ejla9ARRIgv10IVzDHAE="},{"block_header":{"number":"2","state_root":"0xDc3904D9DA9A22e8e68a1cB73347DF5AeCeB5A10F66A27526838A58137237846","tx_root":"0xBa2Ac8f8273f4D07eb963775149F868691EAAa5F753ee414B70F87cBE24bB521","receipt_root":"0x6452cd73d1DDBE93B49924068f81711B7f6bB6C795Dea95A161cB53706fb8dCf","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0xfB041b3D98d317C209d9782e97E873B24f42c3D0C93B24feDA94f6A244e73fEb","timestamp":"1646203073713070000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"transactions":{"Transactions":[{"from":"0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","to":"0x75fD57f0b48D3B256Fe2B5577d51354bb8492d50","timestamp":1646203073307036000,"transaction_hash":"0x0A7cA3B7e0561191d8059Fb338b72498e7921cc9B306f835524054dbdE6c95ef","payload":"EgEx","signature":"A6b1aLO3+otsPVnN5t1lf4aPpqwh1EFVSZR3aHx+v6Q4PoLC+vSNo5LJrniddGd61MlFOW+Vze4JAO9vJsTFGPcB"}]},"block_hash":"0x0aeF953812D6b240A089bD1A68288553E50987D6158064deAcEc5Af4caD9ad41","signature":"rbc+L3JDcQCspGNpXV0Ullk7VKPiVYV51hpmO+TJKqBmk/BGmd00XdHCrQsuT1iOA+7Wr3SVvW4oYVzZYXCQtQE="},{"block_header":{"number":"3","state_root":"0xA73a9daAcC16a27076a2e779897602d3BB1610986Aed5d64F532c6D13064E21E","tx_root":"0x35Ea7694d78a39aCbd0C7834b8eC3f52715F5Ebd412dbf1b6dcb4ea43B53Bf86","receipt_root":"0x1aD448730D3A984E88FC3a00a7D0766f7Be2BfE6D52aEBD3Ac2d175fD12Feff3","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x0aeF953812D6b240A089bD1A68288553E50987D6158064deAcEc5Af4caD9ad41","timestamp":"1646203074014189000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"transactions":{"Transactions":[{"from":"0x79a1215469FaB6f9c63c1816b45183AD3624bE34","to":"0x75fD57f0b48D3B256Fe2B5577d51354bb8492d50","timestamp":1646203073813146000,"transaction_hash":"0x8b688e31689971CA25B8F637ec651cdFc512A04B8cfF142D37BB0660B8614b41","payload":"EgEx","signature":"A74heb72ak0/NZ8EcIvQWcXFqnWczWPYxWqAwYVXJ2jvZdVzKejHDyUg+WHjRygFnpV+aWTkvXwGibu2e6Q40ggA"}]},"block_hash":"0x12c2F62C5065c400BFBbda2692C5158C718cB48b3dC231d42E147E043E7849e9","signature":"whId2KY+6VogzQyajne6xZxFtBaimoEFrj/DfP6+F0xLiy9l9l3GVDSb9Gnb9fc+1QHkMPVM/rSFXySfwKM1HwA="}]}%

# bad Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/blocks?start=5&end=3' -H 'accept: application/json'
{"error":"get blocks failed: start and end input err","code":2,"message":"get blocks failed: start and end input err"}%
```



### 查询中继链信息

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/chain_meta</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| type | query | 暂时不需要 | No | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbChainMeta](#pbchainmeta) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/chain_meta' -H 'accept: application/json'
{"height":"40","block_hash":"0x667b31f57331Cf01fBB5cD94f7af7043C4Ca5ceADb7df8f292ebB89d06a44B78","interchain_tx_count":"1"}%
```



### 设置主网关信息

**基本信息**

用于网关主备模式，网关竞选为主网关时在中继链设置地址等信息。如果同一个address已经设置了主网关，则会报错。

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/set_master_pier</td>
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

**请求参数**

| Name | Located in | Description                    | Required | Schema                    |
| ---- | ---------- | ------------------------------ | -------- | ------------------------- |
| body | body       | pier相关信息，支持json格式导入 | Yes      | [pbPierInfo](#pbpierinfo) |

**返回参数**

| Code    | Description                   | Schema                        |
| ------- | ----------------------------- | ----------------------------- |
| 200     | A successful response.        | [pbResponse](#pbresponse)     |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'POST' 'http://127.0.0.1:9091/v1/set_master_pier' -d '{"address": "appchain1","index": "1","timeout": "1000"}' -H 'accept: application/json'
{
	"data": "EglhcHBjaGFpbjE="
}

# bad Response
$ curl -X 'POST' 'http://127.0.0.1:9091/v1/set_master_pier' -d '{"address": "appchain1","index": "2","timeout": "1000"}' -H 'accept: application/json'

{"error":"master pier already exist","code":2,"message":"master pier already exist"}%
```



### 主网关发送心跳信息

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/heart_beat</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema                    |
| ---- | ---------- | ----------- | -------- | ------------------------- |
| body | body       |             | Yes      | [pbPierInfo](#pbpierinfo) |

**返回参数**

| Code    | Description                   | Schema                        |
| ------- | ----------------------------- | ----------------------------- |
| 200     | A successful response.        | [pbResponse](#pbresponse)     |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'POST' 'http://127.0.0.1:9091/v1/heart_beat' -d '{"address": "appchain1","index": "2","timeout": "1000"}' -H 'accept: application/json'

{"data":"EglhcHBjaGFpbjE="}

# bad Response
$ curl -X 'POST' 'http://127.0.0.1:9091/v1/heart_beat' -d '{"address": "appchain1","index": "1","timeout": "1000"}' -H 'accept: application/json'

{"error":"send heart beat to appchain1 with index %!d(string=1) failed: wrong pier heart beat","code":2,"message":"send heart beat to appchain1 with index %!d(string=1) failed: wrong pier heart beat"}%
```



### 查询主网关信息

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/check_master_pier/{address}</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| address | path | 网关绑定的应用链id | Yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbResponse](#pbresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

**!!注意：因为response的data使用protobuf编码，返回`address`以及`status`，base64解码后会将status字段的值屏蔽掉，建议将输出的值进行base64解码，再通过proto解码得到正确的输出。**

```shell
# success Response
curl -X 'GET' 'http://127.0.0.1:9091/v1/check_master_pier/address\=appchain5' -H 'accept: application/json'
{"data":"CAISEmFkZHJlc3NcPWFwcGNoYWluNQ=="}%

// 输出格式转换示例
func TestCheckMasterPier(t *testing.T) {
	resp := &pb.CheckPierResponse{}
	str := "CAISEmFkZHJlc3NcPWFwcGNoYWluMQ=="
	val, err := base64.StdEncoding.DecodeString(str)
	assert.Nil(t, err)
	err = resp.Unmarshal(val)
	assert.Nil(t, err)
	fmt.Println(resp.Status.String())    //No_MASTER
}
```





### 请求中继链的chainID

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/get_chain_id</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | 暂时不需要 | No | [pbEmpty](#pbempty) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbResponse](#pbresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'POST' 'http://127.0.0.1:9091/v1/get_chain_id' -H 'accept: application/json'
{"data":"TAUAAAAAAAA="}%

// 输出格式转换示例
func TestGetChainId(t *testing.T) {
	resp := "TAUAAAAAAAA="
	val, err := base64.StdEncoding.DecodeString(resp)
	assert.Nil(t, err)
	chainId := binary.LittleEndian.Uint64(val)
	fmt.Printf("get chainId: %d\n", chainId)     // get chainId: 1356
}
```



### 查询不同交易类型的区块

happy_block与block的区别是：happy_block将区块内的交易进行分类，分为Bxh交易与Eth交易。Bxh交易为普通的中继链类型的交易，Eth交易为以太坊类型交易，用于接收[MetaMask](https://metamask.io/faqs.html)发送的以太坊交易。

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/happy_block</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| start | query | 查询区块起始高度 | yes | string (uint64) |
| end | query | 查询区块结束高度 | yes | string (uint64) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbGetHappyBlocksResponse](#pbgethappyblocksresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/happy_block?start=3&end=5' -H 'accept: application/json'
{"blocks":[{"block_header":{"number":"3","state_root":"0x355CDa829D9216e2877732E752E58fd0f23556D4710877e590ffF3d09F5451Ab","tx_root":"0x0BAcDFb5Ca061341899a2f7966885a0b9064B908bdcA208de9A634177A9E9bA6","receipt_root":"0x85fF7C69E5B9Af18D6e8Fa05CBD64145C27E245CDFCE3677bB08A57CD8b2cDA9","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x63F1856440F2431A722540EBd4CcA9F9cAa29C30CeA8A5aE7E17617dF60ACbcb","timestamp":"1646273414234112000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"bxh_txs":[{"from":"0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","to":"0xeedFef830c6FBDDA3257AC883126995702F0eea3","timestamp":"1646273413831236000","transaction_hash":"0x0B06820dcBf89B98551467d27929D2a70826D60a380d5a3708fE0dbbFA2854F8","payload":"Eh0xMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMA==","nonce":"1","signature":"A5gLWXpP3ceErbiyAih+LvxAXKP/6j5dqbxFukdMKxd7ebeuSLvC2FG55urCpXRgKRL1HHNaNLrwO7uZLPIZs0YA"}],"block_hash":"0x99a159973625287CfE4f860DafAF0f9e3713A7da1B051daBCADFacFB22EF7807","signature":"dsmEwAU/HpGfMwhLv+gt5tjyvy9b/xf7Xs1ZncASJ5V2tYc65sh3XZinmerIStDcTN7QNbOG/8NwUqPBpZT8cwE=","index":["0"]},{"block_header":{"number":"4","state_root":"0x322Ae627cAD626cDE71F2c15B2Bcbd891cf170115E358373221E76e2Bd769c81","tx_root":"0xB5deb46c3a7EEEc54D9b39B6872145422BFD0a095E246eC99bf27674120D3DB0","receipt_root":"0x752367ba0851069d5261F05EFD16928b816e802e5c00cA8A0aCC4A4308005d7e","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x99a159973625287CfE4f860DafAF0f9e3713A7da1B051daBCADFacFB22EF7807","timestamp":"1646274209307689000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"eth_txs":["0ecbaJe3f+EpDOMRVQvXhw735enBqszvSbi5jh4m68A="],"block_hash":"0x8120922d0075805024392BF04d45136c484bacAD35f4D102A17046aE44aDfc6f","signature":"z07bAhH1OVcHlLvTXBTloiTdmnxLM3dWwHnvBfTOQEMJ+8DQbfYFAiOJuBhHL7k9iZ+38FwDv19XpNdVNQqnfAA=","index":["1"]},{"block_header":{"number":"5","state_root":"0xA14890B8Bd2FE83fA67f7773886B8D5871c4Ac9f8930275306819149F7db26fB","tx_root":"0x318882b96AC3EE78acbb5726198d0064e850B8549e43513FD95D1985CC94e00A","receipt_root":"0xfA88985EA457D59055e5f140e3319aB0e3E13FBd43cece73BA0c69b18cbE393b","timeout_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x8120922d0075805024392BF04d45136c484bacAD35f4D102A17046aE44aDfc6f","timestamp":"1646274380555215000","version":"MS4wLjA=","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"eth_txs":["dQqdNUBoifM9cP/dPq0MQpSS6sxVF4FKMrrsDOFuraM="],"block_hash":"0x044c825A7FAf20A8dba08D9EB627eCCD1f0B5b15a19c98B4458DF6C6742cDD79","signature":"wLJFIEqKGohd/QLLfbfZB3bxqlg+MsKtDMZIx//4FexXSqNZBTKSV3K5HyF9oZZgS/Wxo9/SeLpao2ZZwxc1JAA=","index":["1"]}]}

# bad Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/happy_block?start=5&end=3' -H 'accept: application/json'
{"error":"get blocks failed: start and end input err","code":2,"message":"get blocks failed: start and end input err"}%
```

### 查询中继链配置信息

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/info</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| type | query | 默认为0；<br/>0: "CHAIN_STATUS",<br/> 1: "NETWORK", <br/>2: "VALIDATORS" | No | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbResponse](#pbresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# 中继链状态
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/info' -H 'accept: application/json'
{"data":"bm9ybWFs"}%

$ echo bm9ybWFs | base64 -d
normal

# 网络信息
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/info?type=1' -H 'accept: application/json'
{"data":"eyIxIjp7ImlkIjoxLCJwaWQiOiJRbVhpNThmcDlaY3pGM1o1aXoxeVhBZXozSHk1TllvMVI4U1RIV0tFTTlYblRMIiwiYWNjb3VudCI6IjB4YzdGOTk5YjgzQWY2REY5ZTY3ZDBhMzdFZTdlOTAwYkYzOGIzRDAxMyIsImhvc3RzIjpbIi9pcDQvMTI3LjAuMC4xL3RjcC80MDAxL3AycC8iXX0sIjIiOnsiaWQiOjIsInBpZCI6IlFtYm1EMWt6ZHN4Umlhd3h1N2JScnRlRGdXMWl0dVh1cFI4R0g2RTJFVUFIWTQiLCJhY2NvdW50IjoiMHg3OWExMjE1NDY5RmFCNmY5YzYzYzE4MTZiNDUxODNBRDM2MjRiRTM0IiwiaG9zdHMiOlsiL2lwNC8xMjcuMC4wLjEvdGNwLzQwMDIvcDJwLyJdfSwiMyI6eyJpZCI6MywicGlkIjoiUW1RVWNEWUN0cWJwbjVOaGF3NEZBR3hRYVNTTnZkV2ZBRmNwUVQ5U1BpZXpiUyIsImFjY291bnQiOiIweDk3YzhCNTE2RDE5ZWRCZjU3NUQ3MmExNzJBZjdGNDE4QkU0OThDMzciLCJob3N0cyI6WyIvaXA0LzEyNy4wLjAuMS90Y3AvNDAwMy9wMnAvIl19LCI0Ijp7ImlkIjo0LCJwaWQiOiJRbVFXM2JGbjhYWDF0NFcxNFBtbjM3YlBKVXBVVkJyQmpuUHVCWndQb2czUWR5IiwiYWNjb3VudCI6IjB4YzBGZjJlMGIzMTg5MTMyRDgxNWI4ZWIzMjViRTE3Mjg1QUM4OThmOCIsImhvc3RzIjpbIi9pcDQvMTI3LjAuMC4xL3RjcC80MDA0L3AycC8iXX19"}%

$ echo eyIxIjp7ImlkIjoxLCJwaWQiOiJRbVhpNThmcDlaY3pGM1o1aXoxeVhBZXozSHk1TllvMVI4U1RIV0tFTTlYblRMIiwiYWNjb3VudCI6IjB4YzdGOTk5YjgzQWY2REY5ZTY3ZDBhMzdFZTdlOTAwYkYzOGIzRDAxMyIsImhvc3RzIjpbIi9pcDQvMTI3LjAuMC4xL3RjcC80MDAxL3AycC8iXX0sIjIiOnsiaWQiOjIsInBpZCI6IlFtYm1EMWt6ZHN4Umlhd3h1N2JScnRlRGdXMWl0dVh1cFI4R0g2RTJFVUFIWTQiLCJhY2NvdW50IjoiMHg3OWExMjE1NDY5RmFCNmY5YzYzYzE4MTZiNDUxODNBRDM2MjRiRTM0IiwiaG9zdHMiOlsiL2lwNC8xMjcuMC4wLjEvdGNwLzQwMDIvcDJwLyJdfSwiMyI6eyJpZCI6MywicGlkIjoiUW1RVWNEWUN0cWJwbjVOaGF3NEZBR3hRYVNTTnZkV2ZBRmNwUVQ5U1BpZXpiUyIsImFjY291bnQiOiIweDk3YzhCNTE2RDE5ZWRCZjU3NUQ3MmExNzJBZjdGNDE4QkU0OThDMzciLCJob3N0cyI6WyIvaXA0LzEyNy4wLjAuMS90Y3AvNDAwMy9wMnAvIl19LCI0Ijp7ImlkIjo0LCJwaWQiOiJRbVFXM2JGbjhYWDF0NFcxNFBtbjM3YlBKVXBVVkJyQmpuUHVCWndQb2czUWR5IiwiYWNjb3VudCI6IjB4YzBGZjJlMGIzMTg5MTMyRDgxNWI4ZWIzMjViRTE3Mjg1QUM4OThmOCIsImhvc3RzIjpbIi9pcDQvMTI3LjAuMC4xL3RjcC80MDA0L3AycC8iXX19 | base64 -d | jq

{
  "1": {
    "id": 1,
    "pid": "QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL",
    "account": "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4001/p2p/"
    ]
  },
  "2": {
    "id": 2,
    "pid": "QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4",
    "account": "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4002/p2p/"
    ]
  },
  "3": {
    "id": 3,
    "pid": "QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS",
    "account": "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4003/p2p/"
    ]
  },
  "4": {
    "id": 4,
    "pid": "QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy",
    "account": "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4004/p2p/"
    ]
  }
}

# 验证者信息
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/info?type=2' -H 'accept: application/json'
{"data":"WyIweGM3Rjk5OWI4M0FmNkRGOWU2N2QwYTM3RWU3ZTkwMGJGMzhiM0QwMTMiLCIweDc5YTEyMTU0NjlGYUI2ZjljNjNjMTgxNmI0NTE4M0FEMzYyNGJFMzQiLCIweDk3YzhCNTE2RDE5ZWRCZjU3NUQ3MmExNzJBZjdGNDE4QkU0OThDMzciLCIweGMwRmYyZTBiMzE4OTEzMkQ4MTViOGViMzI1YkUxNzI4NUFDODk4ZjgiXQ=="}%

$ echo WyIweGM3Rjk5OWI4M0FmNkRGOWU2N2QwYTM3RWU3ZTkwMGJGMzhiM0QwMTMiLCIweDc5YTEyMTU0NjlGYUI2ZjljNjNjMTgxNmI0NTE4M0FEMzYyNGJFMzQiLCIweDk3YzhCNTE2RDE5ZWRCZjU3NUQ3MmExNzJBZjdGNDE4QkU0OThDMzciLCIweGMwRmYyZTBiMzE4OTEzMkQ4MTViOGViMzI1YkUxNzI4NUFDODk4ZjgiXQ== | base64 -d | jq 
[
  "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
  "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
  "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
  "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"
]

# bad Response
# 中继链共识异常
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/info' -H 'accept: application/json'
{"data":"YWJub3JtYWw="}%

$ echo YWJub3JtYWw= | base64 -d
abnormal
```



### 获得指定网关交易集合

将中继链上指定区块区间内的所有interchain_tx_wrappers类型的交易进行分类，获取所有发往对应的网关的交易集合。

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/interchain_tx_wrappers</td>
    </tr>
    <tr>
        <td><b>接口类型</b></td>
        <td>Websocket</td>
    </tr>
</table>

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| begin | query | 查询区块起始高度 | yes | string (uint64) |
| end | query | 查询区块结束高度 | yes | string (uint64) |
| pid | query | 发送的网关的pid地址 | yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response.(streaming responses) | object |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

### 获取中继链多签

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/multi_sign</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| content | query | IBTP的ID | Yes | string |
| type | query | 0: "ASSET_EXCHANGE", <br/>1: "IBTP_REQUEST", <br/>2: "IBTP_RESPONSE", <br/>3: "BLOCK_HEADER", <br/>4: "BURN", | No | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbSignResponse](#pbsignresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/multi_sign?content=1356:0xd7ACefF5C069D55beCbDFDD94a1047d0eB929077:mychannel-1356:0xcdD712fB599F50C20F9Be0a615f356eC7A0267b4:mychannel-1&type=1' -H 'accept: application/json' | jq 
{
  "sign": {
    "0x79a1215469FaB6f9c63c1816b45183AD3624bE34": "r4XWEEJU+v7/WMTb5C0SjxQZmBlvhSUzLe1xGjBRBdd2o8FBnFFYEiUY2yZ0pvyCjVSrqNweCcW9ViMuXFbYOQA=",
    "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013": "dkZyk3DDNGdDJ5JfRrLLg/lyg3Fe6b3Ea5tHcO5/qbhFp5DAG2mSMH/25jFmABrCOLLVmD0cUVToNIW1BDvyTgA="
  }
}

# bad Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/multi_sign?content=111&type=1' -H 'accept: application/json'
{
  "error": "get sign on current node failed: get ibtp 111 isReq true: cannot get the tx hash which contains the IBTP 111",
  "code": 2,
  "message": "get sign on current node failed: get ibtp 111 isReq true: cannot get the tx hash which contains the IBTP 111"
}
```



### 查询指定账户的nonce值

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/pendingNonce/{address}</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| address | path |  | Yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbResponse](#pbresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/pendingNonce/0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013' -H 'accept: application/json' | jq
{
  "data": "MTE="
}
$ echo MTE= | base64 -d | jq
11

# bad Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/pendingNonce/0x111' -H 'accept: application/json' | jq
{
  "error": "invalid account address: 0x111",
  "code": 2,
  "message": "invalid account address: 0x111"
}
```



### 查询交易池中的交易

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/pending_transaction/{tx_hash}</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| tx_hash | path | 交易哈希 | Yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbGetTransactionResponse](#pbgettransactionresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' \
  'http://127.0.0.1:9091/v1/pending_transaction/0xEC9F91503806A10CE5a7a1A263feC0d69dEe4D8864fcC4ee50e5CE047C8f26ED' -H 'accept: application/json' | jq
{
  "tx": {
    "from": "0x4F5C5853a9D48e59a89f5715FfD249c6c8edd5Ab",
    "to": "0x000000000000000000000000000000000000000A",
    "timestamp": "1646296579793145000",
    "transaction_hash": "0xEC9F91503806A10CE5a7a1A263feC0d69dEe4D8864fcC4ee50e5CE047C8f26ED",
    "IBTP": {
      "from": "1356:0x4F5C5853a9D48e59a89f5715FfD249c6c8edd5Ab:mychannel&transfer",
      "to": "1356:0xC2b606650933cCfe04109ef2Aa1FC9Fc3f42D34C:mychannel&transfer",
      "index": "31758",
      "timeoutHeight": "10",
      "proof": "b4SbeC35g5q0oON42UooKTmtMXwoXtG0KESfxdelFB8=",
      "payload": "EioKEGludGVyY2hhaW5DaGFyZ2USBUFsaWNlEgVBbGljZRIIAAAAAAAAAAE="
    },
    "nonce": "31759",
    "signature": "Axh6trpF+u7/v/im4uxnlAN0CT7/m1jRd2EuuLGBFCk9I7C7fRae7vdGm8RiWvJqvbs0n7BqjLxfUZNTtZSue0sB",
    "extra": "CkMKQQo/CAESCBIGYnJva2VyGjEKDHBvbGxpbmdFdmVudAoheyJkaWQ6Yml0eGh1YjpldGhlcmFwcGNoYWluOi4iOjN9ErwKCrkDCiDvcxFAzlbLZQ571ScBuPuvKLC+a4FJU/uBPR9g3Ic4yBKUAwpqElAKBmJyb2tlchJGCi4KJW91dC1tc2ctZGlkOmJpdHhodWI6ZXRoZXJhcHBjaGFpbjouLTQSBQjRChABChQKC291dHRlci1tZXRhEgUI0QoQARIWCgRsc2NjEg4KDAoGYnJva2VyEgIIAxqXAgjIARqRAlt7ImluZGV4Ijo0LCJkc3RfY29udHJhY3RfZGlkIjoiZGlkOmJpdHhodWI6ZXRoZXJhcHBjaGFpbjoweDMwYzVEM2FlYjQ2ODFhZjREMTMzODREQmMyYTcxN0M1MWNiMWNjMTEiLCJzcmNfY29udHJhY3RfaWQiOiJteWNoYW5uZWxcdTAwMjZ0cmFuc2ZlciIsImZ1bmMiOiJpbnRlcmNoYWluQ2hhcmdlIiwiYXJncyI6IkFsaWNlLEFsaWNlLDEiLCJjYWxsYmFjayI6IiIsImFyZ3NjYiI6IiIsInJvbGxiYWNrIjoiaW50ZXJjaGFpblJvbGxiYWNrIiwiYXJnc3JiIjoiQWxpY2UsMSJ9XSIMEgZicm9rZXIaAnYwEv0GCrIGCgdPcmcyTVNQEqYGLS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNKekNDQWM2Z0F3SUJBZ0lRU3cyVUhyKzM4MWVmSXhWUm83cEdnakFLQmdncWhrak9QUVFEQWpCek1Rc3cKQ1FZRFZRUUdFd0pWVXpFVE1CRUdBMVVFQ0JNS1EyRnNhV1p2Y201cFlURVdNQlFHQTFVRUJ4TU5VMkZ1SUVaeQpZVzVqYVhOamJ6RVpNQmNHQTFVRUNoTVFiM0puTWk1bGVHRnRjR3hsTG1OdmJURWNNQm9HQTFVRUF4TVRZMkV1CmIzSm5NaTVsZUdGdGNHeGxMbU52YlRBZUZ3MHlNVEExTWpVd05UUXlNREJhRncwek1UQTFNak13TlRReU1EQmEKTUdveEN6QUpCZ05WQkFZVEFsVlRNUk13RVFZRFZRUUlFd3BEWVd4cFptOXlibWxoTVJZd0ZBWURWUVFIRXcxVApZVzRnUm5KaGJtTnBjMk52TVEwd0N3WURWUVFMRXdSd1pXVnlNUjh3SFFZRFZRUURFeFp3WldWeU1TNXZjbWN5CkxtVjRZVzF3YkdVdVkyOXRNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUVDT0MrWWlXNmR2VjcKcjMySDVNdVo2UVpmN2V6OUh4VW5hbkpVSmlOd0RFNnR0QjFKLzdubk5MWkJBSnJUbEMwVTBkWDhEcGpQMkhMUgpyazh6alRMWHVhTk5NRXN3RGdZRFZSMFBBUUgvQkFRREFnZUFNQXdHQTFVZEV3RUIvd1FDTUFBd0t3WURWUjBqCkJDUXdJb0FnSnlhMFQ1RjI1Z05wejRPbFJYMGpyaWc3U0lFMzk3L1ovcFZRdnRQMWlIb3dDZ1lJS29aSXpqMEUKQXdJRFJ3QXdSQUlnRXJGTHBtSTBYaWUzbW9Cdi81b0xVR3k3SFFYaCtMWk96SGxpY1JQOWMvNENJRnNZOFFQdgpJVTJxL3BJQzd5Vk83aEtpa1p5bjZEVGFSSU1keVh4U2VvdHcKLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQoSRjBEAiBbBkaaMSVq4pzNhWPVnm/0kzrKnOLToRxVnhKQ7qGjNAIgOCsNejaWz5yKSrKammsZr0tGi+EnUep7G9T27ZwRfq0="
  }
}

# bad Response
$ curl -X 'GET' \
  'http://127.0.0.1:9091/v1/pending_transaction/0x6B1c81DeF4ebd5730227C5aEeA7A89846d37307a9C0B5C243f82edf09FBF1d61' \
  -H 'accept: application/json' | jq
{
  "error": "tx:0x6B1c81DeF4ebd5730227C5aEeA7A89846d37307a9C0B5C243f82edf09FBF1d61 not in mempool",
  "code": 2,
  "message": "tx:0x6B1c81DeF4ebd5730227C5aEeA7A89846d37307a9C0B5C243f82edf09FBF1d61 not in mempool"
}
```



### 根据交易哈希查询回执

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/receipt/{tx_hash}</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| tx_hash | path | 交易的哈希值 | Yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbReceipt](#pbreceipt) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/receipt/0x0468219A782c3Ef5B4bf640B4260751F41Bb86C7db8527637060Fe952C1380D0' -H 'accept: application/json' | jq
{
  "tx_hash": "0x0468219A782c3Ef5B4bf640B4260751F41Bb86C7db8527637060Fe952C1380D0",
  "events": [
    {
      "tx_hash": "0x0468219A782c3Ef5B4bf640B4260751F41Bb86C7db8527637060Fe952C1380D0",
      "data": "eyIweDA1N0U4MmQ1MDY1MTRBMzljQTVmODhhOTYxNTdjNmRhMzA4ZTdEODEiOjB9",
      "event_type": 1
    },
    {
      "tx_hash": "0x0468219A782c3Ef5B4bf640B4260751F41Bb86C7db8527637060Fe952C1380D0",
      "data": "eyJBdWRpdE9iaiI6IkNrSXhNelUyT2pCNFlUSTJaalF6UXpReE5EQTRNMFkyTlRJMlFqUTJPVGM0WmpVeE5ERkNOekJpUXprNVJUVmxSVHB0ZVdOb1lXNXVaV3dtZEhKaGJuTm1aWElTUndwQ01UTTFOam93ZURBMU4wVTRNbVExTURZMU1UUkJNemxqUVRWbU9EaGhPVFl4TlRkak5tUmhNekE0WlRkRU9ERTZiWGxqYUdGdWJtVnNKblJ5WVc1elptVnlFZ0VCR2dBaUFDb0EiLCJSZWxhdGVkQ2hhaW5JRExpc3QiOnsiMHhhMjZmNDNDNDE0MDgzRjY1MjZCNDY5NzhmNTE0MUI3MGJDOTlFNWVFIjoiIn19",
      "event_type": 10
    },
    {
      "tx_hash": "0x0468219A782c3Ef5B4bf640B4260751F41Bb86C7db8527637060Fe952C1380D0",
      "data": "eyJBdWRpdE9iaiI6IkNrSXhNelUyT2pCNE1EVTNSVGd5WkRVd05qVXhORUV6T1dOQk5XWTRPR0U1TmpFMU4yTTJaR0V6TURobE4wUTRNVHB0ZVdOb1lXNXVaV3dtZEhKaGJuTm1aWElTQUJvQUlrY0tRakV6TlRZNk1IaGhNalptTkRORE5ERTBNRGd6UmpZMU1qWkNORFk1TnpobU5URTBNVUkzTUdKRE9UbEZOV1ZGT20xNVkyaGhibTVsYkNaMGNtRnVjMlpsY2hJQkFTb0EiLCJSZWxhdGVkQ2hhaW5JRExpc3QiOnsiMHgwNTdFODJkNTA2NTE0QTM5Y0E1Zjg4YTk2MTU3YzZkYTMwOGU3RDgxIjoiIn19",
      "event_type": 10
    }
  ],
  "gas_used": "210000",
  "bloom": "0x00000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000030000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000000000000000000000400200000000000000000000000000000000"
}

# bad Response
# 输入一个不存在的交易哈希
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/receipt/0x0468219A782c3Ef5B4bf640B4260751F41Bb86C7db8527637060Fe952C138111' -H 'accept: application/json' | jq
{
  "error": "cannot found receipt for 0x0468219A782C3ef5B4bf640B4260751f41BB86C7Db8527637060fE952c138111%!(EXTRA string=not found in DB)",
  "code": 5,
  "message": "cannot found receipt for 0x0468219A782C3ef5B4bf640B4260751f41BB86C7Db8527637060fE952c138111%!(EXTRA string=not found in DB)"
}
```





### 事件订阅功能

**基本信息**

该接口用于客户端订阅bitxhub中出现的特定类型的事件。此请求将与客户端建立websocket连接。

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/subscription</td>
    </tr>
    <tr>
        <td><b>接口类型</b></td>
        <td>Websocket</td>
    </tr>
</table>

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| type | query | 0: "BLOCK",<br/>1: "EVENT", <br/>2: "INTERCHAIN_TX", <br/>3: "BLOCK_HEADER", <br/>4: "INTERCHAIN_TX_WRAPPER", <br/>5: "UNION_INTERCHAIN_TX_WRAPPER", <br/>6: "EVM_LOG" | No | string |
| extra | query | 扩展字段 | No | byte |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response.(streaming responses) | object |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |



### 查询tps

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/tps/{begin}/{end}</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| begin | path | 查询区块起始高度 | Yes | string (uint64) |
| end | path | 查询区块结束高度 | Yes | string (uint64) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbResponse](#pbresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |



```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/tps/100/200' -H 'accept: application/json' | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-100    23  100    23    0     0   1341      0 --:--:-- --:--:-- --:--:--  1352
{
  "data": "Jg0AAAAAAAA="
}

// 类型转换示例
func TestTPS(t *testing.T) {
	data := "Jg0AAAAAAAA="
	val, err := base64.StdEncoding.DecodeString(data)
	assert.Nil(t, err)
	tps := binary.LittleEndian.Uint64(val)
	fmt.Printf("tps is:%d\n", tps)		// tps is:3366
}

# bad Response
# 输入结束高度大于最新区块高度
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/tps/100/20000' -H 'accept: application/json' | jq
{
  "error": "get tps between 100 and 510 failed: error during get block TPS",
  "code": 2,
  "message": "get tps between 100 and 510 failed: error during get block TPS"
}
```



### 发送交易

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/transaction</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | 交易具体内容 | Yes | [pbBxhTransaction](#pbbxhtransaction) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbTransactionHashMsg](#pbtransactionhashmsg) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |



### 根据交易哈希获取交易

**基本信息**

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/transaction/{tx_hash}</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| tx_hash | path | 交易哈希 | Yes | string |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbGetTransactionResponse](#pbgettransactionresponse) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |

**示例**

```shell
# success Response
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/transaction/0x14f29C0866C0B658191aF968b96447F848792a3F81686700597aB2A8c30145ED' -H 'accept: application/json' | jq
{
  "tx": {
    "from": "0x80Af96bCeEA047266cc62aa52A4Ade4e509bFe78",
    "to": "0x000000000000000000000000000000000000000A",
    "timestamp": "1646306404905540000",
    "transaction_hash": "0x14f29C0866C0B658191aF968b96447F848792a3F81686700597aB2A8c30145ED",
    "IBTP": {
      "from": "1356:0x80Af96bCeEA047266cc62aa52A4Ade4e509bFe78:mychannel&transfer",
      "to": "1356:0xb49198fB6f10a749421c673Cf78FF86acE9Af534:mychannel&transfer",
      "index": "1",
      "timeoutHeight": "10",
      "proof": "ZbQOap1QGrO4AwOu/wB2NiKt5LH0m3N46EJbJyi1S90=",
      "payload": "EiMKEGludGVyY2hhaW5DaGFyZ2USBUFsaWNlEgVBbGljZRIBMQ=="
    },
    "nonce": "2",
    "signature": "A0rPMupHTkjOyUkQBaAYngIKIFiGy3YwzheTskrNHYxxKiowW2uL0fojrarWH7vfTiq74JAdycZk11NIELnEVGAB",
    "extra": "CiQKIgogCAESCBIGYnJva2VyGhIKDHBvbGxpbmdFdmVudAoCe30SsREKpgMKIEii8xZRv2Bdk8J6HgDkKWWUSiRcvdukxrMw8ZfBNf96EoEDCncSXQoGYnJva2VyElMKPAo0b3V0LW1zZy0weDkyOTU0NWY0NDY5MjE3OGVkYjdmYTQ2OGI0NGM1MzUxNTk2MTg0YmEtMRIECCUQAQoTCgtvdXR0ZXItbWV0YRIECCUQARIWCgRsc2NjEg4KDAoGYnJva2VyEgIIBRr3AQjIARrxAVt7ImluZGV4IjoxLCJkc3RfY2hhaW5faWQiOiIweDkyOTU0NWY0NDY5MjE3OGVkYjdmYTQ2OGI0NGM1MzUxNTk2MTg0YmEiLCJzcmNfY29udHJhY3RfaWQiOiJteWNoYW5uZWxcdTAwMjZ0cmFuc2ZlciIsImRzdF9jb250cmFjdF9pZCI6Im15Y2hhbm5lbFx1MDAyNnRyYW5zZmVyIiwiZnVuYyI6ImludGVyY2hhaW5DaGFyZ2UiLCJhcmdzIjoiQWxpY2UsQWxpY2UsMSIsImNhbGxiYWNrIjoiaW50ZXJjaGFpbkNvbmZpcm0ifV0iDBIGYnJva2VyGgJ2MBKBBwq2BgoHT3JnMk1TUBKqBi0tLS0tQkVHSU4gQ0VSVElGSUNBVEUtLS0tLQpNSUlDS1RDQ0FjK2dBd0lCQWdJUkFMMTJxTi9nb3ZqVkZHT0ZnUU94QXdzd0NnWUlLb1pJemowRUF3SXdjekVMCk1Ba0dBMVVFQmhNQ1ZWTXhFekFSQmdOVkJBZ1RDa05oYkdsbWIzSnVhV0V4RmpBVUJnTlZCQWNURFZOaGJpQkcKY21GdVkybHpZMjh4R1RBWEJnTlZCQW9URUc5eVp6SXVaWGhoYlhCc1pTNWpiMjB4SERBYUJnTlZCQU1URTJOaApMbTl5WnpJdVpYaGhiWEJzWlM1amIyMHdIaGNOTWpBd09EQTBNakF5TURBd1doY05NekF3T0RBeU1qQXlNREF3CldqQnFNUXN3Q1FZRFZRUUdFd0pWVXpFVE1CRUdBMVVFQ0JNS1EyRnNhV1p2Y201cFlURVdNQlFHQTFVRUJ4TU4KVTJGdUlFWnlZVzVqYVhOamJ6RU5NQXNHQTFVRUN4TUVjR1ZsY2pFZk1CMEdBMVVFQXhNV2NHVmxjakV1YjNKbgpNaTVsZUdGdGNHeGxMbU52YlRCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhBMElBQk94eXZBaDhtVWI0Ck91UExZcHpMMzZiRFpSL3JrOWt5WHg2SUI5TXc0SWx2Nmh2OEFDRmhoYlh6eks0TzRXL2xidTNCeFYyRTFhTWYKa3V6K0dkdUd6RE9qVFRCTE1BNEdBMVVkRHdFQi93UUVBd0lIZ0RBTUJnTlZIUk1CQWY4RUFqQUFNQ3NHQTFVZApJd1FrTUNLQUlFRkRqVkxzNzZ1TGxJTEFGTXlwbFJ3b3JoTmwyd21KQk5XckQwY2s1cndITUFvR0NDcUdTTTQ5CkJBTUNBMGdBTUVVQ0lRQ1l3OHIyU25ueHB1K0ZzRXhhVzBsN0ZlbEo2SVp0cEt5RTlkMmpucGVDc2dJZ1lHMXMKYlo3RzkvRC9rN0QwdHFlMEF4Wkt4VGJzOHRmbkRjVHNXeVpmQzFvPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tChJGMEQCIGm60EQcYstr+iVPpwBNzs1/fBUBLtAMEs9IXL9IjtXXAiBk3kbmJXZeNyLhL6RgSfMcy8xXelwPUQK8MIK9xOuiGxKBBwq2BgoHT3JnMU1TUBKqBi0tLS0tQkVHSU4gQ0VSVElGSUNBVEUtLS0tLQpNSUlDS0RDQ0FjNmdBd0lCQWdJUU5XT1JXc09sSFR4V0lqcVpQSE5SaHpBS0JnZ3Foa2pPUFFRREFqQnpNUXN3CkNRWURWUVFHRXdKVlV6RVRNQkVHQTFVRUNCTUtRMkZzYVdadmNtNXBZVEVXTUJRR0ExVUVCeE1OVTJGdUlFWnkKWVc1amFYTmpiekVaTUJjR0ExVUVDaE1RYjNKbk1TNWxlR0Z0Y0d4bExtTnZiVEVjTUJvR0ExVUVBeE1UWTJFdQpiM0puTVM1bGVHRnRjR3hsTG1OdmJUQWVGdzB5TURBNE1EUXlNREl3TURCYUZ3MHpNREE0TURJeU1ESXdNREJhCk1Hb3hDekFKQmdOVkJBWVRBbFZUTVJNd0VRWURWUVFJRXdwRFlXeHBabTl5Ym1saE1SWXdGQVlEVlFRSEV3MVQKWVc0Z1JuSmhibU5wYzJOdk1RMHdDd1lEVlFRTEV3UndaV1Z5TVI4d0hRWURWUVFERXhad1pXVnlNQzV2Y21jeApMbVY0WVcxd2JHVXVZMjl0TUZrd0V3WUhLb1pJemowQ0FRWUlLb1pJemowREFRY0RRZ0FFdG9GL3ArdXZkZzlvCkY2Y29taVJBNjdnT3drV3ErazZURnlnZkJWWG1WZjFHZmNPd3RsenBocUJMQ1BTbzlpTndhS2lxRzdiUGl6N0QKWCtEa0JuSnc3Nk5OTUVzd0RnWURWUjBQQVFIL0JBUURBZ2VBTUF3R0ExVWRFd0VCL3dRQ01BQXdLd1lEVlIwagpCQ1F3SW9BZ1BHdmQ4cWR0TVJRcC85VklqMW0vMVZlSlNXVmIyQ2hHZ0pxQXRzNFdST3d3Q2dZSUtvWkl6ajBFCkF3SURTQUF3UlFJaEFPR0hVN08xaTVuZTdOckFhOEw4Kzh6dnRWYW5MZUpFSWxXVTRRNG53d2NvQWlCb3FUWW8Kd2Z2enBHczJhWG5JdTJPcDU0RjJ6Q3ZKRDc3cTI1dEdiajJwRlE9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tChJGMEQCIQCKWJUKr7l25IJDCdJF4nI5cVh6C6FEZ+kxztPTR7NkxwIfPwifSjMb3rSo+vlE2dHl/eOfocImlALzoLq/Ws4Bag=="
  },
  "tx_meta": {
    "block_hash": "ijtjaYe0rwXsiLpd+coM05GVEe5PmtYs7vJRVN7W9Z0=",
    "block_height": "31"
  }
}

# bad Response
# 输入不存在的交易哈希
$ curl -X 'GET' 'http://127.0.0.1:9091/v1/transaction/0xa56b0b7fcDBCea002D871173DAC1Ac9Dbc79FbB64e80915f7A7E9bE0414dC9bA' -H 'accept: application/json' | jq
  "error": "get transaction 0xa56b0b7fcDBCea002D871173DAC1Ac9Dbc79FbB64e80915f7A7E9bE0414dC9bA failed: not found in DB",
  "code": 2,
  "message": "get transaction 0xa56b0b7fcDBCea002D871173DAC1Ac9Dbc79FbB64e80915f7A7E9bE0414dC9bA failed: not found in DB"
```



### 发送只读交易

**基本信息**

只读交易并不会上链，适用于查询账本等操作。

<table>
    <tr>
        <td><b>接口地址</b></td>
        <td>/v1/view</td>
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

**请求参数**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | 交易具体内容 | Yes | [pbBxhTransaction](#pbbxhtransaction) |

**返回参数**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | A successful response. | [pbReceipt](#pbreceipt) |
| default | An unexpected error response. | [runtimeError](#runtimeerror) |



## 数据结构

### Event_EventType

| **Name**               | **Type** | **Description**                      |
| ---------------------- | -------- | ------------------------------------ |
| Event_OTHER            | int32    | 其他类型的事件                       |
| Event_INTERCHAIN       | int32    | 处理INTERCHAIN counter抛出的事件类型 |
| Event_NODEMGR          | int32    | 增删节点抛出的事件类型               |
| Event_WASM             | int32    | WASM抛出的事件类型                   |
| Event_AUDIT_PROPOSAL   | int32    | 审计提案管理抛出的事件类型           |
| Event_AUDIT_APPCHAIN   | int32    | 审计应用链管理抛出的事件类型         |
| Event_AUDIT_RULE       | int32    | 审计验证规则管理抛出的事件类型       |
| Event_AUDIT_SERVICE    | int32    | 审计服务管理抛出的事件类型           |
| Event_AUDIT_NODE       | int32    | 审计节点管理抛出的事件类型           |
| Event_AUDIT_ROLE       | int32    | 审计身份管理抛出的事件类型           |
| Event_AUDIT_INTERCHAIN | int32    | 审计中继链管理抛出的事件类型         |
| Event_AUDIT_DAPP       | int32    | 审计DAPP管理抛出的事件类型           |

### pbAuditSubscriptionRequestType

| **Name**                       | **Type** | **Description**  |
| ------------------------------ | -------- | ---------------- |
| pbAuditSubscriptionRequestType | string   | 默认为AUDIT_NODE |

### pbBlock

| **Name**     | **Type**                        | **Description** |
| ------------ |---------------------------------| --------------- |
| block_header | [pbBlockHeader](#pbblockheader) | 区块头          |
| transactions | byte                            | 交易列表        |
| block_hash   | byte                            | 交易哈希        |
| signature    | byte                            | 签名            |
| extra        | byte                            | 扩展字段        |

### pbBlockHeader

| **Name**     | **Type**        | **Description** |
| ------------ | --------------- | --------------- |
| number       | string (uint64) | 区块高度        |
| state_root   | byte            | 状态根          |
| tx_root      | byte            | 交易根          |
| receipt_root | byte            | 回执根          |
| timeout_root | byte            | 超时交易根      |
| parent_hash  | byte            | 父区块哈希      |
| timestamp    | string (int64)  | 时间戳          |
| version      | byte            | 版本号          |
| Bloom        | byte            | 过滤器          |

### pbBxhTransaction

| **Name**         | **Type**          | **Description**             |
| ---------------- |-------------------| --------------------------- |
| version          | byte              | 版本号                      |
| from             | byte              | 来源账户地址                |
| to               | byte              | 目的账户地址                |
| timestamp        | string (int64)    | 时间戳                      |
| transaction_hash | byte              | 交易哈希                    |
| payload          | byte              | 交易内容                    |
| IBTP             | [pbIBTP](#pbibtp) | IBTP内容                    |
| nonce            | string (uint64)   | 交易nonce                   |
| amount           | string            | Transfer交易的数值          |
| typ              | TxType            | 区分eth交易还是普通跨链交易 |
| signature        | byte              | 签名                        |
| extra            | byte              | 扩展字段                    |

### pbChainMeta

| **Name**            | **Type**        | **Description**    |
| ------------------- | --------------- | ------------------ |
| height              | string (uint64) | 当前最新区块高度   |
| block_hash          | byte            | 最新区块哈希       |
| interchain_tx_count | string (uint64) | interchain交易数目 |

### pbEmpty

| **Name** | **Type** | **Description** |
| -------- | -------- | --------------- |
| pbEmpty  | object   | 空              |

### pbEvent

| **Name**   | **Type**                            | **Description** |
| ---------- |-------------------------------------| --------------- |
| tx_hash    | byte                                | 交易哈希        |
| data       | byte                                | 事件内容        |
| event_type | [Event_EventType](#event_eventtype) | 事件类型        |

### pbEvmLog

| **Name**     | **Type**        | **Description**    |
| ------------ | --------------- | ------------------ |
| address      | byte            | 被调用的合约地址   |
| topics       | []byte          | 事件规则           |
| data         | byte            | 合约的返回值       |
| block_number | string (uint64) | 事件所在区块高度   |
| tx_hash      | byte            | 交易哈希           |
| tx_index     | string (uint64) | 交易在区块中的索引 |
| block_hash   | byte            | 区块哈希           |
| index        | string (uint64) | Log的索引          |
| removed      | boolean         | 是否被最长链替代   |

### pbGetBlockHeadersResponse

| **Name**     | **Type**                            | **Description** |
| ------------ |-------------------------------------| --------------- |
| blockHeaders | [ [pbBlockHeader](#pbblockheader) ] | 区块头集合      |

### pbGetBlockRequestType

| **Name**              | **Type** | **Description**                                |
| --------------------- | -------- | ---------------------------------------------- |
| pbGetBlockRequestType | string   | 获取区块的方式  0:通过区块高度  1:通过区块哈希 |

### pbGetBlocksResponse

| **Name** | **Type**                | **Description** |
| -------- |-------------------------| --------------- |
| blocks   | [ [pbBlock](#pbblock) ] | 区块集合        |

###  pbGetHappyBlocksResponse

| **Name** | **Type**                          | **Description**        |
| -------- |-----------------------------------| ---------------------- |
| blocks   | [ [pbHappyBlock](#pbhappyblock) ] | 交易分类输出的区块集合 |

### pbGetMultiSignsRequestType

| **Name**                   | **Type** | **Description**                                              |
| -------------------------- | -------- | ------------------------------------------------------------ |
| pbGetMultiSignsRequestType | string   | 0:"ASSET_EXCHANGE",  1:"IBTP_REQUEST",  2:"IBTP_RESPONSE",  3:"BLOCK_HEADER",  4:"BURN" |

### pbGetTransactionResponse

| **Name** | **Type**                                | **Description** |
| -------- |-----------------------------------------| --------------- |
| tx       | [pbBxhTransaction](#pbbxhtransaction)   | 跨链交易        |
| tx_meta  | [pbTransactionMeta](#pbtransactionMeta) | 交易的元数据    |

### pbHappyBlock

| **Name**     | **Type**                                  | **Description**                    |
| ------------ |-------------------------------------------| ---------------------------------- |
| block_header | [pbBlockHeader](#pbblockheader)           | 区块头                             |
| bxh_txs      | [ [pbBxhTransaction](#pbbxhtransaction) ] | 跨链交易集合                       |
| eth_txs      | [ byte ]                                  | Eth交易集合                        |
| block_hash   | byte                                      | 区块哈希                           |
| signature    | byte                                      | 签名                               |
| extra        | byte                                      | 扩展字段                           |
| index        | [ string (uint64) ]                       | 交易在区块索引  0:bxh_tx  1:eth_tx |

### pbIBTP

| **Name**      | **Type**                                | **Description**                               |
| ------------- |-----------------------------------------| --------------------------------------------- |
| from          | string                                  | 来源地址,数据结构：bxhId:appchainId:serviceId |
| to            | string                                  | 目的地址，数据结构同上                        |
| index         | string (uint64)                         | 该账户的交易索引                              |
| type          | [pbIBTPType](#pbibtptype)               | IBTP类型                                      |
| timeoutHeight | string (int64)                          | 超时高度                                      |
| proof         | byte                                    | 交易存在性与有效性证明                        |
| payload       | byte                                    | 交易具体内容                                  |
| group         | [pbStringUint64Map](#pbstringuint64map) | 一堆多跨链的目的地址集合                      |
| version       | string                                  | 版本号                                        |
| extra         | byte                                    | 扩展字段                                      |

### pbIBTPType

| **Name**   | **Type** | **Description**                                              |
| ---------- | -------- | ------------------------------------------------------------ |
| pbIBTPType | string   | 0: "INTERCHAIN",    1:  "RECEIPT_SUCCESS",    2:  "RECEIPT_FAILURE",    3:  "RECEIPT_ROLLBACK",    4:  "RECEIPT_ROLLBACK_END", |

### pbInterchainTxWrapper

| **Name**        | **Type**                          | **Description**    |
| --------------- |-----------------------------------| ------------------ |
| l2Roots         | [ byte ]                          | 默克尔根           |
| transactions    | [ [pbVerifiedTx](#pbverifiedtx) ] | 已验证的交易集合   |
| height          | string (uint64)                   | 所在区块高度       |
| timeout_l2Roots | [ byte ]                          | 超时交易的默克尔根 |
| timeout_ibtps   | [ string ]                        | 超时交易集合       |
| multi_tx_ibtps  | [ string ]                        | 一对多交易集合     |

### pbInterchainTxWrappers

| **Name**             | **Type**                                            | **Description**    |
| -------------------- |-----------------------------------------------------| ------------------ |
| interchainTxWrappers | [ [pbInterchainTxWrapper](#pbinterchaintxwrapper) ] | 已封装的交易家集合 |

### pbPierInfo

| **Name** | **Type**       | **Description**    |
| -------- | -------------- | ------------------ |
| address  | string         | 网关地址           |
| index    | string         | 主备网关编号       |
| timeout  | string (int64) | 发送心跳的时间间隔 |

### pbReceipt

| **Name**         | **Type**                            | **Description**           |
| ---------------- |-------------------------------------| ------------------------- |
| version          | byte                                | 版本号                    |
| tx_hash          | byte                                | 交易哈希                  |
| ret              | byte                                | 回执内容                  |
| status           | [pbReceiptStatus](#pbreceiptstatus) | 回执状态                  |
| events           | [ [pbEvent](#pbevent) ]             | 交易事件                  |
| gas_used         | string (uint64)                     | 花费的gas费               |
| evm_logs         | [ [pbEvmLog](#pbevmlog) ]           | Evm合约通过事件产生的日志 |
| bloom            | byte                                | 布隆过滤器                |
| contract_address | byte                                | 合约地址                  |
| tx_status        | pbTransactionStatus                 | 事务状态                  |

### pbTransactionStatus

| **Name**            | **Type** | **Description**                                              |
| ------------------- | -------- | ------------------------------------------------------------ |
| pbTransactionStatus | string   | BEGIN = 0;      BEGIN_FAILURE = 1;      BEGIN_ROLLBACK = 2;      SUCCESS = 3;      FAILURE = 4;      ROLLBACK = 5; |

 

### pbReceiptStatus

| **Name**        | **Type** | **Description**                   |
| --------------- | -------- | --------------------------------- |
| pbReceiptStatus | string   | 回执状态；  0：SUCCESS  1：FAILED |

### pbRequestType

| **Name**      | **Type** | **Description**                                     |
| ------------- | -------- | --------------------------------------------------- |
| pbRequestType | string   | 查询参数：  0:CHAIN_STATUS  1:NETWORK  2:VALIDATORS |

### pbResponse

| **Name** | **Type** | **Description** |
| -------- | -------- | --------------- |
| data     | byte     | 返回值          |

### pbSignResponse

| **Name** | **Type** | **Description** |
| -------- | -------- | --------------- |
| sign     | object   | 签名            |

### pbStringUint64Map

| **Name** | **Type**            | **Description**                        |
| -------- | ------------------- | -------------------------------------- |
| keys     | [ string ]          | 用于记录中继链的Counter，key为地址集合 |
| vals     | [ string (uint64) ] | Val为地址对应的counter                 |

### pbSubscriptionRequestType

| **Name**                  | **Type** | **Description**                                              |
| ------------------------- | -------- | ------------------------------------------------------------ |
| pbSubscriptionRequestType | string   | 订阅的数据结构：  0:  "BLOCK",  1:  "EVENT",  2:  "INTERCHAIN_TX",  3:  "BLOCK_HEADER",  4:  "INTERCHAIN_TX_WRAPPER",  5:  "UNION_INTERCHAIN_TX_WRAPPER",  6:  "EVM_LOG", |

### pbTransactionHashMsg

| **Name** | **Type** | **Description** |
| -------- | -------- | --------------- |
| tx_hash  | string   | 交易哈希        |

### pbTransactionMeta

| **Name**     | **Type**        | **Description**  |
| ------------ | --------------- | ---------------- |
| block_hash   | byte            | 交易所在区块哈希 |
| block_height | string (uint64) | 区块高度         |
| index        | string (uint64) | 交易索引         |

### pbVerifiedTx

| **Name** | **Type**                              | **Description**   |
| -------- |---------------------------------------| ----------------- |
| tx       | [pbBxhTransaction](#pbbxhtransaction) | 交易体            |
| valid    | boolean                               | proof是否验证通过 |

### protobufAny

| **Name** | **Type** | **Description** |
| -------- | -------- | --------------- |
| type_url | string   | 类型描述        |
| value    | byte     | 具体内容        |

### runtimeError

| **Name** | **Type**                        | **Description** |
| -------- |---------------------------------| --------------- |
| error    | string                          | 报错信息        |
| code     | integer                         | 错误码          |
| message  | string                          | 错误描述信息    |
| details  | [ [protobufAny](#protobufany) ] | 具体内容        |

### runtimeStreamError

| **Name**    | **Type**                        | **Description** |
| ----------- |---------------------------------| --------------- |
| grpc_code   | integer                         | Grpc错误码      |
| http_code   | integer                         | http错误码      |
| message     | string                          | 错误信息        |
| http_status | string                          | http错误信息    |
| details     | [ [protobufAny ](#protobufany)] | 具体内容        |

 

