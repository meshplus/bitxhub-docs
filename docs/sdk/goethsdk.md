# Go Eth SDK

## 1 前言

此SDK文档面向BitXHub平台的应用开发者，提供BitXHub Go ETH SDK的使用指南，该SDK主要用于调用BitXHub的ETH接口。

## 2 接口使用流程示例

### 2.1 合约部署调用流程示例

```go
// 获取用于交易签名的账户
account, err := utils.LoadAccount("./testdata/config")
require.Nil(t, err)

// 初始化客户端
client, err := New(
    WithUrls([]string{
        "http://localhost:8881",
        "http://localhost:8882",
        "http://localhost:8883",
        "http://localhost:8884",
    }),
)
require.Nil(t, err)

// 编译合约
result, err := client.Compile("./testdata/storage.sol")
require.Nil(t, err)

// 部署合约
addresses, err := client.Deploy(account.PrivateKey, result, nil)
require.Nil(t, err)
require.Equal(t, 1, len(addresses))

// 等待交易上链
time.Sleep(time.Second)

// 构造参数
contractAbi, err := utils.LoadAbi("./testdata/storage.abi")
require.Nil(t, err)
args, err := utils.Decode(&contractAbi, "store", "2")
require.Nil(t, err)

// 调用合约方法store
_, err = client.Invoke(account.PrivateKey, &contractAbi, addresses[0], "store", args)
require.Nil(t, err)

time.Sleep(time.Second)

// 调用合约方法retrieve，检查store方法是否调用成功
res, err := client.Invoke(account.PrivateKey, &contractAbi, addresses[0], "retrieve", nil)
require.Nil(t, err)
// 解析返回值
v, ok := res[0].(*big.Int)
require.Equal(t, true, ok)
require.Equal(t, "2", v.String())
```

### 2.2 发送交易并获取回执流程示例

```go
// 获取用于交易签名的账户
account, err := utils.LoadAccount("./testdata/config")
require.Nil(t, err)

// 初始化客户端
client, err := New(
    WithUrls([]string{
        "http://localhost:8881",
        "http://localhost:8882",
        "http://localhost:8883",
        "http://localhost:8884",
    }),
)
require.Nil(t, err)

// 获取nonce
nonce, err := client.EthGetTransactionCount(account.Address, nil)
require.Nil(t, err)

// 获取gasPrice
price, err := client.EthGasPrice()
require.Nil(t, err)

// 生成一个账户地址，用于测试
pk, err := crypto.GenerateKey()
require.Nil(t, err)

// 构建交易
tx := utils.NewTransaction(nonce, crypto.PubkeyToAddress(pk.PublicKey), uint64(10000000), price, nil, big.NewInt(1))

// 发送交易
hash, err := client.EthSendTransaction(account.PrivateKey, tx)
require.Nil(t, err)

// 获取回执
receipt, err := client.EthGetTransactionReceipt(hash)
require.Nil(t, err)
require.Equal(t, types.ReceiptStatusSuccessful, receipt.Status)
```

## 3 SDK文档

### 3.1 初始化和停止

#### 3.1.1 初始化Client

用途：调用该接口获取与中继链交互的Client。

参数：

- `opts` 是中继链节点URL、用于交易签名的密钥、以及连接池大小等。

```go
func New(opts ...Option) (*EthRPC, error)
```

示例：

```go
client, err := New(
    WithUrls([]string{
        "http://localhost:8881",
        "http://localhost:8882",
        "http://localhost:8883",
        "http://localhost:8884",
    }),
    WithPoolSize(10),
)
```

#### 3.1.2 停止Client

用途：调用该接口将与中继链连接的`Client`关闭。

```go
func Stop()
```

### 3.2 交易接口

#### 3.2.1 发送交易

用途：发送交易。

参数：

- `privKey` 用于交易签名的私钥。
- `transaction` 交易实例，结构信息定义在以太坊库中。

```go
func EthSendTransaction(privKey *ecdsa.PrivateKey, transaction *types.Transaction) (common.Hash, error)
```

#### 3.2.2 发送签名交易

用途：发送签名交易。

参数：

- `transaction` 已签名的交易实例。

