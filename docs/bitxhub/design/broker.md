# 跨链管理合约设计

按照IBTP结构的设计，BitXHub只负责记录应用链这个粒度的跨链交易index。但是Pier在调用应用链智能合约执行跨链交易时，有多个智能合约的情况下，很难维护一个应用链层面的index。

为此，我们考虑在各个应用链上维护一个单独的智能合约Broker作为中转。该合约直接和Pier交互，而应用链的跨链交易的index由该合约维护。

直连模式下，跨链管理合约还需要记录对方应用链的相关信息，以便Pier对跨链交易进行处理，保证跨链交易的有序性和原子性。

下面以以太坊上的Solidity合约为例进行说明。

## Broker合约接口整体说明
```solidity
// 注册本链服务，在部署业务合约时由合约调用，并生成proposal由应用链管理员投票
function register() public

// 注册直连事务管理服务，在部署事务管理合约时由合约调用
function registerDirectTransaction() public

// 直连模式下注册其他链的服务, 由当前应用链管理员调用
// chainID：对方应用链ID
// serviceID: 对方应用链上要对本链进行跨链调用的服务ID
// whiteList：允许对方服务调用的本链服务列表
function registerRemoteService(string memory chainID, string memory serviceID, address[] memory whiteList) public onlyAdmin

// 直连模式注册其他链的信息，由当前应用链管理员调用
// chainID：对方应用链ID
// broker：对方应用链broker合约地址
// ruleAddr：对方应用链验证规则地址，用于Pier验证跨链交易
// trustRoot：对方应用链信任根信息，用于Pier验证跨链交易
function registerAppchain(string memory chainID, string memory broker, address ruleAddr, bytes memory trustRoot) public onlyAdmin

// 管理员对本链服务proposal投票，
// status：1 - 同意， 0 - 拒绝，其他 - 不计票
function audit(address addr, int64 status) public onlyAdmin returns (bool)

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

// getAppchainInfo 直连模式下获取向本链注册的其他应用链信息
// chainID：对方应用链ID
function getAppchainInfo(string memory chainID) public view returns (string memory, bytes memory, address)

// getLocalServiceList 获取审核通过的本链服务ID列表
function getLocalServiceList() public view returns (string[] memory)

// getRemoteServiceList 直连模式下获取其他应用链向本链注册的应用链服务ID列表
function getRemoteServiceList() public view returns (string[] memory)

// getRSWhiteList 直连模式下获取其他应用链能够调用的本链服务ID列表
// remoteAddr：对方应用链服务地址(:chainID:serviceID)
function getRSWhiteList(string memory remoteAddr) public view returns (address[] memory)

// getLocalWhiteList 查询本链服务是否注册成功
// addr：本链服务地址(serviceID)
function getLocalWhiteList(address addr) public view returns (bool)

// getDirectTransactionMeta 直连模式下获取跨链交易此时的事务状态以及事务开始时间
// id：ibtp id
function getDirectTransactionMeta(string memory id) public view returns (uint, uint64)

// getChainID 获取broker合约部署时指定的bxhID以及chainID
function getChainID() public view returns (string memory, string memory)

// 提供给跨链网关调用的接口，跨链网关收到跨链请求时会调用该接口。
function invokeInterchain(string memory srcFullID, string memory destAddr, uint64 index, uint64 typ, string memory callFunc, bytes[] memory args, uint64 txStatus, bytes[] memory signatures, bool isEncrypt) payable external

// 提供给跨链网关调用的接口，跨链网关收到跨链请求回执时会调用该接口。
function invokeReceipt(string memory srcAddr, string memory dstFullID, uint64 index, uint64 typ, bytes[] memory result, uint64 txStatus, bytes[] memory signatures) payable external

// 提供给业务合约发起通用的跨链交易的接口。
function emitInterchainEvent(string memory destFullServiceID, string memory funcCall, bytes[] memory args, string memory funcCb, bytes[] memory argsCb, string memory funcRb, bytes[] memory argsRb, bool isEncrypt) public onlyWhiteList

// 提供给合约部署初始化使用
function initialize() public
```

## Broker合约功能说明
### 1. Broker合约初始化

broker合约初始化时需要添加中继链的相关信息：

- 中继链ID：用于抛出跨链交易时获得当前的full service ID
- 应用链ID：用于抛出跨链交易时获得当前full service ID
- 验证人地址集合：用于验证中继链多签
- 验签通过的最少验证人数量：如果是直连模式，改值为0
- 应用链管理员集合
- 投票通过的最少应用链管理员数量

