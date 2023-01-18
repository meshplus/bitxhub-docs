# 应用链插件编写

在本教程中，你将构建一个完整功能的 Plugin。过程中能学习基本的概念和具体使用细节。该示例将展示如何快速、轻松地接入自己的区块链到跨链平台中来。

如果你需要接入自己开发的区块链到BitXHub跨链平台来的话，可以根据你们自己的区块链来定制开发Plugin，通过跨链网关来加入到跨链平台。

## 开发要求

- 安装 [__go1.13+__](https://golang.org/doc/install)

- 设置好$GOPATH等环境

## 1. 重要概念

在解释具体的接口之前，先明确几个概念：

- **跨链请求**：如果有两条区块链A和B，A链需要向B链发起任何操作，需要按照IBTP规则向中继链发出一个请求包，我们称之为跨链请求A->B；
- **IBTP包**：满足IBTP的一个package，跨链请求都需要通过IBTP包进行；
- **来源链**：在跨链请求A->B中，A即为来源链服务；
- **目的链**：在跨链请求A->B中，B即为目的链服务。

## 3. Plugin接口

为了更加便捷的开发Plugin接入到Pier中来，我们规定了下面一些必要的接口。

```go
type Client interface {
	
    // 初始化Plugin服务。
    Initialize(configPath string, extra []byte, mode string) error
    
    // 启动Plugin服务的接口。
    Start() error

    // 停止Plugin服务的接口。
    Stop() error

    // Plugin负责将区块链上产生的跨链事件转化为标准的IBTP格式，Pier通过GetIBTP接口获取跨链请求再进行处理。
    GetIBTPCh() chan *pb.IBTP
    
    // Plugin负责将区块链上信任根变更的信息传递给中继链。
    GetUpdateMeta() chan *pb.UpdateMeta

    // Plugin 负责执行来源链过来的跨链请求，Pier调用SubmitIBTP提交收到的跨链请求。
    // from: 来源链服务ID
    // index: 跨链交易索引
    // serviceID: 目的链服务ID
    // ibtpType: 跨链交易类型<IBTP_INTERCHAIN, IBTP_RECEIPT_SUCCESS, IBTP_RECEIPT_FAILURE, IBTP_RECEIPT_ROLLBACK>
    // content: 跨链调用内容编码
    // proof: 中继链多签数据
    // isEncrypted: content.payload是否加密标识列表
    SubmitIBTP(from string, index uint64, serviceID string, ibtpType pb.IBTP_Type, content *pb.Content, proof *pb.BxhProof, isEncrypted bool) (*pb.SubmitIBTPResponse, error)

    // Plugin 负责执行来源链过来的跨链请求，Pier针对简单事务调用SubmitIBTPBatch批量提交收到的跨链请求。
    // from: 来源链服务ID列表
    // index: 跨链交易索引列表
    // serviceID: 目的链服务ID列表
    // ibtpType: 跨链交易类型列表<IBTP_INTERCHAIN, IBTP_RECEIPT_SUCCESS, IBTP_RECEIPT_FAILURE, IBTP_RECEIPT_ROLLBACK>
    // content: 跨链调用内容编码列表
    // proof: 中继链多签数据列表
    // isEncrypted: content.payload是否加密标识列表
    SubmitIBTPBatch(from []string, index []uint64, serviceID []string, ibtpType []pb.IBTP_Type, content []*pb.Content, proof []*pb.BxhProof, isEncrypted []bool) (*pb.SubmitIBTPResponse, error)

    // Plugin 负责执行来源链过来的跨链请求，Pier调用SubmitIBTP提交收到的跨链请求。
    // to: 目的链服务ID
    // index: 跨链交易索引
    // serviceID: 来源链服务ID
    // ibtpType: 跨链交易类型<IBTP_INTERCHAIN, IBTP_RECEIPT_SUCCESS, IBTP_RECEIPT_FAILURE, IBTP_RECEIPT_ROLLBACK>
    // result: 跨链调用结果编码
    // proof: 中继链多签数据
    SubmitReceipt(to string, index uint64, serviceID string, ibtpType pb.IBTP_Type, result *pb.Result, proof *pb.BxhProof) (*pb.SubmitIBTPResponse, error)

    // Plugin 负责执行来源链过来的跨链请求，Pier针对简单事务，调用SubmitReceiptBatch批量提交收到的跨链请求。
    // 目前未启用
    SubmitReceipt(to string, index uint64, serviceID string, ibtpType pb.IBTP_Type, result *pb.Result, proof *pb.BxhProof) (*pb.SubmitIBTPResponse, error)

    // GetOutMessage 负责在跨链合约中查询历史跨链请求。查询键值中servicePair指定服务对，idx指定序号，查询结果为以Plugin负责的区块链作为来源链的跨链请求。
    GetOutMessage(servicePair string, idx uint64) (*pb.IBTP, error)

    // GetReceiptMessage 负责在跨链合约中查询历史跨链回执。查询键值中servicePair指定服务对，idx指定序号，查询结果为以Plugin负责的区块链作为目的链的跨链回执。
    GetReceiptMessage(servicePair string, idx uint64) (*pb.IBTP, error)

    // GetInMeta 是获取跨链回执相关的Meta信息的接口。以Plugin负责的区块链为目的链服务的一系列跨链请求的序号信息。如果Plugin负责A链服务，则A可能和多条链服务进行跨链，如A->B:3; A->C:5。返回的map中，key值为服务对（来源链服务ID+目的链服务ID），value对应已发送到该目的链的最新跨链请求的序号，如{A+B:3, A+C:5}。
    GetInMeta() (map[string]uint64, error

    // GetOutMeta 是获取跨链请求相关的Meta信息的接口。以Plugin负责的区块链为来源链服务的一系列跨链请求的序号信息。如果Plugin负责A链服务，则A可能和多条链服务进行跨链，如A->B:3; A->C:5。返回的map中，key值为服务对（来源链服务ID+目的链服务ID），value对应已发送到该目的链的最新跨链请求的序号，如{A+B:3, A+C:5}。
    GetOutMeta() (map[string]uint64, error)
   
    // GetCallbackMeta 是获取跨链请求相关的Meta信息的接口。
    GetCallbackMeta() (map[string]uint64, error)
    
    // GetDstRollbackMeta 是获取最新已回滚交易的序号的接口。
    GetDstRollbackMeta() (map[string]uint64, error)
    
    // GetDirectTransactionMeta 是直连模式下返回对应交易的事务信息的接口。
    GetDirectTransactionMeta(string) (uint64, uint64, uint64, error)
    
    // GetServices 是获取跨链管理合约中所有合法服务的信息的接口。
    GetServices() ([]string, error)
    
    // GetChainID 是获取跨链管理合约中设置的chainID与BitxhubID的接口。
    GetChainID() (string, string, error)
    
    // GetAppchainInfo 是直连模式下获取已注册的目的链服务的信息的接口。
    GetAppchainInfo(chainID string) (string, []byte, string, error)
    
    // GetOffChainData 是链下文件传输时获取指定文件信息的接口。
    GetOffChainData(request *pb.GetDataRequest) (*pb.OffChainDataInfo, error)
    
    // Plugin负责识别链下文件传输标识，并生成对应链下文件传输请求，Pier通过GetOffChainDataReq接口获取链下文件传输请求再进行处理。
    GetOffChainDataReq() chan *pb.GetDataRequest
    
    // SubmitOffChainData 是链下文件传输时处理指定文件的接口。
    SubmitOffChainData(response *pb.GetDataResponse) error

    // Name 描述Plugin负责的区块链的自定义名称，一般和业务相关，如司法链等。
    Name() string

    // Type 描述Plugin负责的区块链类型，比如Fabric。
    Type() string
}
```

## 3. 程序目的

本教程以开发一个简单的连接Fabric区块链网络的Plugin为例，最终的程序能够实现从负责的区块链获取`Hello World`信息并返回到跨链平台中。

## 4. 开始编写你的程序

首先选择你的工程目录，按照正常的GO程序的流程建立项目

```shell
$ go version // 确认你安装的GO版本
$ mkdir ${YOUR_PROJECT}
$ cd ${YOUR_PROJECT}
$ go mod init exmple/fabric-plugin
```

### 4.1 读取配置

Plugin的配置文件路径是通过Initialize的方法动态传入的，这意味着你可以方便的修改关于你的区块链的参数信息。我们新建文件 `./config.go` 文件，负责配置读取的所有操作。

这里使用的是 `github.com/spf13/viper`库和TOML文件作为配置，当然你也可以使用任何你熟悉的工具来读取配置。

```go
package main

import (
    "path/filepath"
    "strings"

    "github.com/spf13/viper"
)

const (
    ConfigName = "fabric.toml"
)

type Config struct {
    Fabric   Fabric    `toml:"fabric" json:"fabric"`
    Services []Service `mapstructure:"services" json:"services"`
}
type Fabric struct {
    Name          string `toml:"name" json:"name"`
    Username      string `toml:"username" json:"username"`
    CCID          string `toml:"ccid" json:"ccid"`
    ChannelId     string `mapstructure:"channel_id" toml:"channel_id" json:"channel_id"`
    Org           string `toml:"org" json:"org"`
    ServerPort    string `toml:"server_port" json:"server_port"`
    TimeoutHeight int64  `mapstructure:"timeout_height" json:"timeout_height"`
    TimeoutPeriod uint64 `mapstructure:"timeout_period" json:"timeout_period"`
}

type Service struct {
    ID   string `toml:"id" json:"id"`
    Name string `toml:"name" json:"name"`
    Type string `toml:"type" json:"type"`
}

func DefaultConfig() *Config {
    return &Config{
    	Fabric: Fabric{
    		Name:          "fabric",
    		Username:      "Admin",
    		CCID:          "broker",
    		ChannelId:     "mychannel",
    		Org:           "org2",
    		TimeoutHeight: 30,
    		TimeoutPeriod: 60,
    	},
    	Services: nil,
    }
}

func UnmarshalConfig(configPath string) (*Config, error) {
    viper.SetConfigFile(filepath.Join(configPath, ConfigName))
    viper.SetConfigType("toml")
    viper.AutomaticEnv()
    viper.SetEnvPrefix("FABRIC")
    replacer := strings.NewReplacer(".", "_")
    viper.SetEnvKeyReplacer(replacer)
    if err := viper.ReadInConfig(); err != nil {
    	return nil, err
    }

    config := DefaultConfig()

    if err := viper.Unmarshal(config); err != nil {
    	return nil, err
    }

    return config, nil
}
```

### 

### 4.2 Client对象

首先创建一个`client.go`文件，这个文件是Plugin的核心和入口。

在该文件中，应该定义你的Plugin如何获取client 实例，以及如何启动和停止Plugin服务。

现在我们需要创建一个自定义的Client 结构，跨链网关最终拿到的应该是这个结构的一个实例，先来看看这个结构中都需要什么。

首先来看看`Client自定义`具体结构：

```go
type ContractMeta struct {
    EventFilter string `json:"event_filter"`
    Username    string `json:"username"`
    CCID        string `json:"ccid"`
    ChannelID   string `json:"channel_id"`
    ORG         string `json:"org"`
}

type Client struct {
    meta          *ContractMeta
    consumer      *Consumer
    eventC        chan *pb.IBTP
    appchainID    string
    bitxhubID     string
    name          string
    serviceMeta   map[string]*pb.Interchain
    ticker        *time.Ticker
    done          chan bool
    timeoutHeight int64
    config        *Config
}
```

- `meta`：Plugin直接和跨链合约交互，需要保存你的合约的一些基础信息。由于我们需要连接一个Fabric网络，这些Meta信息包括 **Fabric中跨链事件的名称、Fabric中的用户名称、Chaincode合约的名称、你的组织名称Org以及组织所在的channel；**

- `consumer`：可以理解为Fabric上跨链事件的“监听器”，这个监听器也是一个自定义的结构，具体的结构在后面会详细介绍；

- `eventC`：为跨链网关提供读取监听到的跨链事件的通道；

- `name`：自定的区块链的名称；

- `appchainID`：跨链网关注册在跨链平台中后产生的唯一ID，作为应用链的标识。

- `bitxhubID`: 跨链网关所连接的中继链的唯一ID。

- `timeoutHeight`: 跨链交易超时块高，中继链在出块该值后未收到目的链回执便会触发超时回滚。

然后应该提供一个Client的实例化的接口（类似于构造函数），具体代码如下：

```go
func (c *Client) Initialize(configPath string, extra []byte, mode string) error {
    eventC := make(chan *pb.IBTP)
    config, err := UnmarshalConfig(configPath)
    if err != nil {
    	return fmt.Errorf("unmarshal config for plugin :%w", err)
    }
    fabricConfig := config.Fabric
    contractmeta := &ContractMeta{
    	Username:  fabricConfig.Username,
    	CCID:      fabricConfig.CCID,
    	ChannelID: fabricConfig.ChannelId,
    	ORG:       fabricConfig.Org,
    }

    m := make(map[string]*pb.Interchain)

    mgh, err := newFabricHandler(contractmeta.EventFilter, eventC)
    if err != nil {
    	return err
    }

    done := make(chan bool)
    csm, err := NewConsumer(configPath, contractmeta, mgh, done)
    if err != nil {
    	return err
    }

    c.consumer = csm
    c.eventC = eventC
    c.meta = contractmeta
    c.name = fabricConfig.Name
    c.serviceMeta = m
    c.ticker = time.NewTicker(2 * time.Second)
    c.done = done
    c.timeoutHeight = fabricConfig.TimeoutHeight
    c.config = config
    c.appchainID = ""
    c.bitxhubID = ""
    return nil
}
```

### 4.3 consumer

consumer 负责监听区块链上的由跨链合约抛出的跨链事件以及和调用chaincode。

我们新建 `./consumer.go` 文件

```go
type Consumer struct {
   eventClient     *event.Client
   meta            *ContractMeta
   msgH            MessageHandler
   channelProvider context.ChannelProvider
   ChannelClient   *channel.Client
   registration    fab.Registration
   ctx             chan bool
}
```

- `eventClient`：fabric gosdk提供的事件Client；

- `meta Fabric`：相关的参数信息；

- `msgH`：事件handler，在监听到指定事件之后负责处理的函数；

- `channelProvider`：fabric gosdk提供的和chaincode交互；

- `ChannelClient`：fabric gosdk 提供的和调用chaincode的对象；

- `registeration`：fabric gosdk 提供的订阅特定事件的对象；

- `ctx`：用来结束consumer的goroutine。

### 4.4 Event

由于在Fabric上抛出的事件内容是可以自定义的，而跨链请求要在跨链平台上传递的话，需要使用IBTP包，所以我们需要一定的代码来执行这种转换。

我们新建 `./event.go` 文件

```go
type Event struct {
    Index     uint64   `json:"index"`
    DstFullID string   `json:"dst_full_id"`
    SrcFullID string   `json:"src_full_id"`
    Encrypt   bool     `json:"encrypt"`
    CallFunc  CallFunc `json:"call_func"`
    CallBack  CallFunc `json:"callback"`
    RollBack  CallFunc `json:"rollback"`
}
```

Event结构也是自定义的，需要和在你的跨链合约中抛出的事件结构一致。一个跨链交易事件，一般来说需要指定目标应用链的ID `DstFullID`，目标应用链上智能合约服务ID（Fabric上的chaincode没有合约地址）`SrcFullID`，这次跨链交易的发起方服务ID,`Encrypt`是否要求加密，`CallFunc`，跨链调用的函数及参数， 是否有跨链调用之后要执行的回调函数及参数 `CallBack`，是否有跨链调用之后要执行的回滚函数及参数 `RollBack`。

### 4.5 SubmitIBTP

该接口主要负责将其他链发送过来的IBTP包解析并构造成当前目的链的交易，发送到目的链的跨链合约中。
如果来源链要求将本链调用合约的结果返回的话，还需要构造相应的IBTP回执发回来源链。

```go
func (c *Client) SubmitIBTP(from string, index uint64, serviceID string, ibtpType pb.IBTP_Type, content *pb.Content, proof *pb.BxhProof, isEncrypted bool) (*pb.SubmitIBTPResponse, error) {
    ret := &pb.SubmitIBTPResponse{Status: true}

    typ := int64(binary.BigEndian.Uint64(content.Args[0]))
    if typ == int64(pb.IBTP_Multi) {
    	return ret, fmt.Errorf("multi IBTP is not supported yet")
    }

    _, resp, err := c.InvokeInterchain(from, index, serviceID, uint64(ibtpType), content.Func, content.Args[1:], uint64(proof.TxStatus), proof.MultiSign, isEncrypted)
    if err != nil {
    	ret.Status = false
    	ret.Message = fmt.Sprintf("invoke interchain foribtp to call %s: %w", content.Func, err)
    	return ret, nil
    }
    ret.Status = resp.OK
    ret.Message = resp.Message

    if c.bitxhubID == "" || c.appchainID == "" {
    	c.bitxhubID, c.appchainID, err = c.GetChainID()
    	if err != nil {
    		ret.Status = false
    		ret.Message = fmt.Sprintf("get id err: %s", err)
    		return ret, nil
    	}
    }
    destFullID := c.bitxhubID + ":" + c.appchainID + ":" + serviceID
    servicePair := from + "-" + destFullID
    ibtp, err := c.GetReceiptMessage(servicePair, index)
    ret.Result = ibtp
    return ret, nil
}
```



## 5. 编译你的Plugin

我们采用GO语言提供的插件模式，实现Pier对于你编写的Plugin的动态加载。

MacOS和Linux平台：

运行下面的命令，能够得到 `your_plugin.so`文件。

```shell
$ cd ${YOUR_PROJECT_PATH}
$ go build --buildmode=plugin -o your_plugin.so ./*.go
```

将你编写的动态链接文件，放到Pier配置文件夹下，配合我们提供的Pier，就能接入到跨链平台来。