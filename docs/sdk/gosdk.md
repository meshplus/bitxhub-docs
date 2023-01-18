# Go SDK

## 1 前言

此SDK文档面向BitXHub平台的应用开发者，提供BitXHub Go SDK的使用指南。

## 2 接口使用流程示例

### 2.1 基础流程示例

为了更好的理解接口的使用，本示例将从初始化Client，部署合约，调用合约和返回值解析这个大致流程作介绍，具体详细接口可参考第三章SDK文档。

#### 2.1.1 初始化Client

配置集群节点信息、日志、密钥、IPFS地址信息、grpc超时时间以及grpc连接池大小。

例如：

```go
privKey, err := asym.GenerateKeyPair(crypto.Secp256k1)
var cfg = &config{
    nodesInfo: []*NodeInfo{
        {Addr: "localhost:60011", EnableTLS: false, CertPath: "testdata/node1/certs/agency.cert", CommonName: "BitXHub", AccessCert: "testdata/node1/certs/gateway.cert", AccessKey: "testdata/node1/certs/gateway.priv"},
        {Addr: "localhost:60012", EnableTLS: true, CertPath: "testdata/node1/certs/agency.cert", CommonName: "BitXHub", AccessCert: "testdata/node2/certs/gateway.cert", AccessKey: "testdata/node2/certs/gateway.priv"},
        {Addr: "localhost:60013", EnableTLS: true, CertPath: "testdata/node3/certs/agency.cert", CommonName: "BitXHub", AccessCert: "testdata/node3/certs/gateway.cert", AccessKey: "testdata/node3/certs/gateway.priv"},
        {Addr: "localhost:60014", EnableTLS: true, CertPath: "testdata/node4/certs/agency.cert", CommonName: "BitXHub", AccessCert: "testdata/node4/certs/gateway.cert", AccessKey: "testdata/node4/certs/gateway.priv"},
    },
    logger: logrus.New(),
    privateKey: privKey,
    ipfsAddrs: []string{
        "http://localhost:5001"
    },
    timeoutLimit: time.Second * 6,
    poolSize: 6,
}
```

初始化Client，所有RPC操作将通过该接口与BitXHub交互。

例如：

```go
cli, err := New(
    WithNodesInfo(cfg.nodesInfo...),
    WithLogger(cfg.logger),
    WithPrivateKey(cfg.privateKey),
    WithIPFSInfo(cfg.ipfsAddrs...),
    WithTimeoutLimit(cfg.timeoutLimit),
    WithPoolSize(cfg.poolSize)
)
```

#### 2.1.2 部署合约

开发者需提供已经编译的`WebAssembly`文件。

例如：

```go
contract, err := ioutil.ReadFile("./testdata/example.wasm")
```

通过client部署合约，部署完成后可以获取合约地址`addr`。

例如：

```go
// 第二个参数为交易from、nonce、私钥等参数，为nil时会从client配置中自动获取
addr, err := cli.DeployContract(contract, nil)
```

#### 2.1.3 调用合约

调用合约需传入合约地址、合约方法名和对应的参数。

例如：

```go
// 参数：合约地址；方法名；交易nonce等参数；方法参数1；方法参数2
result, err := cli.InvokeXVMContract(addr, "method_name", nil, Int32(1), Int32(2))
```

#### 2.1.4 返回值解析

得到返回值结果后，获得状态码可以判断是否调用成功，若调用成功，解析返回值可看到调用之后的结果。

例如：

```go
if CheckReceipt(result) {
    fmt.Println(string(result.Ret))
}
```

#### 2.1.5 完整示例

```go
// 获取wasm合约字节数组
contract, _ := ioutil.ReadFile("./testdata/example.wasm")

// 部署合约，获取合约地址
addr, _ := cli.DeployContract(contract, nil)

// 调用合约，获取交易回执
result, _ := cli.InvokeXVMContract(addr, "method_name", nil, Int32(1), Int32(2))

// 判断合约调用交易成功与否，打印合约调用数据
if CheckReceipt(result) {
    fmt.Println(string(result.Ret))
}
```

### 2.2 应用链管理流程示例

本示例展示应用链管理流程中的注册、审核以及注销操作。

#### 2.2.1 应用链注册

调用BVM合约的`Register`方法，向BitXHub注册应用链。

例如：

```go
args := []*pb.Arg{
    rpcx.String(""), //validators
    rpcx.Int32(0), //consensus_type
    rpcx.String("hyperchain"), //chain_type
    rpcx.String("税务链"), //name
    rpcx.String("趣链税务链"), //desc
    rpcx.String("1.8"), //version
}

ret, err := cli.InvokeBVMContract(constant.InterchainContractAddr.Address(), "Register", nil, args...)
```

