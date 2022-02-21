# 中继模式管理

中继链支持对应用链的管理，包括注册、更新、冻结、注销应用链等功能。

## 1. 应用链注册
### pier appchain method register 
对于需要加入跨链网络使用中继链进行跨链的应用链，需要首先由应用链管理员向中继链注册应用链，参数说明如下：

```shell
NAME:
   Pier appchain method register - Register appchain did method and info to bitxhub

USAGE:
   Pier appchain method register [command options] [arguments...]

OPTIONS:
   --admin-key value   Specific admin key path
   --method value      Specific did sub method name(like appchain)
   --doc-addr value    Specify the addr of did document
   --doc-hash value    Specify the hash of did document
   --name value        Specific appchain name
   --type value        Specific appchain type
   --desc value        Specific appchain description
   --version value     Specific appchain version
   --validators value  Specific appchain validators path
   --consensus value   Specific appchain consensus type
   --rule value        Specific appchain rule
   --rule-url value    Specific appchain rule url
   --reason value      Specify governance reason
```
**参数解释：**

* `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录。
* `--admin-key`：必选参数，管理员私钥地址，一般为admin.json。
* `--method`：必选参数，did方法名。
* `--doc-addr`：必选参数，指定did文件地址。
* `--doc-hash`：必选参数，指定did文件的哈希值。
* `--name`：必选参数，指定的应用了名称。
* `--type`：指定的应用链类型。如fabric、flato。
* `--desc`：必选参数，对应用链的描述信息。
* `--version`：必选参数，指定应用链版本信息。
* `--validators`：必选参数，指定应用链的验证人信息所在的文件路径。
* `--consensus`：必选参数，指定p应用链的共识类型，如rbft、raft等。
* `--rule`：必选参数，指定验证规则地址。
* `--rule_url`：指定验证规则url。
* `--addr`：可选参数，指定要连接的中继链节点地址，如果不指定，默认使用$repo目录下pier.toml中指定的BitXHub节点地址。

该命令向中继链发送一笔应用链注册的交易，中继链以交易的from（即当前pier公钥的地址）作为应用链的ID，生成一个应用链注册的提案。

中继链管理员需要对提案进行投票，命令如下：
```shell
bitxhub [--repo <repository>] client governance vote --id <proposal id> --info <voting information>  --reason <reason to vote>
```
**参数解释：**

* `--repo`：可选参数，指定bitxhub节点配置文件所在目录，如果不指定，默认使用$HOME/.bitxhub目录。
* `--id`：必选参数，指定提案id。
* `--info`：必选参数，指定投票信息，approve或者reject。
* `--reason`：必选参数，指定投票的原因。

**示例说明**
比如进行fabric应用链的注册,现在 --doc-addr 和 --doc-hash 、--rule-url没有真正用到，使用下面示例的即可，验证规则需要提前注册（也可使用示例中内置验证规则地址）。验证者信息需要提前拷贝到repo目录下。注意在注册应用链时，确保已经正常启动中继链：

```shell
# 具体样例

$ cd <bitxhub_project> && make install

# 1.启动四节点bitxhub集群
$ make cluster

