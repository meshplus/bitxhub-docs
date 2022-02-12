# 跨链合约

按照跨链合约的设计，我们需要在有跨链需求的应用链上部署两种合约。一个合约负责对接跨链网关Pier，为跨链管理合约Broker；一个合约负责具体的业务场景，为业务合约。业务合约需要跨链时，要统一将跨链请求提交到Broker合约上，Broker统一和Pier进行交互。一个Broker合约可以负责对接多个业务合约。

跨链接入方无需对broker合约进行修改，直接部署使用即可；同时为了简化业务合约的编写，我们设计了业务合约的相应接口。

以下以以太坊上的solidity合约为例。

## Broker 合约接口

```solidity
// 注册本链服务，由本链服务管理员调用，并生成proposal由应用链管理员投票
// addr： 本链业务合约地址
function register(address addr) public

// 直连模式下注册其他链的服务, 由当前应用链管理员调用
// serviceID: 对方应用链上要对本链进行跨链调用的服务ID
// banList：不允许对方服务调用的本链服务列表
function registerRemoteService(string memory chainID, string memory serviceID, address[] memory whiteList) public onlyAdmin

// 管理员对本地服务proposal投票，
// status：1 - 同意， 0 - 拒绝，其他 - 不计票
function audit(address addr, int64 status) public returns(bool)

// getInnerMeta 是获取当前链服务作为目的服务，接收到其他链服务跨链请求的记录。
// 以Broker所在的区块链为目的链的一系列跨链请求的序号信息。
// 如果Broker在A链，则可能有多条链和A进行跨链，如B->A:3; C->A:5。
// 返回的map中，key值为来源服务ID和当前服务ID组成的service pair，
// value为该service pair的跨链请求的记录，如{B-A:3, C-A:5}。
function getInnerMeta() public view returns (string[] memory, uint64[] memory)

// getOuterMeta 是获取当前链服务作为来源服务，向其他链服务发起跨链请求的记录。
// 以Broker所在的区块链为来源链的一系列跨链请求的序号信息。
// 如果以Broker在A链，则A可能和多条链进行跨链，如A->B:3; A->C:5。
// 返回的map中，key值为当前应用链的服务ID和目标服务ID组成的service pair，
// value为该service pair的跨链请求记录，如{A-B:3, A-C:5}。
function getOuterMeta() public view returns (string[] memory, uint64[] memory)

// getCallbackMeta 是获取当前链服务作为来源服务，接收到其他链服务跨链回执的记录。
// 以Broker所在的区块链为来源链的一系列跨链请求的序号信息。
// 如果Broker在A链，则A可能和多条链进行跨链，如A->B:3; A->C:5; 
// 同时由于跨链请求中支持回调操作，即A->B->A为一次完整的跨链操作，
// 我们需要记录回调请求的序号信息，如A->B->:2; A->C—>A:4。返回的map中，
// key值为当前应用链的服务ID和目标服务ID组成的service pair，
// value为该service pair的跨链回执的记录，
// 如{B-A:2, C-A:4}。注意 callbackMeta序号可能和outMeta是不一致的，
// 这是由于由A发出的跨链请求部分是没有回调的.
function getCallbackMeta() public view returns (string[] memory, uint64[] memory)

// getDstRollbackMeta 是获取当前链服务作为目的服务，在当前链上回滚的index记录。
// 以Broker所在的区块链为目的链的一系列跨链请求的序号信息。
// 如果Broker在A链，则可能有多条链和A进行跨链，如B->A:3; C->A:5; 
// 同时由于跨链请求中支持超时回滚，
// 需要记录中继链超时块高后未收到目的链跨链回执的场景下目的链回滚的信息.
// 我们需要记录超时回滚的序号信息。返回的map中，key值为来源服务ID和当前服务ID组成的service pair，
// value为该service pair 在目的链最近一次进行回滚的index值，
// 如{B-A:3, C-A:5}。
function getDstRollbackMeta() public view returns (string[] memory, uint64[] memory)

// getReceiptMessage 获取当前应用链执行的某个id对应的跨链交易的回执信息。
// 查询键值中inServicePair为来源链的来源服务ID和当前服务ID组成的service pair，idx指定序号，
// 查询结果为目的服务执行跨链交易后返回的回执信息。
function getReceiptMessage(string memory inServicePair, uint64 idx) public view returns (bytes[] memory, uint64, bool)

// getOutMessage 获取从当前应用链抛出的某个id对应的跨链交易的payload信息。
// 查询键值中outServicePair为当前链的来源服务ID和目的服务ID组成的service pair，
// idx指定序号，查询结果为该跨链事件的调用/回调/回滚信息。
function getOutMessage(string memory outServicePair, uint64 idx) public view returns (string memory, bytes[] memory, bool)

// getLocalServiceList 获取审核通过的本链服务ID列表
function getLocalServiceList() public view returns (string[] memory)

// gteRemoteServiceList 获取其他应用链向本链注册的应用链服务ID列表
function getRemoteServiceList() public view returns (string[] memory)

// 提供给跨链网关调用的接口，跨链网关收到跨链请求时会调用该接口。
function invokeInterchain(string memory srcFullID, address destAddr, uint64 index, uint64 typ, string memory callFunc, bytes[] memory args, uint64 txStatus, bytes[] memory signatures, bool isEncrypt) payable external

// 提供给跨链网关调用的接口，跨链网关收到跨链请求回执时会调用该接口。
function invokeReceipt(address srcAddr, string memory dstFullID, uint64 index, uint64 typ, bytes[] memory result, uint64 txStatus, bytes[] memory signatures) payable external

// 提供给业务合约发起通用的跨链交易的接口。
function emitInterchainEvent(string memory destFullServiceID, string memory funcCall, bytes[] memory args, string memory funcCb, bytes[] memory argsCb, string memory funcRb, bytes[] memory argsRb, bool isEncrypt) public onlyWhiteList

// 提供给合约部署初始化使用
function initialize() public
```

