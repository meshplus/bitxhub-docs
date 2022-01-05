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

### 示例说明
比如进行fabric应用链的注册,现在 --doc-addr 和 --doc-hash 、--rule-url没有真正用到，使用下面示例的即可，验证规则需要提前注册（也可使用示例中内置验证规则地址）。验证者信息需要提前拷贝到repo目录下。注意在注册应用链时，确保已经正常启动中继链：
```shell
# 具体样例

$ cd <bitxhub_project> && make install

# 启动bitxhub
$ make cluster

# bitxhub管理员向pier管理员转账。注意更改为bitxhub项目地址
$ bitxhub client tx send --key ~/goproject/meshplus/bitxhub/scripts/build/node1/key.json --to 0xf2201f28368706EF75e3199b4f4DE2a24bA85CAe --amount 100000000000000000000000000

$ pier --repo $(pwd) init relay
# 可以使用对应插件项目下的验证者信息，注意更改项目路径
$ cp ~/goproject/meshplus/pier-client-fabric/config/fabric.validators ./fabric/

$ bitxhub client tx send --key ~/code/bitxhub/scripts/build/node1/key.json --to 0xf2201f28368706EF75e3199b4f4DE2a24bA85CAe --amount 100000000000000000000000000

$ pier --repo $(pwd) appchain method register --admin-key ./key.json --method fabricappchain --doc-addr ./ipfs/QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --doc-hash QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi --name fabricTest --type fabric --desc="test for fabric" --version v1.0.3 --validators ./fabric/fabric.validators --consensus raft --rule 0x00000000000000000000000000000000000000a2 --rule-url http://localHost

Register appchain method info for did:bitxhub:fabricappchain:. successfully, wait for proposal 0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-0 to finish.
```
如上例所示，应用链管理员提交应用链注册请求，应用链DID为did:bitxhub:fabricappchain:.，提案号为0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-0。中继链管理员需要对该提案进行审核并进行投票，命令如下：

如管理员对该提案审核后，认为该应用链提交对信息无误，投票通过，命令执行如下：
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
Id                                            ManagedObjectId               Type         EventType  Status   A/R  IE/AE/TE  Special/Super  CreateTime           Description  EndReason
--                                            ---------------               ----         ---------  ------   ---  --------  -------------  ----------           -----------  ---------
0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-0  did:bitxhub:fabricappchain:.  AppchainMgr  register   approve  3/0  4/4/3     false/true     1641348118114187000               end of normal voting
========================================================================================
* A/R：approve num / reject num
* IE/AE/TE：the total number of electorate at the time of the initial proposal / the number of available electorate currently /the minimum threshold for votes to take effect
* Special/Super：is special proposal / is super admin voted

```
可以看到该提案已经投票通过，应用链注册成功。

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

### 示例说明
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

### 示例说明
比如对之前已经冻结的应用链进行激活，命令执行如下：
```shell
$ pier --repo $(pwd) appchain activate --admin-key ./key.json --id did:bitxhub:fabricappchain:.
INFO[11:01:25.884] Establish connection with bitxhub localhost:60011 successfully  module=rpcx
the activate request was submitted successfully, proposal id is 0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-2
```

管理员进行审核并投票，与注册应用链部分一致，不再赘述。

## 5. 注销应用链
### pier appchain logout
如果应用链退出跨链系统，不再进行跨链，应用链管理员可以向中继链提交注销应用链的提案。命令如下：
```shell
pier --repo <repository> appchain logout --admin-key <admin_key_json> --id <appchian_did>
```

### 示例说明
比如对之前激活的应用链进行注销，命令执行如下：
```shell
$ pier appchain logout --admin-key ./key.json --id did:bitxhub:fabricappchain:.
the logout request was submitted successfully, proposal id is 0xCc9b389cEA6b1E2845a895829126B0a15a1cdA6F-3
```

管理员进行审核并投票，与注册应用链部分一致，不再赘述。

### Pier appchain get 

获取应用链注册后的相关信息。命令如下：

```shell
pier --repo <repository> appchain get --admin-key <admin_key_json> --id <appchian_did>
```

### 示例说明

获取当前应用链相关信息，命令执行如下：

```shell
# 具体样例
$ pier --repo $(pwd) appchain get --admin-key ./key.json --id did:bitxhub:fabricappchain:.

{"id":"did:bitxhub:fabricappchain:.","name":"fabricTest1","validators":"-----BEGIN CERTIFICATE-----\nMIICKTCCAc+gAwIBAgIRAIBO31aZaSZoEYSy2AJuhJcwCgYIKoZIzj0EAwIwczEL\nMAkGA1UEBhMCVVMxEzARBgNVBAgTCkNhbGlmb3JuaWExFjAUBgNVBAcTDVNhbiBG\ncmFuY2lzY28xGTAXBgNVBAoTEG9yZzIuZXhhbXBsZS5jb20xHDAaBgNVBAMTE2Nh\nLm9yZzIuZXhhbXBsZS5jb20wHhcNMjAwMjA1MDgyMjAwWhcNMzAwMjAyMDgyMjAw\nWjBqMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMN\nU2FuIEZyYW5jaXNjbzENMAsGA1UECxMEcGVlcjEfMB0GA1UEAxMWcGVlcjEub3Jn\nMi5leGFtcGxlLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABG3jszFPTbGm\ndAYg2BxmHMTDKfQReNw3p9ttMK130qF5lQo5zLBG8Sa3viOCLnvjjg6A/P+yKnwv\nisI/jEVE8T2jTTBLMA4GA1UdDwEB/wQEAwIHgDAMBgNVHRMBAf8EAjAAMCsGA1Ud\nIwQkMCKAIMVL+daK7nMGr2/AQIXTSPFkdd3UiPVDkWtkh5ujnalEMAoGCCqGSM49\nBAMCA0gAMEUCIQDMYOQiYeMiQZTxlRkj/3/jjYvwwdCcX5AWuFmraiHkugIgFkX/\n6uiTSD0lz8P+wwlLf24cIABq2aZyi8q4gj0YfwA=\n-----END CERTIFICATE-----\n","consensus_type":"raft","status":"updating","chain_type":"fabric","desc":"test for fabric","version":"v1.1.0","public_key":"0x42444f30475a697746533934706e344169712b77365442417757663635787637724f616b4e786d536344465766394970546f694f7239712b6f6259386c514546617a434b316c714e32533446306b426e633542313034413d","owner_did":"","did_doc_addr":"./ipfs/QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzoNUi","did_doc_hash":"QmQVxzUqN2Yv2UHUQXYwH8dSNkM8ReJ9qPqwJsf8zzo111","fsm":{}}
```