获取到成功的交易回执后，解析交易回执内容。

例如：

```go
{
    "id": "0x5098cc26b0d485145fb8258d2e79c49886cd4662", \\应用链ID
    "name": "税务链",
    "validators": "",
    "consensus_type": 0,
    "status": 0,
    "chain_type": "hyperchain",
    "desc": "趣链税务链",
    "version": "1.8"
}
```

#### 2.2.2 应用链审核

调用BVM合约的`Aduit`方法，向BitXHub审核应用链。

例如：

```go
args := []*pb.Arg{
    rpcx.String(appchainID),
    rpcx.Int32(1), //审核通过
    rpcx.String(""), //desc
}
ret, err = cli.InvokeBVMContract(constant.InterchainContractAddr.Address(),"Aduit", nil, args...)

```

#### 2.2.3 应用链注销

调用BVM合约的`DeleteAppchain`方法，向BitXHub注销应用链。

例如：

```go
ret, err = cli.InvokeBVMContract(constant.InterchainContractAddr.Address(), "DeleteAppchain", nil, rpcx.String(appchainID))
```

### 2.3 验证规则使用示例

本示例展示验证规则中的注册、审核操作，以及WebAssembly合约示例。

#### 2.3.1 验证规则注册

调用BVM合约的`RegisterRule`方法，向应用链注册验证规则（WebAssembly合约），这里我们需要先注册应用链和部署验证规则合约，然后获取应用链ID和合约地址。

例如：

```go
ret, err = cli.InvokeBVMContract(constant.RoleContractAddr.Address(), "RegisterRule", nil, rpcx.String(chainAddr), rpcx.String(contractAddr))
```

#### 2.3.2 验证规则审核

调用BVM合约的`Aduit`方法，向BitXHub审核验证规则。

例如：

```go
args := []*pb.Arg{
    rpcx.String(appchainID),
    rpcx.Int32(1), //审核通过
    rpcx.String(""), //desc
}
ret, err = cli.InvokeBVMContract(constant.RuleManagerContractAddr.Address(),"Aduit", nil, args...)

```

#### 2.3.3 验证规则示例（WebAssembly合约, Fabric实例）

```rust
extern crate protobuf;
extern crate sha2;


use crate::crypto::ecdsa;
use crate::model::transaction;
use sha2::{Digest, Sha256};


pub fn verify(proof: &[u8], validator: &[u8]) -> bool {
    let cap =
        protobuf::parse_from_bytes::<transaction::ChaincodeActionPayload>(proof).expect("error");
    let cap_act = cap.action.unwrap();
    let endorsers = cap_act.endorsements;

    let mut digest = Sha256::new();
    let mut payload = cap_act.proposal_response_payload.to_owned();

    payload.extend(&endorsers[0].endorser);
    digest.input(&payload);

    let digest_byte = digest.result();

    return ecdsa::verify(
        &endorsers[0].signature,
        &digest_byte,
        &validator,
        ecdsa::EcdsaAlgorithmn::P256,
    );
}
```

## 3 SDK文档

### 3.1 初始化和启动

#### 3.1.1 初始化Client

用途：调用该接口获取与中继链交互的Client。

参数：

- `opts` 是中继链的集群节点信息、日志、密钥、IPFS地址信息、grpc超时时间以及grpc连接池大小等。

```go
func New(opts ...Option) (*ChainClient, error)
```

#### 3.1.2 停止Client

用途：调用该接口将与中继链交互的`Client`关闭。

```go
func Stop() error
```

#### 3.1.3 设置Client私钥

用途：调用该接口将重新设置与中继链交互的Client的私钥。

参数：

- `key` Client私钥实例。

```go
func SetPrivateKey(key crypto.PrivateKey)
```

### 3.2 交易接口

#### 3.2.1 发送只读交易

用途：调用该接口向中继链发送只读交易，并返回交易回执。若交易是可写的，则该交易不会被执行并返回错误。

参数：

- `tx` 只读交易实例。

```go
func SendView(tx *pb.BxhTransaction) (*pb.Receipt, error)
```

#### 3.2.2 发送交易

用途：调用该接口向中继链发送交易，交易类型包括普通交易、跨链交易和智能合约。接口会对交易进行签名，签名所使用的私钥默认是客户端初始化时配置的私钥，或者也可以通过接口参数传入。