### 重要接口说明

- `emitInterchainEvent` 

该接口是业务合约发起通用的跨链调用的接口。接收的参数有：目的服务ID，跨链调用目的服务方法和参数，收到跨链成功回执后进行回调所需的方法和参数，收到跨链失败回执后进行回滚所需的方法和参数，跨链过程中是否需要加密payload。

Broker会记录跨链交易相应的元信息，对跨链交易进行编号，保证跨链交易有序进行, 并且抛出跨链事件，以通知跨链网关跨链交易的产生。

- `invokeInterchain` 

该接口是跨链网关对业务合约进行跨链调用接口。 接收参数有：来源服务ID，目的服务合约地址，IBTP index，IBTP type，对目的服务进行跨链调用的方法，对目的服务跨链调用的参数，中继链上该跨链交易事务的状态，中继链对上述所有字段的多签。

跨链网关对要调用的目的合约的方法和参数进行封装，通过该接口实现对不同目的合约的灵活调用，并返回目的合约的调用函数的返回值。

- `invokeReceipt`

该接口是跨链网关对业务合约进行跨链回调或回滚的接口。接收参数有：来源服务ID，目的服务ID，IBTP index，IBTP type，调用目的链服务返回的内容，中继链上该跨链交易事务的状态，中继链多签。

## 业务合约接口

业务合约现阶段分为资产类和数据交换类的业务合约，由于资产类的有操作原子性和安全性的考虑，需要的接口实现上比数据交换类的业务合约更复杂。

### Transfer 合约