# 2. bitxhub管理员向pier管理员转账。注意更改为bitxhub项目地址，to地址通过pier --repo /.pier id 获得,切换到pier配置所在地址
$ cd {PIER_REPO} && PIER_ID=$(pier --repo $(pwd) id)
$ bitxhub client tx send --key ~/goproject/meshplus/bitxhub/scripts/build/node1/key.json --to $PIER_ID --amount 100000000000000000000000000
```


#### 1.1 Pier命令行发起应用链注册

=== "Ethereum"

    ```shell
    $ pier --repo $(pwd) init relay
    # 3. 初始化pier，修改相关配置信息，参考但终极模式跨链网关部署使用教程
    $ cp ~/goproject/meshplus/pier-client-ethereum/config/ether.validators ./ether/
    ………
    
    
    $ pier --repo $(pwd) appchain method register --admin-key ./key.json --method appchain0xC32db5F3A0573b4634C6c106485e31C84ff95f74 --doc-addr ./ipfs/QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --doc-hash QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --name ethTest --type ether --desc="test for ether" --version v1.0.0 --validators ./ether/ether.validators --consensus pow --rule 0x00000000000000000000000000000000000000a2 --rule-url http://localHost
    Register appchain method info for did:bitxhub:ethappchain:. successfully, wait for proposal 0xC32db5F3A0573b4634C6c106485e31C84ff95f74-0 to finish.
    ```
    
    如上例所示，应用链管理员提交应用链注册请求，应用链DID为`did:bitxhub:ethappchain:.`，提案号为`0xC32db5F3A0573b4634C6c106485e31C84ff95f74-0`。

=== "Fabric"

    ```shell
    $ pier --repo $(pwd) init relay
    # 3. 初始化pier，修改相关配置信息，参考但终极模式跨链网关部署使用教程
    $ cp ~/goproject/meshplus/pier-client-fabric/config/fabric.validators ./fabric/
    ………
    
    
    $ pier --repo $(pwd) appchain method register --admin-key ./key.json --method appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc --doc-addr ./ipfs/QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --doc-hash QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --name fabricTest --type fabric --desc="test for fabric" --version v1.0.3 --validators ./fabric/fabric.validators --consensus raft --rule 0x00000000000000000000000000000000000000a2 --rule-url http://localHost
    
    Register appchain method info for did:bitxhub:fabricappchain:. successfully, wait for proposal 0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc-0 to finish.
    ```
    如上例所示，应用链管理员提交应用链注册请求，应用链DID为`did:bitxhub:fabricappchain:.`，提案号为`0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc-0`。
    
#### 1.2 中继链节点依次投票
中继链管理员需要对该提案进行审核并进行投票，命令如下：

=== "Ethereum"

    ```shell
    $ PROPOSAL_ID=上面得到的ID
    
    # 注意切换到bitxhub项目目录下
    $ bitxhub --repo ./scripts/build/node1 client governance vote --id $PROPOSAL_ID --info approve --reason "eth appchain register"
    vote successfully!
    
    $ bitxhub --repo ./scripts/build/node2 client governance vote --id $PROPOSAL_ID --info approve --reason "eth
    appchain register"
    vote successfully!
    
    $ bitxhub --repo ./scripts/build/node3 client governance vote --id $PROPOSAL_ID --info approve --reason "eth appchain register"
    vote successfully!
    
    $ bitxhub --repo ./scripts/build/node1 client governance proposal query --id $PROPOSAL_ID
    
    ========================================================================================
    Id                                            ManagedObjectId                                                   Type         EventType  Status   A/R  IE/AE/TE  Special/Super  CreateTime           Description  EndReason
    --                                            ---------------                                                   ----         ---------  ------   ---  --------  -------------  ----------           -----------  ---------
    0xC32db5F3A0573b4634C6c106485e31C84ff95f74-0  did:bitxhub:appchain0xC32db5F3A0573b4634C6c106485e31C84ff95f74:.  AppchainMgr  register   approve  3/0  4/4/3     false/true     1645421807013839000               end of normal voting
    ========================================================================================
    * A/R：approve num / reject num
    * IE/AE/TE：the total number of electorate at the time of the initial proposal / the number of available electorate currently /the minimum threshold for votes to take effect
    * Special/Super：is special proposal / is super admin voted
    
    ```

=== "Fabric"

    ```shell
    $ PROPOSAL_ID=上面得到的ID
    
    # 注意切换到bitxhub项目目录下
    $ bitxhub --repo ./scripts/build/node1 client governance vote --id $PROPOSAL_ID --info approve --reason "fabric appchain register"
    vote successfully!
    
    $ bitxhub --repo ./scripts/build/node2 client governance vote --id $PROPOSAL_ID --info approve --reason "fabric
    appchain register"
    vote successfully!
    
    $ bitxhub --repo ./scripts/build/node3 client governance vote --id $PROPOSAL_ID --info approve --reason "fabric appchain register"
    vote successfully!
    
    $ bitxhub --repo ./scripts/build/node1 client governance proposal query --id $PROPOSAL_ID
    
    ========================================================================================
    Id                                            ManagedObjectId                                                   Type         EventType  Status   A/R  IE/AE/TE  Special/Super  CreateTime           Description  EndReason
    --                                            ---------------                                                   ----         ---------  ------   ---  --------  -------------  ----------           -----------  ---------
    0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc-0  did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.  AppchainMgr  register   approve  3/0  4/4/3     false/true     1645424563986591000               end of normal voting
    ========================================================================================
    * A/R：approve num / reject num
    * IE/AE/TE：the total number of electorate at the time of the initial proposal / the number of available electorate currently /the minimum threshold for votes to take effect
    * Special/Super：is special proposal / is super admin voted
    
    ```
可以看到该提案已经投票通过，应用链注册成功。

## 2. 获取应用链信息

### Pier appchain get

获取应用链注册后的相关信息。命令如下：

```shell
pier --repo <repository> appchain get --admin-key <admin_key_json> --id <appchian_did>
```

### 示例说明

获取当前应用链相关信息，命令执行如下：

=== "ethereum"

    ```shell
    # 具体样例
    $ pier --repo $(pwd) appchain get --admin-key ./key.json --id did:bitxhub:appchain0xC32db5F3A0573b4634C6c106485e31C84ff95f74:.
    
    {"id":"did:bitxhub:appchain0xC32db5F3A0573b4634C6c106485e31C84ff95f74:.","name":"ethTest","validators":"0x000f1a7a08ccc48e5d30f80850cf1cf283aa3abd,0xe93b92f1da08f925bdee44e91e7768380ae83307,0xb18c8575e3284e79b92100025a31378feb8100d6,0x856E2B9A5FA82FD1B031D1FF6863864DBAC7995D","consensus_type":"pow","status":"available","chain_type":"ether","desc":"test for ether","version":"v1.1.0","public_key":"0x424654476176323867484f355a67357338624e794e6178736b707965434d4764744a6d615464735a4c545861386630326f51787934415475644f53334c7a2f43666a726b79445957304841566e6a6558586d32554c34553d","owner_did":"did:bitxhub:appchain0xC32db5F3A0573b4634C6c106485e31C84ff95f74:0xC32db5F3A0573b4634C6c106485e31C84ff95f74","did_doc_addr":"./ipfs/QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi","did_doc_hash":"QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi","rule":"0x00000000000000000000000000000000000000a2","rule_url":"http://localHost","fsm":{}}
    ```

=== "fabric"

    ```shell
    # 具体样例
    $ pier --repo $(pwd) appchain get --admin-key ./key.json --id did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.

    {"id":"did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.","name":"fabricTest","validators":"-----BEGIN CERTIFICATE-----\nMIICKTCCAc+gAwIBAgIRAIBO31aZaSZoEYSy2AJuhJcwCgYIKoZIzj0EAwIwczEL\nMAkGA1UEBhMCVVMxEzARBgNVBAgTCkNhbGlmb3JuaWExFjAUBgNVBAcTDVNhbiBG\ncmFuY2lzY28xGTAXBgNVBAoTEG9yZzIuZXhhbXBsZS5jb20xHDAaBgNVBAMTE2Nh\nLm9yZzIuZXhhbXBsZS5jb20wHhcNMjAwMjA1MDgyMjAwWhcNMzAwMjAyMDgyMjAw\nWjBqMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMN\nU2FuIEZyYW5jaXNjbzENMAsGA1UECxMEcGVlcjEfMB0GA1UEAxMWcGVlcjEub3Jn\nMi5leGFtcGxlLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABG3jszFPTbGm\ndAYg2BxmHMTDKfQReNw3p9ttMK130qF5lQo5zLBG8Sa3viOCLnvjjg6A/P+yKnwv\nisI/jEVE8T2jTTBLMA4GA1UdDwEB/wQEAwIHgDAMBgNVHRMBAf8EAjAAMCsGA1Ud\nIwQkMCKAIMVL+daK7nMGr2/AQIXTSPFkdd3UiPVDkWtkh5ujnalEMAoGCCqGSM49\nBAMCA0gAMEUCIQDMYOQiYeMiQZTxlRkj/3/jjYvwwdCcX5AWuFmraiHkugIgFkX/\n6uiTSD0lz8P+wwlLf24cIABq2aZyi8q4gj0YfwA=\n-----END CERTIFICATE-----\n","consensus_type":"raft","status":"available","chain_type":"fabric","desc":"test for fabric","version":"v1.0.3","public_key":"0x42466555443339355037652b30307877664d4d39586252315543544f597337424568396579564a4f6e75762f446c474d45423936532b2b367a2f335669534846715458547236766648446179747a484933644469714d633d","owner_did":"did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc","did_doc_addr":"./ipfs/QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi","did_doc_hash":"QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi","rule":"0x00000000000000000000000000000000000000a2","rule_url":"http://localHost","fsm":{}}
    ```

## 3. 部署验证规则
### [pier client rule deploy](../../../dev/cmd/bitxhub_cmd/)
在注册完应用链后，还需要在网关部署验证规则，命令如下：
```shell
pier --repo <repository> client rule deploy --path <validating_wasm_path> --method <appchain0x....> --admin-key <admin_key.json> -rule-url <any_url>
```

**注意：以太坊验证规则默认仅支持内置验证规则，地址为`0x00000000000000000000000000000000000000a2`，所以可不对验证规则进行单独部署。**

**示例说明**

### 3.1 部署并注册验证规则
=== "Fabric"

    ```shell
    # 部署并注册验证规则
    $ pier --repo $(pwd) rule deploy --path ./fabric/validating.wasm --method appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc --admin-key ./key.json --rule-url http://github.com
    
    # 其中0x508D8FB42b5Dc35C96a010681ABEC977B87ab8Ba为验证规则地址 
    Deploy rule to bitxhub for appchain appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc successfully: 0x508D8FB42b5Dc35C96a010681ABEC977B87ab8Ba
    Register rule to bitxhub for appchain did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:. successfully.
    
    # 在中继链查询应用链的验证规则列表，显示该验证规则已部署，但并未正式使用该验证规则
    $ bitxhub --repo ./scripts/build/node1 client governance rule all --id did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.

    ChainId                                                           RuleAddress                                 Status     Master
    -------                                                           -----------                                 ------     ------
    did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.  0x00000000000000000000000000000000000000a2  available  true
    did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.  0x508D8FB42b5Dc35C96a010681ABEC977B87ab8Ba  bindable   false
    ```

### 3.2 更新验证规则
=== "Fabric"
    
    ```shell
    # 更新为当前部署的验证规则，addr为验证规则的地址，通过部署验证规则输出的结果得到该地址
    $pier --repo $(pwd) rule update --addr 0x508D8FB42b5Dc35C96a010681ABEC977B87ab8Ba --method appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc --admin-key ./key.json  --reason reason
    
    Update master rule to bitxhub for appchain did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:. successfully, wait for proposal 0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc-1 to finish
    
    # 在中继链查询应用链的验证规则列表，显示验证规则master已从内置验证规则更新为当前验证规则
    $ bitxhub --repo ./scripts/build/node1 client governance rule all --id did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.
    ChainId                                                           RuleAddress                                 Status     Master
    -------                                                           -----------                                 ------     ------
    did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.  0x00000000000000000000000000000000000000a2  bindable   false
    did:bitxhub:appchain0xa9189942f259988B5Bb0156E95b5717f3eA18Ccc:.  0x508D8FB42b5Dc35C96a010681ABEC977B87ab8Ba  available  true
    ```

## 2. 更新应用链
### pier appchain update
如果应用链发生了更改，比如验证人信息发生了变化，需要更新其在中继链上的应用链信息。参数说明如下：
```shell
NAME:
   Pier appchain update - update appchain in bitxhub