参数：

- `tx` 交易实例。
- `opts` 交易的from、nonce和签名私钥等参数。

```go
func SendTransaction(tx *pb.Transaction, opts *TransactOpts) (string, error)
```

#### 3.2.3 发送批量交易

用途：调用该接口向中继链发送批量的已签名交易。

参数：

- `txs` 批量交易实例。

```go
func SendTransactions(txs *pb.MultiTransaction) (*pb.MultiTransactionHash, error)
```

#### 3.2.4 发送交易并返回回执

用途：调用该接口向中继链发送交易并返回交易执行后的回执。

参数：

- `tx` 交易实例。
- `opts` 交易的from、nonce和签名私钥等参数。

```go
func SendTransactionWithReceipt(tx *pb.BxhTransaction, opts *TransactOpts) (*pb.Receipt, error)
```

#### 3.2.5 查询交易回执

用途：调用该接口向BitXHub查询交易回执，交易回执中的status字段标识交易是否成功。

参数：

- `hash` 交易哈希。

```go
func GetReceipt(hash string) (*pb.Receipt, error)
```

用例：

```go
func TestChainClient_SendTransactionWithReceipt(t *testing.T) {
    // 生成from私钥
    privKey, _ := asym.GenerateKeyPair(crypto.Secp256k1)

    // 生成to私钥
    toPrivKey, _ := asym.GenerateKeyPair(crypto.Secp256k1)

    // 配置Client
    cli, _ := New(
        WithNodesInfo(cfg.nodesInfo...),
        WithLogger(cfg.logger),
        WithPrivateKey(privKey),
    )

    // 获取from地址
    from, _ := privKey.PublicKey().Address()

    // 获取to地址
    to, _ := toPrivKey.PublicKey().Address()

    // 构建交易体
    data := &pb.TransactionData{
        Amount: "10",
    }
    payload, err := data.Marshal()
    require.Nil(t, err)
    tx := &pb.BxhTransaction{
        From:      from,
        To:        to,
        Payload:   payload,
        Timestamp: time.Now().UnixNano(),
    }

    // 用from的私钥签名交易
    _ = tx.Sign(privKey)

    // 通过client发送交易
    hash, _ := cli.SendTransaction(tx, nil)

    // 获取交易回执，判断交易执行状态
    ret, _ := cli.GetReceipt(hash)
    require.Equal(t, tx.Hash().String(), ret.TxHash.String())

    // 停止client
    _ = cli.Stop()
}
```

#### 3.2.6 查询交易

用途：调用该接口向BitXHub查询交易。

参数：

- `hash` 交易哈希。

```go
func GetTransaction(hash string) (*proto.GetTransactionResponse, error)
```

#### 3.2.7 生成跨链交易

用途：根据IBTP生成跨链交易。

参数：

- `IBTP` 跨链交易统一数据结构

```go
func GenerateIBTPTx(ibtp *pb.IBTP) (*pb.BxhTransaction, error)
```

### 3.3 合约接口

合约类型：

- BVM：BitXHub内置合约。
- XVM：WebAssembly合约。

#### 3.3.1 部署合约

用途：调用该接口向BitXHub部署XVM合约，返回合约地址。

参数：

- `contract` wasm合约编译后的字节数据。
- `opts` 交易的from、nonce和签名私钥等参数。

```go
func DeployContract(contract []byte, opts *TransactOpts) (contractAddr *types.Address, err error)
```

#### 3.3.2 调用合约

用途：该接口向中继链调用合约获取交易回执。

参数：

- `vmType` 合约类型：BVM和XVM。
- `address` 合约地址。
- `method` 合约方法。
- `opts` 交易的from、nonce和签名私钥等参数。
- `args` 合约方法参数。

```go
func InvokeContract(vmType pb.TransactionData_VMType, address types.Address, method string, opts *TransactOpts, args ...*pb.Arg) (*pb.Receipt, error)
```

调用XVM合约用例：

```go
func TestChainClient_InvokeXVMContract(t *testing.T) {
    privKey, err := asym.GenerateKeyPair(crypto.Secp256k1)
    require.Nil(t, err)

    cli, err := New(
        WithNodesInfo(cfg.nodesInfo...),
        WithLogger(cfg.logger),
        WithPrivateKey(privKey),
    )
    require.Nil(t, err)

    contract, err := ioutil.ReadFile("./testdata/example.wasm")
    require.Nil(t, err)

    addr, err := cli.DeployContract(contract, nil)
    require.Nil(t, err)

    result, err := cli.InvokeXVMContract(addr, "a", nil, Int32(1), Int32(2))
    require.Nil(t, err)
    require.Equal(t, "336", string(result.Ret))
}
```

