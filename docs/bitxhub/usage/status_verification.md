## 系统验证和检查

在部署完跨链系统之后，通常需要检查或监控系统的运行状态，这里提供一些检查和监控BitXHub系统的方法。

### 节点进程状态验证

可以通过ps命令查看节点进程的运行状态，示例如下：

```
ps aux|grep bitxhub
ps aux|grep pier
```

详细过程请参考[节点状态检查](/bitxhub/operation/node_status_check)。

### 节点日志检查

如果是在终端前台启动的节点，那么日志会实时打印在终端上，观察其无报错即可；

如果是通过nohup等后台启动的节点，在节点主配置目录的logs文件夹中就是节点的日志文件，打开即可检查日志，一般情况下除了出块，bitxhub节点之间会定时相互`ping`其它节点并返回延时信息，可以简单看到节点集群之间的网络状态。

## 跨链交易
在多条应用链以及合约部署成功后，可以发起跨链交易请求。这里，以id为ethappchain1和ethappchain2的两条应用链为例：

1. 在remix中找到ethappchain1和ethappchain2的broker合约，然后找到audit方法审计transfer合约。其中，addr参数为transfer合约的地址，status设置为1。
![!](../../../assets/set_audit.png)



2. transfer合约审计通过后，我们需要分别为两条应用链新建一个账户。其中，ethappchain1的账户名为alice，账户金额为10000。ethappchain2的账户名为bob，账户金额为100。
![!](../../../assets/setbalance_forchain1.png)
![!](../../../assets/setbalance_forchain2.png)



3. 设置账户以及金额成功之后便可以发起跨链交易请求。在此，我们以alice向bob转账100做一个示例。
在remix调用ethappchain1的transfer合约中的transfer方法，transfer合约中的四个参数分别为destChainServiceID，sender，receiver以及amount。 需要注意的是，destChainServiceID参数的格式为:(中级链id:目的链id:transfer合约地址)。示例说明如下：
在示例跨链交易请求中，输入的destChainServiceID是1356:ethappchain2:0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11。
其中，1356为中继链id，ethappchain2为目的链id，0x30c5D3aeb4681af4D13384DBc2a717C51cb1cc11为transfer合约地址。
跨链交易成功后，alice账户的金额应该为9900，bob账户的金额应该为200。跨链交易结束后，可以调用transfer合约中的getBalance方法得到alice和bob的账户金额。
![!](../../../assets/transfer.png)


4. 发起跨链交易请求成功后，通过调用getBalance方法可以得到此时alice和bob账户的金额。
![!](../../../assets/getbalance_forchain1.png)
![!](../../../assets/getbalance_forchain2.png)

alice和bob的账户金额是正确的，说明跨链交易成功。