USAGE:
   Pier appchain update [command options] [arguments...]

OPTIONS:
   --admin-key value       Specific admin key path
   --id value              Specify appchain id(did)
   --doc-addr value        Specify appchain did doc addr
   --doc-hash value        Specify appchain did doc hash
   --name value            Specify appchain name
   --type value            Specify appchain type
   --desc value            Specify appchain description
   --version value         Specify appchain version
   --validators value      Specify appchain validators path
   --consensus-type value  Specify appchain consensus type
   --reason value          Specify governance reason
```

**参数解释：**

* `--repo`：可选参数，指定pier配置文件所在目录，如果不指定，默认使用$HOME/.pier目录。
* `--admin-key`：必选参数，管理员私钥地址，一般为admin.json。
* `--id`：必选参数，应用链的did。
* `--doc-addr`：必选参数，指定did文件地址。
* `--doc-hash`：必选参数，指定did文件的哈希值。
* `--name`：必选参数，指定的应用了名称。
* `--type`：指定的应用链类型。如fabric、flato。
* `--desc`：必选参数，对应用链的描述信息。
* `--version`：必选参数，指定应用链版本信息。
* `--validators`：必选参数，指定应用链的验证人信息所在的文件路径。
* `--consensus-type`：必选参数，指定p应用链的共识类型，如rbft、raft等。
* `--reason`：可选参数，描述信息。
* `--addr`：可选参数，指定要连接的中继链节点地址，如果不指定，默认使用$repo目录下pier.toml中指定的BitXHub节点地址。

**示例说明**
比如进行fabric应用链的验证人信息发生变化，需要更新应用链，命令执行如下：
```shell
# 具体样例
$ pier --repo $(pwd) appchain update --admin-key ./key.json --id did:bitxhub:fabricappchain:. --doc-addr ./ipfs/QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --doc-hash QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --name fabricTest --type fabric --desc="test for fabric" --version v1.1.0 --validators./fabric/fabric.validators --consensus-type raft