调用BVM合约用例：

```go

func TestChainClient_InvokeBVMContract(t *testing.T) {
    privKey, err := asym.GenerateKeyPair(crypto.Secp256k1)
    require.Nil(t, err)

    cli, err := New(
        WithNodesInfo(cfg.nodesInfo...),
        WithLogger(cfg.logger),
        WithPrivateKey(privKey),
    )
    require.Nil(t, err)

    result, err := cli.InvokeBVMContract(constant.StoreContractAddr.Address(), "Set", nil, String("a"), String("10"))
    require.Nil(t, err)
    require.Nil(t, result.Ret)

    res, err := cli.InvokeBVMContract(constant.StoreContractAddr.Address(), "Get", nil, String("a"))
    require.Nil(t, err)
    require.Equal(t, string(res.Ret), "10")
}
```

#### 3.3.3 生成合约调用交易

用途：生成签名后的调用合约的交易。

参数：

- `vmType` 调用合约类型。
- `address` 调用合约地址。
- `method` 调用合约方法。
- `args` 调用合约方法的参数。

```go
func GenerateContractTx(vmType pb.TransactionData_VMType, address *types.Address, method string, args ...*pb.Arg) (*pb.BxhTransaction, error)
```

### 3.4 区块接口

#### 3.4.1 查询当前区块链信息

用途：获取当前链信息，返回的chainMeta中包含当前区块高度、当前区块哈希以及当前区块跨链交易数。

```go
func GetChainMeta() (*pb.ChainMeta, error)
```

#### 3.4.2 批量查询区块

用途：批量查询区块，返回指定块高度范围（start到end）的区块信息。

参数：

- `start` 指定范围的起始区块高度。
- `end` 指定范围的结束区块高度。

```go
func GetBlocks(start uint64, end uint64) (*pb.GetBlocksResponse, error)
```

#### 3.4.3 查询区块

用途：从中继链获取指定区块高度或区块哈希对应的区块信息。

参数：

- `value` 区块高度或者区块哈希。
- `blockType` 查询类型。

```go
func GetBlock(value string, blockType pb.GetBlockRequest_Type) (*pb.Block, error)
```

#### 3.4.4 查询区块链状态

用途：返回当前区块链共识的状态（正常或者不正常）。

```go
func GetChainStatus() (*pb.Response, error)
```

### 3.5 订阅接口

#### 3.5.1 订阅事件

用途：调用该接口向中继链发起订阅事件。

参数：

- `ctx` 上下文信息。
- `type` 事件类型，包含区块事件，区块头事件，跨链交易事件。
- `extra` 额外信息。

```go
func Subscribe(ctx context.Context, typ pb.SubscriptionRequest_Type, extra []byte) (<-chan interface{}, error)
```

用例：

```go
func TestChainClient_Subscribe(t *testing.T) {
    privKey, err := asym.GenerateKeyPair(crypto.Secp256k1)
    require.Nil(t, err)

    privKey1, err := asym.GenerateKeyPair(crypto.Secp256k1)
    require.Nil(t, err)

    from, err := privKey.PublicKey().Address()
    require.Nil(t, err)

    to, err := privKey1.PublicKey().Address()
    require.Nil(t, err)

    cli, err := New(
        WithNodesInfo(cfg.nodesInfo...),
        WithLogger(cfg.logger),
        WithPrivateKey(privKey),
    )
    require.Nil(t, err)

    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    c, err := cli.Subscribe(ctx, pb.SubscriptionRequest_BLOCK, nil)
    require.Nil(t, err)

    td := &pb.TransactionData{
        Amount: "10",
    }
    data, err := td.Marshal()
    require.Nil(t, err)

    go func() {
        tx := &pb.BxhTransaction{
            From:      from,
            To:        to,
            Payload:   data,
            Timestamp: time.Now().UnixNano(),
        }

        err = tx.Sign(privKey)
        require.Nil(t, err)

        hash, err := cli.SendTransaction(tx, nil)
        require.Nil(t, err)
        require.EqualValues(t, 66, len(hash))
    }()

    for {
        select {
        case block, ok := <-c:
            require.Equal(t, true, ok)
            require.NotNil(t, block)

            return
        case <-ctx.Done():
            return
        }
    }
}
```

#### 3.5.2 订阅审计事件

用途：调用该接口向中继链订阅审计事件。