```solidity
  // 发起一笔跨链交易的接口
  function transfer(string memory destChainServiceID, string memory sender, string memory receiver, uint64 amount) public

  // 提供给Broker合约收到跨链请求所调用的接口
  function interchainCharge(bytes[] memory args, bool isRollback) public onlyBroker returns (bytes[] memory)

  // 跨链交易失败之后，提供给Broker合约进行回滚的接口
  function interchainRollback(bytes[] memory args) public onlyBroker

  // 获取transfer合约中某个账户的余额
  function getBalance(string memory id) public view returns(uint64)

  // 在transfer合约中给某个账户设置一定的余额
  function setBalance(string memory id, uint64 amount) public
}
```

### DataSwapper合约

```solidity
  // 发起一个跨链获取数据交易的接口
  function get(string memory destChainServiceID, string memory key) public

  // 提供给Broker合约调用，当Broker收到跨链获取数据的请求时取数据的接口
  function interchainGet(bytes[] memory args, bool isRollback) public onlyBroker returns(bytes[] memory)

  // 跨链获取到的数据回写的接口
  function interchainSet(bytes[] memory args) public onlyBroker
```

## 具体实现

对于想要接入到我们的跨链平台中的Fabric区块链，我们已经有提供跨链管理合约Broker和相应的Plugin，你只需要对你的业务合约进行一定的改造便可拥有跨链功能。

**如果是其他应用链，你可以根据我们的设计思路自行开发跨链管理合约以及相应的Plugin。**

现在我们已经有Solidity版本和chaincode版本编写的跨链合约样例实现，具体说明如下：