```go
func EthSendRawTransaction(transaction *types.Transaction) (common.Hash, error)
```

示例：

```go
func TestEthSendRawTransaction(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    nonce, err := client.EthGetTransactionCount(account.Address, nil)
    require.Nil(t, err)
    price, err := client.EthGasPrice()
    require.Nil(t, err)
    pk, err := crypto.GenerateKey()
    require.Nil(t, err)
    tx := utils.NewTransaction(nonce, crypto.PubkeyToAddress(pk.PublicKey), uint64(10000000), price, nil, big.NewInt(1))
    signTx, err := types.SignTx(tx, types.NewEIP155Signer(client.EthGetChainId()), account.PrivateKey)
    require.Nil(t, err)
    txHash, err := client.EthSendRawTransaction(signTx)
    require.Nil(t, err)
    receipt, err := client.EthGetTransactionReceipt(txHash)
    require.Nil(t, err)
    require.Equal(t, types.ReceiptStatusSuccessful, receipt.Status)
}
```

#### 3.2.3 发送交易并获取回执

用途：发送交易并获取回执。

参数：

- `privKey` 用于交易签名的私钥。
- `transaction` 交易实例，结构信息定义在以太坊库中。

```go
func EthSendTransactionWithReceipt(privKey *ecdsa.PrivateKey, transaction *types.Transaction) (*types.Receipt, error)
```

#### 3.2.4 发送签名交易并获取回执

用途：发送签名交易并获取回执。

参数：

- `transaction` 已签名的交易实例。

```go
func EthSendRawTransactionWithReceipt(transaction *types.Transaction) (*types.Receipt, error)
```

#### 3.2.5 查询交易回执

用途：查询交易回执。

参数：

- `hash` 交易哈希。

```go
func EthGetTransactionReceipt(hash common.Hash) (*types.Receipt, error)
```

#### 3.2.6 通过哈希查询交易

用途：通过哈希查询交易。

参数：

- `txHash` 交易哈希。

```go
func EthGetTransactionByHash(txHash common.Hash) (*types.Transaction, error)
```

示例：

```go
func TestEthGetTransactionByHash(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    nonce, err := client.EthGetTransactionCount(account.Address, nil)
    require.Nil(t, err)
    price, err := client.EthGasPrice()
    require.Nil(t, err)
    pk, err := crypto.GenerateKey()
    require.Nil(t, err)
    tx := utils.NewTransaction(nonce, crypto.PubkeyToAddress(pk.PublicKey), uint64(10000000), price, nil, big.NewInt(1))
    receipt, err := client.EthSendTransactionWithReceipt(account.PrivateKey, tx)
    require.Nil(t, err)

    actualTx, err := client.EthGetTransactionByHash(receipt.TxHash)
    require.Nil(t, err)
    reflect.DeepEqual(tx, actualTx)
}
```

#### 3.2.7 通过区块哈希和索引查询交易

用途：通过区块哈希和索引查询交易。

参数：

- `blockHash` 区块哈希。
- `index` 交易在区块中的索引。

```go
func EthGetTransactionByBlockHashAndIndex(blockHash common.Hash, index int) (*types.Transaction, error)
```

示例：

```go
func TestEthGetTransactionByBlockHashAndIndex(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    nonce, err := client.EthGetTransactionCount(account.Address, nil)
    require.Nil(t, err)
    price, err := client.EthGasPrice()
    require.Nil(t, err)
    pk, err := crypto.GenerateKey()
    require.Nil(t, err)
    tx := utils.NewTransaction(nonce, crypto.PubkeyToAddress(pk.PublicKey), uint64(10000000), price, nil, big.NewInt(1))
    receipt, err := client.EthSendTransactionWithReceipt(account.PrivateKey, tx)
    require.Nil(t, err)

    actualTx, err := client.EthGetTransactionByBlockHashAndIndex(receipt.BlockHash, int(receipt.TransactionIndex))
    require.Nil(t, err)
    reflect.DeepEqual(tx, actualTx)
}
```

#### 3.2.8 通过区块号和索引查询交易

用途：通过区块号和索引查询交易。

参数：

- `blockNumber` 区块号。
- `index` 交易在区块中的索引。