因此，broker的构造函数如下：
```solidity
string bxhID;
string appchainID;
address[] validators;
uint64 valThreashold;
address[] admins;
uint64 adminThreashold;

constructor(string memory _bxhID, 
	    string memory _appchainID,
	    address[] memory _validators,
	    uint64 _valThreashold,
	    address[] memory _admins,
	    uint64 _adminThreashold) public {
     bxhID = _bxhID;
     appchainID = _appchainID;
     validators = _validators;
     valThreashold = _valThreashold;
     admins = _admins;
     adminThreahold = _adminThreahold;
}
```

### 2. 服务管理

broker合约需要实现跨链服务的管理功能，主要包含服务注册、审核和查询，具体如下：

1. 应用链业务管理员申请跨链服务的注册，以请求该服务通过broker合约向外面的跨链服务进行跨链
2. 应用链管理员对业务管理员的申请进行审核，允许或者拒绝跨链服务的请求
3. 提供审核通过的跨链服务列表的查询

如果是直连模式，broker合约还需要实现其他跨链服务的管理：

1. 应用链管理员配置允许跨链的对方应用链的跨链服务，并设置其可以访问的本地服务列表 （对方应用链管理员或者业务管理员理论上没有权限访问本链，该信息通过链下协商，由本链应用链管理员进行配置）
2. 提供对方应用链的跨链服务列表的查询

```solidity
// 记录本链审核通过的业务合约，key为本链服务合约地址，value为true
mapping(address => bool) localWhiteList;
// 记录本链业务合约申请和管理员投票记录，投票完成后从当前mapping移除，如果通过则加入localWhiteList
mapping(address => Vote) localServiceProposal;

// 注册本链服务，在部署业务合约时由合约调用，并生成proposal由应用链管理员投票
function register() public

// 管理员对本地服务proposal投票，
// status：1 - 同意， 0 - 拒绝，其他 - 不计票
function audit(address addr, uint64 status) public onlyAdmin

// 直连模式下其他应用链服务的记录
// key为其他应用链服务ID
// value为允许该服务调用本链服务的地址列表
mapping(string => address[]) remoteWhiteList;

// 直连模式下注册其他链的服务, 由当前应用链管理员调用
// chainID：对方应用链ID
// serviceID: 对方应用链上要对本链进行跨链调用的服务ID
// whiteList：允许对方服务调用的本链服务列表
function registerRemoteService(string memory chainID, string memory serviceID, address[] memory whiteList) public onlyAdmin

// 返回审核通过的本链服务ID列表
function getLocalServiceList() public view returns (string[] memory)

// 返回其他应用链向本链注册的应用链服务ID列表
function getRemoteServiceList() public view returns (string[] memory)

// 直连模式注册其他链的信息，由当前应用链管理员调用
// chainID：对方应用链ID
// broker：对方应用链broker合约地址
// ruleAddr：对方应用链验证规则地址，用于Pier验证跨链交易
// trustRoot：对方应用链信任根信息，用于Pier验证跨链交易
function registerAppchain(string memory chainID, string memory broker, address ruleAddr, bytes memory trustRoot) public onlyAdmin

// getAppchainInfo 直连模式下获取向本链注册的其他应用链信息
// chainID：对方应用链ID
function getAppchainInfo(string memory chainID) public view returns (string memory, bytes memory, address)
```

### 3. 跨链信息管理
#### index管理
为了维护跨链服务的有序性，防止重复调用或者遗漏；也为了pier可以在重启时恢复每个跨链服务的跨链状态，如发送的未发送IBTP和接收未接收的IBTP回执，broker合约中需要记录跨链请求和回执的index以及跨链事件的具体信息。

```solidity
// 记录当前应用链服务作为来源服务，向其他链的服务发起跨链请求的记录
// key为当前应用链的服务ID和目标服务ID组成的service pair
// value为该service pair的跨链请求记录
mapping(string => uint64) outCounter

// 记录当前应用链服务作为来源服务，向其他链的服务跨链接收到的回执信息的记录
// key为当前应用链的服务ID和目标服务ID组成的service pair
// value为该service pair的跨链回执的记录
mapping(string => uint64) callbackCounter

// 记录当前应用链服务作为目的服务，收到其他链的服务跨链交易的记录
// key为来源服务ID和当前服务ID组成的service pair
// value为该service pair的跨链请求的记录
mapping(string => uint64) inCounter

struct Vote {
    uint64 approve;
    uint64 reject;
    mapping(address => bool) votedAdmins;
    bool exist;
}

// 记录来源链网关与中继链（中继模式）或目的网关（直连模式）因为网络问题而超时时，来源链需要回滚的IBTP
// key为来源链需要回滚的IBTP ID
// value为应用链管理员投票数量
// 如果应用链管理员投票同意数量达到指定值，则对来源服务进行回滚，更新rollbackProposal，将当前IBTP ID从rollbackProposal中删除，
// 并抛出一个index相同但是callFunc为空的事件，同时更新outMessage，pier会将该事件typ设置成ROLLBACK
// 如果应用链管理员投票拒绝数量达到指定值，则将当前IBTP ID从rollbackProposal中删除，
// 并重新抛出相同内容的跨链事件
mapping(string => mapping(uint64 => Vote)) rollbackProposal

// 中继链超时块高后未收到目的链跨链回执的场景下，目的链回滚的记录
// key为来源服务ID和当前服务ID组成的service pair
// value为该service pair 在目的链最近一次进行回滚的index值
mapping(string => uint64) dstRollbackCounter

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
```

