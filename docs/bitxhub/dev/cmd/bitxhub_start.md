
# bitxhub start

该命令`bitxhub start`显示BitXHub的版本信息。
它会在命令行显示版本、构建日期、操作系统/架构和Go编译器版本。
例如:
```
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 start                         

BitXHub version: dev-fixx-1.6-2a2f39f                                                                                                           
App build date: 2021-11-25T16:43:19                                                                                                                 
System version: darwin/amd64                                                                                                                              
Golang version: go1.14.15                                                                                                                                 
                                                                                                                                                                                                
time="2021-11-29T18:07:37.722" level=info msg="MemPool batch size = 200" module=order
time="2021-11-29T18:07:37.722" level=info msg="MemPool tx slice size = 200" module=order
time="2021-11-29T18:07:37.722" level=info msg="MemPool batch seqNo = 6" module=order
time="2021-11-29T18:07:37.723" level=info msg="MemPool pool size = 50000" module=order
time="2021-11-29T18:07:37.723" level=info msg="Raft localID = 1" module=order
time="2021-11-29T18:07:37.723" level=info msg="Raft lastExec = 6  " module=order
time="2021-11-29T18:07:37.723" level=info msg="Raft snapshotCount = 1000" module=order
time="2021-11-29T18:07:37.723" level=info msg="GRPC service started" module=api port=60011
time="2021-11-29T18:07:37.723" level=info msg="Gateway service started" module=api port=9091
```

## 参数
- passwd：指定节点私钥密码
- config：指定节点配置路径，一般指bitxhub.toml
- network：指定节点p2p网络配置文件，一般指network.toml
- order：指定节点共识配置文件，一般指oeder.toml

**若不指定以上配置，则默认至bitxhub --repo路径下寻找**
```
NAME:
   bitxhub start - Start a long-running start process

USAGE:
   bitxhub start [command options] [arguments...]

OPTIONS:
   --passwd value   bitxhub key password
   --config value   bitxhub config path
   --network value  bitxhub network config path
   --order value    bitxhub order config path

```