```go
func EthGetTransactionByBlockNumberAndIndex(blockNumber *big.Int, index int) (*types.Transaction, error)
```

#### 3.2.9 估算交易gas

用途：估算交易gas。

参数：

- `msg` 交易数据信息。

```go
func EthEstimateGas(msg ethereum.CallMsg) (uint64, error)
```

示例：

```go
func TestEthEstimateGas(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    price, err := client.EthGasPrice()
    require.Nil(t, err)
    to := common.HexToAddress("0xeedFef830c6FBDDA3257AC883126995702F0eea3")
    msg := ethereum.CallMsg{
        From:     account.Address,
        To:       &to,
        GasPrice: price,
    }
    gas, err := client.EthEstimateGas(msg)
    require.Nil(t, err)
    require.Equal(t, uint64(21000), gas)
}
```

### 3.3 合约接口

#### 3.3.1 编译合约

用途：编译合约。

参数：

- `sourceFiles` solidity合约文件。

```go
func Compile(sourceFiles ...string) (*CompileResult, error)
```

#### 3.3.2 部署合约

用途：部署合约。

参数：

- `privKey` 用于交易签名的私钥。
- `result` 调用Compile接口的返回结果。
- `args` 合约构造函数的参数。
- `opts` 交易可选配置，比如：nonce、gasPrice、gasLimit等。

```go
func Deploy(privKey *ecdsa.PrivateKey, result *CompileResult, args []interface{}, opts ...TransactionOption) ([]string, error)
```

#### 3.3.3 通过字节码部署合约

用途：通过字节码部署合约。

参数：

- `privKey` 用于交易签名的私钥。
- `abi` 合约abi信息。
- `code` 合约字节码。
- `args` 合约构造函数的参数。
- `opts` 交易可选配置，比如：nonce、gasPrice、gasLimit等。

```go
func DeployByCode(privKey *ecdsa.PrivateKey, abi abi.ABI, code string, args []interface{}, opts ...TransactionOption) (string, uint64, error)
```

示例：

```go
func TestDeployByCode(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    file, err := ioutil.ReadFile("./testdata/data.abi")
    assert.Nil(t, err)
    abi, err := abi.JSON(bytes.NewReader(file))
    require.Nil(t, err)
    code, err := ioutil.ReadFile("./testdata/data.bin")
    require.Nil(t, err)
    address, blockNum, err := client.DeployByCode(account.PrivateKey, abi, string(code), nil)
    require.Nil(t, err)
    require.NotNil(t, address)
    latestNum, err := client.EthGetBlockByNumber(nil, true)
    require.Nil(t, err)
    require.Equal(t, 0, latestNum.Number().Cmp(big.NewInt(int64(blockNum))))
}
```

#### 3.3.4 调用合约

用途：调用合约方法，会根据方法类型（只读或写），采取不同操作。

参数：

- `privKey` 用于交易签名的私钥。
- `contractAbi` 合约abi信息。
- `address` 合约地址。
- `method` 合约方法名。
- `args` 合约构造函数的参数。
- `opts` 交易可选配置，比如：nonce、gasPrice、gasLimit等。

```go
func Invoke(privKey *ecdsa.PrivateKey, contractAbi *abi.ABI, address string, method string, args []interface{}, opts ...TransactionOption) ([]interface{}, error)
```

#### 3.3.5 调用合约只读方法

用途：调用合约只读方法。

参数：

- `contractAbi` 合约abi信息。
- `address` 合约地址。
- `method` 合约方法名。
- `args` 合约构造函数的参数。

```go
func EthCall(contractAbi *abi.ABI, address string, method string, args []interface{}) ([]interface{}, error)
```

示例：

```go
func TestEthCall(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    result, err := client.Compile("./testdata/storage.sol")
    require.Nil(t, err)
    addresses, err := client.Deploy(account.PrivateKey, result, nil)
    require.Nil(t, err)
    require.Equal(t, 1, len(addresses))

    time.Sleep(time.Second)
    contractAbi, err := utils.LoadAbi("./testdata/storage.abi")
    require.Nil(t, err)
    args, err := utils.Decode(&contractAbi, "store", "5")
    require.Nil(t, err)
    _, err = client.Invoke(account.PrivateKey, &contractAbi, addresses[0], "store", args)
    require.Nil(t, err)

    time.Sleep(time.Second)
    callRes, err := client.EthCall(&contractAbi, addresses[0], "retrieve", nil)
    require.Nil(t, err)
    v, ok := callRes[0].(*big.Int)
    require.Equal(t, true, ok)
    require.Equal(t, "5", v.String())
}
```