#### message维护
Pier从维护的index恢复了各个服务的跨链状态以后，要从应用链查询未发送的IBTP或者IBTP Receipt，因此broker需要提供相关的信息以便应用链可以很方便的查询IBTP或者IBTP Receipt。

```solidity
// 跨链事件调用信息    

    struct CallFunc {
        string func;
        string[] args;
    }
    
    struct InterchainInvoke {
        CallFunc callFunc;
        CallFunc callback;
        CallFunc rollback;
    }

// 记录当前应用链向外抛出跨链事件的ID以及事件的调用/回调/回滚信息
// key：当前链的来源服务ID和目的服务ID组成的service pair
// value: 是一个map，key为index， value为InterchainInvoke，记录该跨链事件的调用/回调/回滚信息
mapping(string => mapping(uint64 => InterchainInvoke)) outMessages;

// 记录当前应用链接收到跨链事件的ID以及执行跨链交易后返回的回执信息
// key：来源链的来源服务ID和当前服务ID组成的service pair
// value: 是一个map，key为index， value为目的服务执行跨链交易后返回的回执信息
mapping(string => mapping(uint64 => bytes)) receiptMessages;

// getReceiptMessage 获取当前应用链执行的某个id对应的跨链交易的回执信息。
// 查询键值中inServicePair为来源链的来源服务ID和当前服务ID组成的service pair，idx指定序号，
// 查询结果为目的服务执行跨链交易后返回的回执信息。
function getReceiptMessage(string memory inServicePair, uint64 idx) public view returns (bytes[] memory, uint64, bool)

// getOutMessage 获取从当前应用链抛出的某个id对应的跨链交易的payload信息。
// 查询键值中outServicePair为当前链的来源服务ID和目的服务ID组成的service pair，
// idx指定序号，查询结果为该跨链事件的调用/回调/回滚信息。
function getOutMessage(string memory outServicePair, uint64 idx) public view returns (string memory, bytes[] memory, bool)
```

### 4. 来源链抛出跨链事件
broker作为应用链上向其他应用链进行跨链的出口，需要提供接口供业务合约调用，使业务合约可以简单的进行跨链调用。

一个chain0的service A到chain1的service B的跨链交易涉及到三种方法，分别如下：

1. callFunc：service B对外提供的允许其他应用链的业务进行跨链调用的方法
2. callback：service A对service B的callFunc调用成功后，回到chain0时对service A进行调用的方法，一般用于把callFunc返回的结果提交给service A
3. rollback：service A对service B的callFunc调用失败后，回到chain0时对service A进行调用的方法，用于回滚service A在抛出跨链事件之前进行的操作

目前这三种方法以及相关的参数信息都记录在IBTP的payload中。为了尽量减少业务合约以及调用者的相关信息的暴露，callback和rollback相关的信息不再记录在IBTP，而是记录在broker合约中。

```solidity
// 业务合约调用该接口向目的应用链的服务进行跨链调用
// destFullServiceID：目的服务ID
// funcCall：跨链调用目的服务方法
// args：跨链调用目的服务参数
// funcCb：收到跨链成功回执后，进行回调所需的方法
// argsCb：收到跨链成功回执后，进行回调所需的部分参数，回调方法的全部参数是 cbFunc.args + 回执的内容
// funcRb：收到跨链失败回执后，进行回滚所需的方法
// argsRb：收到跨链失败回执后，进行回滚所需的参数
// isEncrypt：跨链过程中是否需要加密payload
function emitInterchainEvent(
        string memory destFullServiceID,
        string memory funcCall,
        bytes[] memory args,
        string memory funcCb,
        bytes[] memory argsCb,
        string memory funcRb,
        bytes[] memory argsRb,
        bool isEncrypt)
    public onlyWhiteList
```

broker合约接收到业务合约发起的emitInterchainEvent调用后，对业务合约进行权限检查，通过后，对相应的OuterCounter进行递增，将callFunc/cbFunc/rbFunc记录在outMessages中，并抛出跨链事件。事件定义如下：