- [__Solidity 跨链合约实现__](https://github.com/meshplus/pier-client-ethereum/tree/master/example)；

- [__Chaincode 跨链合约实现__](https://github.com/meshplus/pier-client-fabric/tree/master/example)；

如果你需要新的语言编写合约，你可以按照我们的设计思路和参考实现进行进一步的开发。

现在我们支持Hyperchain EVM合约、以太坊私链Solidity合约、BCOS EVM合约以及Fabric Chaincode合约。

## Hyperchain、以太坊、BCOS上的EVM合约

本节主要说明在支持EVM合约的应用链上，如何使用我们提供的跨链管理合约Broker，在你已有的Solidity业务合约的基础上添加接口，以获得跨链能力。

当然不同的区块链可能在以太坊的EVM上做了一些二次开发和新增功能，请根据具体区块链的文档相应修改代码。

### <a name="contractAnchor">业务合约Demo</a>

假设你已经有了一个简单的KV存储的业务合约，代码如下：

```solidity
pragma solidity >=0.5.6;

contract DataSwapper {
    mapping(string => string) dataM; // map for accounts

    // 数据交换类的业务合约
    function getData(string memory key) public view returns(string memory) {
        return dataM[key];
    }

    function set(string memory key, string memory value) public {
        dataM[key] = value;
    }
}

```

现在你想在这个合约的基础上增加一个跨链获取数据的功能，如果使用我们的跨链管理合约提供的接口，很简单的增加几个接口就可以了。

### 发起跨链数据交换的接口

```solidity
contract DataSwapper {
    // broker合约地址
	address BrokerAddr;
	// 通过构造器初始化broker合约地址
    constructor(address _brokerAddr) public {
        BrokerAddr = _brokerAddr;
    }

	...

    function get(string memory destChainServiceID, string memory key) public {
        bytes[] memory args = new bytes[](1);
        args[0] = abi.encodePacked(key);
        
        bytes[] memory argsCb = new bytes[](1);
        argsCb[0] = abi.encodePacked(key);

        Broker(BrokerAddr).emitInterchainEvent(destChainServiceID, "interchainGet", args, "interchainSet", argsCb, "", new bytes[](0), false);
    }
}

abstract contract Broker {
    function emitInterchainEvent(
        string memory destFullServiceID,
        string memory func,
        bytes[] memory args,
        string memory funcCb,
        bytes[] memory argsCb,
        string memory funcRb,
        bytes[] memory argsRb,
        bool isEncrypt) public virtual;
}
```

其中Broker的地址和该业务合约需要使用到的接口需要在业务合约中声明，然后直接调用该接口发起跨链交易。

### 跨链获取的接口

```solidity
contract DataSwapper {
    
    ...

    modifier onlyBroker {
        require(msg.sender == BrokerAddr, "Invoker are not the Broker");
        _;
    }
    
    function interchainGet(bytes[] memory args, bool isRollback) public onlyBroker returns(bytes[] memory) {
        require(args.length == 1, "interchainGet args' length is not correct, expect 1");
        string memory key = string(args[0]);
        
        bytes[] memory result = new bytes[](1);
        result[0] = abi.encodePacked(dataM[key]);
        
        return result;
    }
}
```
我们规定供其他应用链进行调用的接口参数固定，第一个参数类型为bytes[]，为具体的业务参数，第二个参数类型为bool，表示是否回滚。
其中onlyBroker是进行跨链权限控制的修饰器。该接口和下面的跨链回写接口均是提供给Broker合约进行调用，也是其他应用链发来的跨链交易执行时需要调用的接口。

### 跨链回写的接口

```solidity
contract DataSwapper {

    ...

    function interchainSet(bytes[] memory args) public onlyBroker {
        require(args.length == 2, "interchainSet args' length is not correct, expect 2");
        string memory key = string(args[0]);
        string memory value = string(args[1]);
        set(key, value);
    }

    ...

}

```

## Fabric

本节主要说明在Fabric应用链上，如何使用我们提供的跨链管理合约Broker，在你已有业务合约的基础上添加接口，以获得跨链能力。

### 业务合约Demo

假设你已经有了一个简单的KV存储的业务合约，代码如下：

```go
type DataSwapper struct{}

func (s *DataSwapper) Init(stub shim.ChaincodeStubInterface) pb.Response {
	return shim.Success(nil)
}

func (s *DataSwapper) Invoke(stub shim.ChaincodeStubInterface) pb.Response {
	function, args := stub.GetFunctionAndParameters()

	fmt.Printf("invoke: %s\n", function)
	switch function {
	case "get":
		return s.get(stub, args)
	case "set":
		return s.set(stub, args)
	default:
		return shim.Error("invalid function: " + function + ", args: " + strings.Join(args, ","))
	}
}

func (s *KVStore) get(stub shim.ChaincodeStubInterface, args []string) peer.Response {
	// args[0]: key
	value, err := stub.GetState(args[0])
	if err != nil {
		return shim.Error(err.Error())
	}

	return shim.Success(value)
}

func (s *DataSwapper) set(stub shim.ChaincodeStubInterface, args []string) pb.Response {
	if len(args) != 2 {
		return shim.Error("incorrect number of arguments")
	}

	err := stub.PutState(args[0], []byte(args[1]))
	if err != nil {
		return shim.Error(err.Error())
	}

	return shim.Success(nil)
}

func main() {
	err := shim.Start(new(DataSwapper))
	if err != nil {
		fmt.Printf("Error starting chaincode: %s", err)
	}
}
```

现在你想在这个合约的基础上增加一个跨链获取数据的功能，如果使用我们的跨链管理合约提供的接口，很简单的增加几个接口就可以了。

### 发起跨链数据交换的接口

为了方便用户使用，我们在原来获取数据的接口基础上增加这个功能：

```go
const (
	channelID               = "mychannel"
	brokerContractName      = "broker"
	emitInterchainEventFunc = "EmitInterchainEvent"
)

func (s *DataSwapper) get(stub shim.ChaincodeStubInterface, args []string) pb.Response {
	switch len(args) {
	case 1:
		// args[0]: key
		value, err := stub.GetState(args[0])
		if err != nil {
			return shim.Error(err.Error())
		}

		return shim.Success(value)
	case 2:
		// args[0]: destination service id
		// args[1]: key
		var callArgs, argsCb [][]byte
		callArgs = append(callArgs, []byte(args[1]))
		argsCb = append(argsCb, []byte(args[1]))

		callArgsBytes, err := json.Marshal(callArgs)
		if err != nil {
			return shim.Error(err.Error())
		}
		argsCbBytes, err := json.Marshal(argsCb)
		if err != nil {
			return shim.Error(err.Error())
		}

		b := util.ToChaincodeArgs(emitInterchainEventFunc, args[0], "interchainGet", string(callArgsBytes), "interchainSet", string(argsCbBytes), "", "", strconv.FormatBool(false))
		response := stub.InvokeChaincode(brokerContractName, b, channelID)
		if response.Status != shim.OK {
			return shim.Error(fmt.Errorf("invoke broker chaincode %s error: %s", brokerContractName, response.Message).Error())
		}

		return shim.Success(nil)
	default:
		return shim.Error("incorrect number of arguments")
	}
}
```

由于我们的跨链管理合约一旦部署之后，chaincode name和所在的channel和跨链接口都是不变的，所以在业务变量中直接使用常量指定Broker合约的相关信息。

```go
b := util.ToChaincodeArgs(emitInterchainEventFunc, args[0], "interchainGet", string(callArgsBytes), "interchainSet", string(argsCbBytes), "", "", strconv.FormatBool(false))
response := stub.InvokeChaincode(brokerContractName, b, channelID)
```

这两行代码调用了我们的跨链管理合约，只需要提供参数：目的链ID，目的链上业务合约的地址，跨链调用函数，跨链调用函数参数，回调函数，回调函数参数，最后两个参数为回滚函数和回滚函数参数，因为该场景下即使目的链执行跨链交易失败，来源链也无需回滚，因此无需提供回滚信息。

### 跨链获取的接口

```go
func (s *DataSwapper) interchainGet(stub shim.ChaincodeStubInterface, args []string) pb.Response {
	value, err := stub.GetState(args[0])
	if err != nil {
	    return shim.Error(err.Error())
	}
	return shim.Success(value)
}
```

`interchainGet` 接收参数 `key`，在本合约中查询该`Key`值对应的`value`，并返回。该接口提供给`Broker`合约进行跨链获取数据的调用。

### 跨链回写的接口

```go
func (s *DataSwapper) interchainSet(stub shim.ChaincodeStubInterface, args []string) pb.Response {
	return s.set(stub, args)
}
```

`interchainSet` 接收参数 `key`，在本合约中设置`Key`值对应的`value`。该接口提供给`Broker`合约回写跨链获取数据的时候进行调用。

## 使用示例

**跨链场景**：以以太坊为例，位于A链的账户Alice向位于B链的Bob发起转账交易。

1. 在应用链部署broker合约与业务合约，具体部署流程参考[部署跨链合约](../../usage/single_bitxhub/deploy_pier/)。

2. 调用`register`方法注册业务合约。入参为需要进行跨链的业务合约地址。
3. 调用`audit`对已经注册的业务合约进行审核，status为1说明审核通过。
4. `transfer`业务合约调用`setBalance`方法初始化账户。
5. `transfer`业务合约调用`transfer`方法发起跨链交易。

```
1.1 appchainA deploy ==> brokerA addr：0xe4067Aab511D7eAD1d481A8491666249C2860209  
					 	 businessA addr: 0x919289F66Ce642a7598F76c34005dD813Ccafc20					 						 
1.2 appchainB deploy ==> brokerB addr：0xCAbAb560aD08a30cd11e8e2AB8dd1353a0d6EA35  
					 	 businessB addr: 0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5

2.1 brokerA register ==> addr：0x919289F66Ce642a7598F76c34005dD813Ccafc20 //部署的业务合约地址
2.2 brokerB register ==> addr：0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5 //部署的业务合约地址

3.1 brokerA audit ==> addr：0x919289F66Ce642a7598F76c34005dD813Ccafc20  
					  status: 1
3.2 brokeB audit ==> addr：0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5  
					 status: 1	
														
4.1 transferA setBalance ==> id: Alice 
                             amount: 100
4.2 transferB setBalance ==> id: Bob 
                             amount: 0
													 
// destChainServiceID为B链的服务ID，由bxhID，chainId，serviceId构成
// 应用链和服务需要先在中继链上注册才能进行跨链
5 transferA transfer ==> destChainServiceID: 1356:chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5
						sender: Alice
						receiver: Bob
						amount: 10
```

观察跨链网关与中继链日志

```
# 网关pierA同步以太坊区块头并处理跨链交易，发送至中继链
2022-02-12T16:45:18.800+0800 [DEBUG] plugin.sh: 2022-02-12T16:45:18.800+0800 [INFO ] client: Consumer started
INFO[2022-02-12T16:45:18.800] appchain adapter start                        module=appchain_adapter
INFO[2022-02-12T16:45:18.803] BxhAdapter started                            module=bxh_adapter
INFO[2022-02-12T16:45:18.820] Start To Recover IBTPs!                       module=exchanger
INFO[2022-02-12T16:45:18.820] End To Recover IBTPs!                         module=exchanger
INFO[2022-02-12T16:45:18.820] Exchanger started                             module=exchanger
INFO[2022-02-12T16:45:18.820] listenIBTPFromDestAdaptToServicePairCh bitxhub:1356 Start!  module=exchanger
INFO[2022-02-12T16:45:18.820] listenIBTPFromSrcAdaptToServicePairCh appchain:chain0 Start!  module=exchanger
INFO[2022-02-12T16:46:27.734] Receive ibtp from :appchain:chain0            ibtp_id="1356:chain0:0x919289F66Ce642a7598F76c34005dD813Ccafc20-1356:chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5-1" index=1 module=exchanger type=INTERCHAIN
INFO[2022-02-12T16:46:28.145] Handle interchain tx wrapper                  count=0 height=6 index=0 module=bxh_adapter timeout IDs="[]"
INFO[2022-02-12T16:46:28.424] Handle interchain tx wrapper                  count=0 height=7 index=0 module=bxh_adapter timeout IDs="[]"

# 中继链将收到的跨链交易上链，并路由到网关PierB
time="2022-02-12T16:46:32.153" level=info msg="======== Replica 1 call execute, height=8" module=order
time="2022-02-12T16:46:32.154" level=info msg="Generated block" count=1 height=8 module=app
time="2022-02-12T16:46:32.155" level=info msg="Verify proofs" gasUsed=0 module=executor
time="2022-02-12T16:46:32.157" level=info msg="record invoke service" chainServiceID="chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5" fromChainServiceID="chain0:0x919289F66Ce642a7598F76c34005dD813Ccafc20" module=executor result=true
time="2022-02-12T16:46:32.160" level=info msg="Persisted block" count=1 elapse=1.507132ms hash=0xB619b3f252CA7DAc6BC03BD298132190b4c2a5C18c3F7Aba9Ba7879E9DfCB5d5 height=8 module=executor

# 网关PierB同步区块头并处理跨链交易，将回执类型跨链交易返回给中继链
2022-02-12T16:45:32.848+0800 [DEBUG] plugin.sh: 2022-02-12T16:45:32.848+0800 [INFO ] client: Consumer started
INFO[2022-02-12T16:45:32.848] appchain adapter start                        module=appchain_adapter
INFO[2022-02-12T16:45:32.851] BxhAdapter started                            module=bxh_adapter
INFO[2022-02-12T16:45:32.860] Start To Recover IBTPs!                       module=exchanger
INFO[2022-02-12T16:45:32.861] End To Recover IBTPs!                         module=exchanger
INFO[2022-02-12T16:45:32.861] Exchanger started                             module=exchanger
INFO[2022-02-12T16:45:32.861] listenIBTPFromSrcAdaptToServicePairCh appchain:chain1 Start!  module=exchanger
INFO[2022-02-12T16:45:32.861] listenIBTPFromDestAdaptToServicePairCh bitxhub:1356 Start!  module=exchanger
INFO[2022-02-12T16:46:28.707] Handle interchain tx wrapper                  count=1 height=6 index=0 module=bxh_adapter timeout IDs="[]"
INFO[2022-02-12T16:46:28.708] Receive ibtp from :bitxhub:1356               ibtp_id="1356:chain0:0x919289F66Ce642a7598F76c34005dD813Ccafc20-1356:chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5-1" index=1 module=exchanger type=INTERCHAIN
INFO[2022-02-12T16:46:28.708] Handle interchain tx wrapper                  count=0 height=7 index=0 module=bxh_adapter timeout IDs="[]"
INFO[2022-02-12T16:46:28.715] start submit ibtp                             ibtp="1356:chain0:0x919289F66Ce642a7598F76c34005dD813Ccafc20-1356:chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5-1" module=appchain_adapter typ=INTERCHAIN
INFO[2022-02-12T16:46:31.669] Receive ibtp from :appchain:chain1            ibtp_id="1356:chain0:0x919289F66Ce642a7598F76c34005dD813Ccafc20-1356:chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5-1" index=1 module=exchanger type=RECEIPT_SUCCESS
INFO[2022-02-12T16:46:32.163] Handle interchain tx wrapper                  count=0 height=8 index=0 module=bxh_adapter timeout IDs="[]"
INFO[2022-02-12T16:46:34.510] Handle interchain tx wrapper                  count=0 height=9 index=0 module=bxh_adapter timeout IDs="[]"
INFO[2022-02-12T16:46:49.312] appchain adapter submit ibtp success          module=appchain_adapter

# 中继链将收到的回执类型的跨链交易上链，返回至网关PierA
time="2022-02-12T16:46:34.507" level=info msg="======== Replica 1 call execute, height=9" module=order
time="2022-02-12T16:46:34.507" level=info msg="Generated block" count=1 height=9 module=app
time="2022-02-12T16:46:34.510" level=info msg="Verify proofs" gasUsed=0 module=executor
time="2022-02-12T16:46:34.510" level=info msg="Bxh transaction" gasUsed=210000 module=executor
time="2022-02-12T16:46:34.512" level=info msg="Persisted block" count=1 elapse=1.485487ms hash=0xf98900C2cb9659325ae63273eFA372B264000B89FE5fd4C55703C6e54fA755B0 height=9 module=executor

# 网关PierA收到跨链交易回执，返回给应用链A
INFO[2022-02-12T16:46:34.670] Handle interchain tx wrapper                  count=1 height=8 index=0 module=bxh_adapter timeout IDs="[]"
INFO[2022-02-12T16:46:34.670] Handle interchain tx wrapper                  count=0 height=9 index=0 module=bxh_adapter timeout IDs="[]"
INFO[2022-02-12T16:46:34.670] Receive ibtp from :bitxhub:1356               ibtp_id="1356:chain0:0x919289F66Ce642a7598F76c34005dD813Ccafc20-1356:chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5-1" index=1 module=exchanger type=RECEIPT_SUCCESS
INFO[2022-02-12T16:46:34.671] start submit receipt                          ibtp="1356:chain0:0x919289F66Ce642a7598F76c34005dD813Ccafc20-1356:chain1:0xc12AF1d0473D5d489aDFd87cbbe5bb66C4FFA3f5-1" module=appchain_adapter typ=RECEIPT_SUCCESS

# 跨链交易完成，在应用链A上查询Alice的余额从100变更为90；在应用链B上查询Bob的余额从0变更为10
```


## 总结

经过上面的改造，你的业务合约已经具备跨链获取数据的功能了，完整的代码可以参考[__这里__](https://github.com/meshplus/pier-client-fabric/tree/master/example)。