参数：

- `ctx` 上下文信息。
- `typ` 事件类型，AuditSubscriptionRequest_AUDIT_NODE。
- `blockHeight` 订阅事件开始区块高度。
- `extra` 额外信息（nil）。

```go
func SubscribeAudit(ctx context.Context, typ pb.AuditSubscriptionRequest_Type, blockHeight uint64, extra []byte) (<-chan interface{}, error)
```

#### 3.5.3 获取InterchainTxWrapper

用途：获取指定区块高度范围的InterchainTxWrapper

参数：

- `ctx` 上下文信息。
- `pid` 应用链ID。
- `begin` 起始区块高度。
- `end` 结束区块高度。
- `ch` InterchainTxWrapper的通道。

```go
func GetInterchainTxWrappers(ctx context.Context, pid string, begin, end uint64, ch chan<- *pb.InterchainTxWrapper) error
```

#### 3.5.4 获取区块头

用途：获取指定块高度范围（start到end）的区块信息。

参数：

- `ctx` 上下文信息。
- `start` 指定范围的起始区块高度。
- `end` 指定范围的结束区块高度。
- `ch` 区块头通道。

```go
func GetBlockHeader(ctx context.Context, begin, end uint64, ch *chan<- *pb.BlockHeader) error
```

### 3.6 其它接口

#### 3.6.1 查询中继链验证者

用途：返回中继链创世管理员地址。

```go
func GetValidators() (*pb.Response, error)
```

#### 3.6.2 查询节点网络信息

用途：返回当前区块链网络的节点信息。

```go
func GetNetworkMeta() (*pb.Response, error)
```

#### 3.6.3 查询账户余额

参数：

- `address` 地址。

```go
func GetAccountBalance(address string) (*pb.Response, error)
```

#### 3.6.4 查询TPS

用途：获取指定块高度范围内的TPS。

参数：

- `start` 指定范围的起始区块高度。
- `end` 指定范围的结束区块高度。

```go
func GetTPS(begin, end uint64) (uint64, error)
```

#### 3.6.5 查询nonce

用途：获取指定账户下一笔交易的nonce。

参数：

- `account` 账户地址。

```go
func GetPendingNonceByAccount(account string) (uint64, error)
```

#### 3.6.6 查询BitXHub链ID

用途：获取中继链的链ID。

```go
func GetChainID() (uint64, error)
```

#### 3.6.7 上传文件

用途：上传本地文件到IPFS网络。

参数：

- `localPath` 本地文件路径。

```go
func IPFSPutFromLocal(localPath string) (*pb.Response, error)
```

#### 3.6.8 获取文件

用途：从IPFS网络获取指定文件。

参数：

- `path` IPFS网络中文件路径。

```go
func IPFSGet(path string) (*pb.Response, error)
```

#### 3.6.9 下载文件

用途：从IPFS网络下载指定文件到本地。

参数：

- `path` IPFS网络中文件路径。
- `localPath` 本地文件存储路径。

```go
func IPFSGetToLocal(path string, localPath string) (*pb.Response, error)
```

#### 3.6.10 查询多签

用途：调用该接口向中继链获取指定多签结果。

参数：

- `id` 指定id。
- `type` 指定类型，区块头、IBTP等。

```go
func GetMultiSigns(id string, typ pb.GetMultiSignsRequest_Type) (*pb.SignResponse, error)
```

#### 3.6.11 查询门限签名

用途：调用该接口向中继链获取指定门限签名结果。

参数：

- `id` 指定id。
- `type` 指定类型，区块头、IBTP等。
- `extra` 内部调用时传入的参数，传入空即可。

```go
func GetTssSigns(id string, typ pb.GetSignsRequest_Type, extra []byte) (*pb.SignResponse, error)
```

#### 3.6.12 检查当前pier是否为主节点

用途：检查当前pier是否为主节点。

参数：

- `address` pier的地址。

```go
func CheckMasterPier(address string) (*pb.Response, error)
```

#### 3.6.13 设置当前pier为主节点

用途：设置当前pier为主节点。

参数：

- `address` pier的地址。
- `index` pier的标识。
- `timeout` pier作为主节点的超时时间。

```go
func SetMasterPier(address string, index string, timeout int64) (*pb.Response, error)
```

#### 3.6.14 pier心跳通信

用途：pier主节点向BitXHub的心跳通信，用于更新主节点状态。

参数：

- `address` pier的地址。
- `index` pier的标识。

```go
func HeartBeat(address string, index string) (*pb.Response, error)
```