```solidity
// broker合约抛出跨链事件使用的方法
// index： IBTP index
// dstFullID：IBTP to，目的服务的ID
// srcFullID：IBTP from，来源服务的ID
// func：对目的链服务进行调用使用的方法
// args：对目的链服务进行调用的方法使用的参数
// hash：对func和args使用abi.encodePacked()进行序列化后计算keccak256 hash
event throwInterchainEvent(uint64 index, string dstFullID, string srcFullID, string func, bytes[] args, bytes32 hash);
```

其中，当业务合约调用emitInterchainEvent时，isEncrypt为true，则throw event时func和args都为空，跨链网关接收到跨链事件以后，需要主动调用getOutMessage获取对应IBTP ID的func和args，加密以后组装成IBTP进行传输。

### 5. 目的链接收跨链请求
目的链broker接收跨链请求的方法定义如下：

```solidity
// 目的链收到IBTP时调用的方法
// srcFullServiceID：IBTP from，来源服务ID
// dstAddr：目的服务合约地址
// index：IBTP index
// typ：IBTP type，IBTP_Interchain或者IBTP_Rollback
// callFunc: 对目的服务进行跨链调用的方法
// args：对目的服务跨链调用方法的参数
// txStatus：中继链上该跨链交易事务的状态，BEGIN/FAIL/ROLLBACK
// signatures：中继链对上述所有字段的多签
// isEncrypt：payload是否加密

function invokeInterchain(
        string memory srcFullID,
        string memory destAddr,
        uint64 index,
        uint64 typ,
        string memory callFunc,
        bytes[] memory args,
        uint64 txStatus,
        bytes[] memory signatures,
        bool isEncrypt) payable external {
        
        checkIndex();

        checkMultiSigns();

        updateIndex();

        (bool success, bytes memory returnData) = address(destAddress).ccall(abi.encodeWithSignature(callFunc, args));

        return (success, returnData);
}
```
有以下几种场景调用该方法：

**中继模式**：

1. 正常情况下目的链接收跨链请求，此时typ为IBTP_INTERCHAIN，txStatus为BEGIN
2. 一对多情况下，中继链上该事务最终为失败，需要在来源链和目的链进行回滚。目的链执行回滚，此时typ为IBTP_INTERCHAIN，txStatus为FAIL
3. 中继链未在指定高度前收到目的链的回执，在中继链上该事务已经设置成回滚，目的链执行回滚，此时typ为IBTP_INTERCHAIN，txStatus为ROLLBACK
4. 来源链网关与中继链节点间由于超时而引起来源链回滚，该场景下来源链会发出一个新的相同IBTP的typ为IBTP_ROLLBACK跨链交易，对目的链的index进行递增，此时txStatus为BEGIN，且callFunc为空

**直连模式**：

1. 正常情况下目的链接收跨链请求，此时typ为IBTP_INTERCHAIN
2. 来源链网关与目的链网关由于超时而引起来源链回滚，该场景下来源链会发出一个新的typ为IBTP_ROLLBACK的跨链交易，对目的链的index进行递增

### 6. 来源链接收跨链回执
来源链broker接收跨链交易回执的方法定义如下：

```solidity
// 来源链收到IBTP Receipt时或者来源链与调用的方法
// srcAddr：来源服务ID
// dstChainServiceID：IBTP to，目的服务ID
// index：IBTP index
// typ：IBTP type，IBTP_RECEIPT_SUCCESS/IBTP_RECEIPT_FAILURE/IBTP_INTERCHAIN
// result：调用目的链服务返回的内容
// txStatus：中继链上该跨链交易事务的状态ji
// signatures：中继链多签

function invokeReceipt(
        string memory srcAddr,
        string memory dstFullID,
        uint64 index,
        uint64 typ,
        bytes[] memory result,
        uint64 txStatus,
        bytes[] memory signatures) payable external
```

有以下几种场景调用该方法：

**中继模式**：

1. 正常情况下来源链接收跨链交易回执，此时typ字段可能为IBTP_RECEIPT_SUCCESS或IBTP_RECEIPT_FAILURE，txStatus可能为SUCCESS/FAILURE，根据txStatus决定对来源服务进行回调还是回滚
2. 中继链未在超时块高前收到目的链回执，通知来源服务进行回滚，此时typ字段为IBTP_INTERCHAIN，txStatus为FAILURE，并且多签的的数据为：

```go
from | to | index | typ | callFunc | args | txStatus
```

**直连模式**：

1. 正常情况下来源链接收跨链交易回执，此时typ字段可能为IBTP_RECEIPT_SUCCESS或IBTP_RECEIPT_FAILURE，直接根据typ字段决定对来源服务进行回调还是回滚

该方法执行后将rollbackProposal中对应的IBTP ID的元素删除。