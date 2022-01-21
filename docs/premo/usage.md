# Premo使用文档

Premo是BitXHub跨链系统测试框架，目前支持系统集成测试、接口测试和压力测试

## 安装

### 获取源码

```shell
git clone git@github.com:meshplus/premo.git
```

### 编译

进入premo工程目录:

```shell
cd premo
make install
```

## 初始化

```shell
premo init
```

## 基本使用

```shell
premo [global options] command [command options] [arguments...]
```

### COMMANDS:

- `init` init config home for premo
- `version` Premo version
- `test` test bitxhub function
- `pier` Start or stop the pier
- `bitxhub` Start or stop the bitxhub cluster
- `appchain` Bring up the appchain network
- `interchain` Start or Stop the interchain system
- `status` List the status of instantiated components
- `help, h` Shows a list of commands or help for one command

### GLOBAL OPTIONS:

- `--repo value`  Premo storage repo path
- `--help, -h`    show help (default: false)

## 集成测试

进入premo的工程目录

```shell
cd premo
make bitxhub-tester
```

注意：集成测试默认前置条件是本机已启动bitxhub四节点集群（可在bitxhub工程目录下通过`make cluster`命令启动）

## 接口测试

进入premo的工程目录

```shell
cd premo
make http-tester
```

注意：集成测试默认前置条件是本机已启动bitxhub四节点集群（可在bitxhub工程目录下通过`make cluster`命令启动）

## 压力测试

test命令用于压测bitxhub的TPS性能。使用下面的命令获取使用帮助信息：

```text
premo test --help
```

帮助信息如下：

```shell
NAME:
   premo test - test bitxhub function

USAGE:
   premo test [command options] [arguments...]

OPTIONS:
   --concurrent value, -c value           concurrent number (default: 100)
   --tps value, -t value                  all tx number (default: 500)
   --duration value, -d value             test duration (default: 60)
   --key_path value, -k value             Specify key path
   --remote_bitxhub_addr value, -r value  Specify remote bitxhub address (default: "localhost:60011")
   --type value                           Specify tx type: interchain, data, transfer (default: "transfer")
   --appchain value                       Specify appchain type: fabric, flato, eth (default: "flato")
   --help, -h                             show help (default: false)                           show help (default: false)

```

`--concurrent`或者`-c`指定并发量；

`--tps`或者`-t`指定每秒交易数量；

`--duration`或者`-d`指定压测时间；

`--key_path`或者`-k`指定私钥路径；

`--remote_bitxhub_addr`或者`-r`指定bitxhub的地址；

`--type`指定交易类型，其中`transfer`是普通转账交易，`data`是调用BVM交易，`interchain`是跨链交易；

`--appchain`指定交易的应用链类型，fabric类型使用`fabric复杂验证规则`，flato使用`wasm验证规则`，eth使用`HappyRule验证规则`

压测完成后会打印压测的实际情况：

```text
$ premo test -c 50 -t 3000 -d 1000
INFO[0000] Premo configuration                           concurrent=50 duration=1000 tps=3000 type=transfer
INFO[0010] generate all bees                             number=50
2022-01-21 09:59:18 [INFO] [/home/jiuhuche120/code/premo/internal/bitxhub/bitxhub.go:200] starting broker
INFO[0010] start all bees                                number=50
INFO[0011] current tps is 0, average tx delay is NaNms, max tx delay is 0.000000ms
INFO[0012] current tps is 3000, average tx delay is 468.366803ms, max tx delay is 659.376700ms
INFO[0013] current tps is 3000, average tx delay is 454.559976ms, max tx delay is 662.409000ms
INFO[0014] current tps is 3000, average tx delay is 431.104824ms, max tx delay is 656.018800ms
INFO[0015] current tps is 3000, average tx delay is 468.891062ms, max tx delay is 703.613300ms
INFO[0016] current tps is 3000, average tx delay is 471.733843ms, max tx delay is 771.994700ms
INFO[0017] current tps is 3000, average tx delay is 605.849025ms, max tx delay is 841.901800ms
INFO[0018] current tps is 3000, average tx delay is 542.110394ms, max tx delay is 783.013200ms
INFO[0019] current tps is 3000, average tx delay is 628.977185ms, max tx delay is 844.139100ms
INFO[0020] current tps is 3000, average tx delay is 573.736820ms, max tx delay is 807.243500ms
INFO[0021] current tps is 3000, average tx delay is 521.247552ms, max tx delay is 809.016000ms
INFO[0022] current tps is 3000, average tx delay is 552.563437ms, max tx delay is 793.239000ms
INFO[0023] current tps is 3000, average tx delay is 489.615697ms, max tx delay is 750.089600ms
INFO[0024] current tps is 3000, average tx delay is 548.681905ms, max tx delay is 820.362800ms
INFO[0025] current tps is 3000, average tx delay is 644.761151ms, max tx delay is 893.975200ms
INFO[0026] current tps is 3000, average tx delay is 568.441867ms, max tx delay is 983.741300ms
INFO[0027] current tps is 2000, average tx delay is 658.258679ms, max tx delay is 980.029700ms
INFO[0028] current tps is 2538, average tx delay is 846.983366ms, max tx delay is 1352.342200ms
INFO[0029] current tps is 3056, average tx delay is 907.007390ms, max tx delay is 1415.590600ms
INFO[0030] current tps is 2915, average tx delay is 919.295356ms, max tx delay is 1495.179300ms
INFO[0031] current tps is 2256, average tx delay is 1024.412810ms, max tx delay is 1628.914200ms
INFO[0032] current tps is 3355, average tx delay is 1070.335550ms, max tx delay is 1654.329600ms
^Creceived interrupt signal, shutting down...
2022-01-21 09:59:43 [INFO] [/home/jiuhuche120/code/premo/internal/bitxhub/bitxhub.go:312] Bees are quiting, please wait...
INFO[0036] finish testing                                duration=25.3915894 number=61620 tps=2426.7877671307706 tx_delay=637.4013231856541
```