the update request was submitted successfully，, proposal id is 0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-1
```

管理员进行审核并投票，与注册应用链部分一致，不再赘述。




## 3. 激活应用链
### pier appchain activate
如果冻结的应用链恢复正常，应用链管理员可以申请激活应用链。命令如下：
```shell
$ pier --repo <repository> appchain activate --admin-key <admin_key_json> --id <appchian_did>
```

**示例说明**
比如对之前已经冻结的应用链进行激活，命令执行如下：
```shell
$ pier --repo $(pwd) appchain activate --admin-key ./key.json --id did:bitxhub:fabricappchain:.
INFO[11:01:25.884] Establish connection with bitxhub localhost:60011 successfully  module=rpcx
the activate request was submitted successfully, proposal id is 0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-2
```

管理员进行审核并投票，与注册应用链部分一致，不再赘述。



## 4. 注销应用链

### pier appchain logout
如果应用链退出跨链系统，不再进行跨链，应用链管理员可以向中继链提交注销应用链的提案。命令如下：
```shell
pier --repo <repository> appchain logout --admin-key <admin_key_json> --id <appchian_did>
```

**示例说明**
比如对之前激活的应用链进行注销，命令执行如下：
```shell
$ pier appchain logout --admin-key ./key.json --id did:bitxhub:fabricappchain:.
the logout request was submitted successfully, proposal id is 0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-3
```

管理员进行审核并投票，与注册应用链部分一致，不再赘述。