### 3.4 区块接口

#### 3.4.1 通过区块号查询区块

用途：通过区块号查询区块，可选择是否返回区块中的全部交易。

参数：

- `blockNumber` 区块号，传入nil则返回最新区块。
- `fullTx` 区块数据中是否包含区块的全部交易。

```go
func EthGetBlockByNumber(blockNumber *big.Int, fullTx bool) (*types.Block, error)
```

示例：

```go
func TestEthGetBlockByNumber(t *testing.T) {
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    block, err := client.EthGetBlockByNumber(nil, true)
    require.Nil(t, err)
}
```

#### 3.4.2 通过区块哈希查询区块中交易数量

用途：通过区块哈希查询区块中交易数量。

参数：

- `hash` 区块哈希。

```go
func EthGetBlockTransactionCountByHash(hash common.Hash) (uint64, error)
```

示例：

```go
func TestEthGetBlockTransactionCountByHash(t *testing.T) {
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    block, err := client.EthGetBlockByNumber(nil, true)
    require.Nil(t, err)
    blockHash := block.Hash()
    _, err = client.EthGetBlockTransactionCountByHash(blockHash)
    require.Nil(t, err)
}
```

#### 3.4.3 通过区块号查询区块中交易数量

用途：通过区块号查询区块中交易数量。

参数：

- `blockNumber` 区块号。

```go
func EthGetBlockTransactionCountByNumber(blockNumber *big.Int) (uint64, error)
```

### 3.5 其它接口

#### 3.5.1 查询gas单价

用途：查询gas单价。

```go
func EthGasPrice() (*big.Int, error)
```

#### 3.5.2 查询账户Nonce

用途：查询账户在指定区块下的Nonce。

参数：

- `account` 账户地址。
- `blockNumber` 区块号，传入nil则是最新区块，即返回账户最新nonce。

```go
func EthGetTransactionCount(account common.Address, blockNumber *big.Int) (uint64, error)
```

```go
func TestEthGetTransactionCount(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    nonce, err := client.EthGetTransactionCount(account.Address, nil)
    require.Nil(t, err)
    require.NotNil(t, nonce)
}
```

#### 3.5.3 查询账户余额

用途：查询账户在指定区块下的余额。

参数：

- `account` 账户地址。
- `blockNumber` 区块号，传入nil则是最新区块，即返回账户最新余额。

```go
func EthGetBalance(account common.Address, blockNumber *big.Int) (*big.Int, error)
```

#### 3.5.4 查询账户Code

用途：查询合约账户在指定区块下的字节码。

参数：

- `account` 账户地址。
- `blockNumber` 区块号，传入nil则是最新区块。

```go
func EthGetCode(account common.Address, blockNumber *big.Int) (string, error)
```

示例：

```go
func TestEthCode(t *testing.T) {
    account, err := utils.LoadAccount("./testdata/config")
    require.Nil(t, err)
    client, err := New(
        WithUrls([]string{
            "http://localhost:8881",
            "http://localhost:8882",
            "http://localhost:8883",
            "http://localhost:8884",
        }),
    )
    require.Nil(t, err)
    result, err := client.Compile("./testdata/storage.sol")
    require.Nil(t, err)
    addresses, err := client.Deploy(account.PrivateKey, result, nil)
    require.Nil(t, err)
    require.Equal(t, 1, len(addresses))
    code, err := client.EthGetCode(common.HexToAddress(addresses[0]), nil)
    require.Nil(t, err)
    require.NotNil(t, code)

    emptyCode, err := client.EthGetCode(account.Address, nil)
    require.Nil(t, err)
    require.Equal(t, "0x", emptyCode)
}
```

#### 3.5.5 查询ChainID

用途：查询ChainID。

```go
func EthGetChainId() *big.Int
```
