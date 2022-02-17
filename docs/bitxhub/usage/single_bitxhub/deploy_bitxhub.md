# 中继链部署

中继链用于应用链的跨链管理，以及跨链交易的可信验证与可靠路由，是一种实现IBTP协议的开放许可链。下面通过快速启动BitXHub节点与修改配置启动BitXHub节点两种方式依次进行说明。

## 安装包获取

#### <span id="jump">源码编译</span>

您可以自行拉取BitXHub项目的源码，然后在本地编译BitXHub及插件的二进制文件，具体操作步骤可参考如下：

```shell
# 1. 首先拉取bitxhub项目源代码
git clone https://github.com/meshplus/bitxhub.git

# 2. 进入bitxhub目录，切换到指定的分支或版本后编译bitxhub二进制
cd bitxhub && git checkout v1.6.4 && make build

# 注意⚠️：首次编译需要在build之前先执行 make prepare 完成依赖安装
# 编译完成后可以在项目的bin目录下看到刚刚生成的bitxhub二进制文件，可以确认下bitxhub版本是v1.6.4
./bin/bitxhub version

# 3. 接下来需要编译共识插件，进入到 internal/plugins 目录进行编译
cd internal/plugins && make plugins

# 编译完成后可以在项目的internal/plugins/build目录下看到刚刚生成的共识插件文件，raft.so和solo.so
```

经过以上的步骤，相信您已经编译出了部署中继链节点所需的二进制文件，中继链节点运行还需要外部依赖库，均在项目build目录下（Macos使用libwasmer.dylib，Linux使用libwasmer.so）,建议将得到的二进制和适配的依赖库文件拷贝到同一目录，方便之后的操作。

#### 二进制直接下载

除了源码编译外，我们也提供了直接下载BitXHub二进制的方式，下载地址链接如下：[BitXHub二进制包下载](https://github.com/meshplus/bitxhub/releases/tag/v1.6.4)，链接中已经包含了所需的二进制和依赖库，您也可以根据实际情况选择所需要的版本和系统下载，建议使用 BitXHub v1.6.4稳定版本。



## 快速启动BitXHub节点

获取到安装包后，接下来要根据您的实际情况修改配置文件。

- 如果您是在本地编译的二进制包，您也可以在项目根目录执行`make cluster`一键启动四节点raft共识的BitXHub集群，或者执行`make solo`一键启动单节点solo共识的BitXHub节点。

  ```shell
  # 启动四节点raft共识的BitXHub
  make cluster
  ```

  正常启动后，命令行将打印以下信息：

  ```shell
  cd internal/repo && packr
  rm -f imports/imports.go
  GO111MODULE=on go install -ldflags '-X "github.com/meshplus/bitxhub.BuildDate=2021-11-02T09:57:43" -X "github.com/meshplus/bitxhub.CurrentCommit=f3d0f0d" -X "github.com/meshplus/bitxhub.CurrentBranch=HEAD" -X "github.com/meshplus/bitxhub.CurrentVersion=v1.6.2"' ./cmd/bitxhub
  Install bitxhub successfully!
  ===> Generating 4 nodes configuration
  ===> Building plugin
  GO111MODULE=on go build --buildmode=plugin -o build/raft.so order/etcdraft/*.go
  ===> Staring cluster
  ```

  我们使用tmux工具进行4节点窗口管理，tmux 是一个终端复用器（terminal multiplexer），可以实现终端分屏的功能，在之前的`make prepare`命令将自动下载此工具。如需关闭tmux会话，使用以下命令即可：

  ```shell
  tmux kill-session -t bitxhub
  ```

- 如果您是直接下载的二进制安装包，为了简化操作，我们也提供了可以直接启动raft/solo共识的节点配置文件示例，其下载地址与二进制包一样，文件名以example开头。需要注意的是，raft共识的示例配置文件是四节点集群，solo共识的示例配置文件是单个节点。

  接下来只需将上一步下载的BitXHub二进制及对应插件拷贝到配置目录即可，具体操作如下：

  ```shell
  # 1. 解压二进制压缩包，本示例中二进制文件的下载位置为~/Downloads
  # 如下载到自定义下载目录，将该目录下的BitXHub与example二进制文件复制到当前的bitxhub目录下即可
  mkdir bitxhub && cd bitxhub
  mv {~/Downloads/bitxhub_v1.6.4_Darwin_x86_64.tar.gz, ~/Downloads/example_bitxhub_v1.6.4.tar.gz} .
  tar -zxvf bitxhub_v1.6.4_Darwin_x86_64.tar.gz
  
  # 2. 解压配置文件压缩包(以raft共识为例)
  mkdir -p raft-nodes/node{1,2,3,4}/plugins
  tar -zxvf example_bitxhub_v1.6.4.tar.gz -C raft-nodes/ --strip-components=1
  
  # 3. 将bitxhub、共识插件二进制和依赖库文件分别拷贝到4个节点的配置目录,共识插件放在配置文件的plugins目录底下
  echo raft-nodes/node{1,2,3,4} | xargs -n 1 cp -v {bitxhub,libwasmer.dylib}
  echo raft-nodes/node{1,2,3,4}/plugins | xargs -n 1 cp -v {raft.so,solo.so}
  
  # 注意⚠️：所有操作均在新建的bitxhub目录下完成。
  # 对于Linux系统依赖库文件是libwasmer.so
  # 以上操作均是示例，执行时二进制和配置文件压缩包的名称可能存在差异，需要根据实际情况进行调整
  ```

  解压后的bitxhub目录结构如下所示:

  ```shell
  .
  ├── bitxhub
  ├── bitxhub_v1.6.4_Darwin_x86_64.tar.gz
  ├── example_bitxhub_v1.6.4.tar.gz
  ├── libwasmer.dylib
  ├── raft-nodes
  │   ├── node1
  │   ├── node2
  │   ├── node3
  │   └── node4
  ├── raft.so
  └── solo.so
  
  5 directories, 6 files
  ```

  

  拷贝完成后，可以进入各个节点的配置目录，依次启动BitXHub节点即可，启动操作如下：

  ```sh
  cd raft-nodes/node1
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$(pwd)
  ./bitxhub --repo ./ start
  
  ...
  ...
  
  cd raft-nodes/node4
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$(pwd)
  ./bitxhub --repo ./ start
  ```

  **注意⚠️**：在启动节点时，macOS系统可能会拒绝打开该应用程序，如遇到此状况参考[打开来自身份不明开发者的 Mac App](https://support.apple.com/zh-cn/guide/mac-help/mh40616/mac)，该操作将重复几次。

待节点集群打印出bitxhub的LOGO，表示BitXHub集群开始正常工作。

![!](../../../assets/bitxhub.png)



## BitXHub配置文件说明

前面的提供的配置示例可以方便地快速启动BitXHub节点(集群)，但是没有对配置文件进行具体说明，考虑到您可能需要详细了解bitxhub的运行原理和配置项的具体含义，从而可以灵活配置和启动节点。接下来我们通过手动配置的方式对BitXHub的具体配置项进行具体说明。

**注意⚠️**：为了操作方便，本示例模拟了单机4个节点的场景，开发者可根据实际需求在不同服务器上部署多节点的bitXHub集群。

#### 1. 各节点获取二进制文件

切换到上述 [源码编译](#jump) 的bitxhub目录下，新建文件夹`raft-nodes`，拷贝所获取的相关安装包；

```shell
# 1. 新建四个节点文件夹
mkdir -p raft-nodes/node{1,2,3,4}/plugins

# 2. 将编译好的bithub、raft.so、libwasmer.dylib二进制复制到每个节点
echo raft-nodes/node{1,2,3,4} | xargs -n 1 cp -v {bin/bitxhub,build/libwasmer.dylib}

echo raft-nodes/node{1,2,3,4}/plugins | xargs -n 1 cp -v internal/plugins/build/raft.so 
注意⚠️：对于Linux系统依赖库文件是libwasmer.so
如果raft-nodes不在bitxhub源码目录下，命令行内容需要根据实际情况调整
```

#### 2. 初始化并修改各节点的配置文件

中继链节点主要包括bitxhub.toml、network.tom和order.toml配置文件，分别代表节点本身、节点网络以及节点共识方面的配置，其中order.toml一般使用默认配置即可，其它两个文件均需要根据实际部署情况进行修改。

```shell
# 1. 为各节点生成ca证书（包含受信任的中间ca）
# 生成根CA私钥ca.priv与根ca证书ca.cert
cd raft-nodes
bitxhub cert ca

# 生成中间ca(agency)的私钥
bitxhub cert priv gen --name agency

# 生成agency的csr，用于向根ca请求证书
bitxhub cert csr --key ./agency.priv --org Agency

# 由根ca颁发agency的证书agency.cert，表示根ca信任代理ca(agency)
bitxhub cert issue --key ca.priv --cert ca.cert --csr agency.csr

# 将ca证书链复制到各节点的certs目录
echo node{1,2,3,4}/certs | xargs -n 1 cp -v {ca.cert,agency.cert}
rm agency.csr

# 2. 初始化各节点配置，--repo 指定每个节点的配置文件目录

bitxhub --repo node1 init
bitxhub --repo node2 init
…………

# 3. 生成节点证书（以node1为例）
# 生成节点私钥，用于节点间libp2p网络通信
cd node1/certs && bitxhub cert priv gen --name node

# 生成node的scr，用于向agency请求证书
bitxhub cert csr --key ./node.priv --org Node1

# 由agency颁发节点的证书node.cert
bitxhub cert issue --key ../agency.priv --cert ../agency.cert --csr node.csr
rm node.csr

# 节点生成key.json, key.json用于区块签名等
bitxhub key gen

# 4. 修改node1中的配置文件，详细的文件配置说明见下文
vim bitxhub.toml
vim network.toml
…………
注意⚠️：本示例只展示了node1的操作，但所有节点均需要重复此操作，每个节点需要指定各自的repo
```

##### 2.1 bitxhub.toml文件配置修改说明

bitxhub.toml文件是BitXHub节点启动的主要配置文件。各配置项说明如下：

| 配置项         | 说明                                  |
| -------------- | ------------------------------------- |
| **solo**       | 是否按照单节点模式启动BitXHub         |
| **[port]**     | gateway、grpc、pprof和monitor服务端口 |
| **[pprof]**    | 性能剖析配置                          |
| **[monitor]**  | 监控服务配置                          |
| **[gateway]**  | 跨域配置                              |
| **[ping]**     | ping集群节点功能                      |
| **[security]** | 证书体系                              |
| **[limiter]**  | 流量控制配置                          |
| **[log]**      | 日志输出相关设置                      |
| **[cert]**     | 是否开启认证节点p2p通信证书           |
| **[order]**    | 共识模块，作为插件进行加载            |
| **[executor]** | 执行引擎类型                          |
| **[genesis]**  | 创世节点配置                          |

**在快速体验部署流程中，需要修改的配置一般只有port、order的信息，其它配置默认即可。** 以下为示例参考：

###### 端口号变更

**根据您机器实际分配的端口进行变更（以node1为例）：**

```shell
[port]
  gateway = 9091
  grpc = 60011
  pprof = 53121
  monitor = 40011
```

**注意⚠️**：在修改端口之前需要保证端口未被占用，如果启动时遇到`address already in use`之类的报错，请修改配置或者杀死进程释放该端口。

###### 共识算法类型选择

开源版本目前支持raft和solo，本示例选择raft算法。

```shell
[order]
  plugin = "plugins/raft.so" 
```

###### 管理员地址更新

以4个管理员节点为例，管理员地址通过如下命令获取：

```shell
bitxhub key address --path key.json
# 示例输出：0x769f719F58C6a7AE23F94274ce6435A3b5A2c99F
```

获取4个节点的address后，更新`bitxhub.toml`配置文件中genesis的address内容；

```shell
[genesis]
  [[genesis.admins]]
    address = "0x769f719F58C6a7AE23F94274ce6435A3b5A2c99F"
    weight = 1
…………
  [[genesis.admins]]
    address = "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"
    weight = 1
```



##### 2.2 network.toml文件配置修改

network.toml文件是BitXHub节点网络配置文件，各配置项说明如下：

| 配置项      | 说明                       |
| ----------- | -------------------------- |
| **N**       | 集群节点数量               |
| **id**      | 当前节点标识               |
| **new**     | 判断当前节点是新加入的节点 |
| **[nodes]** | 集群节点信息               |
| **account** | 节点验证者地址             |
| **hosts**   | 节点网络地址               |
| **id**      | 节点标识                   |
| **pid**     | p2p网络唯一标识            |

**在实际部署过程中，需要修改的配置一般是节点数量、nodes的信息，其它配置默认即可。** 以下为示例参考：

###### 配置当前节点集群的数量以及自身的id:

```shell
id = 1 # self id
n = 4 # the number of vp nodes
new = false # track whether the node is a new node
```

###### 配置集群中各个节点的信息

```shell
[[nodes]]
account = "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013"
hosts = ["/ip4/127.0.0.1/tcp/4001/p2p/"]
id = 1
pid = "QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL"
```

**注意⚠️：上面 account就是上一节中bitxhub节点的address地址，hosts中一般就改变ip地址即可，节点的pid信息可以通过如下命令获取：**

```shell
  bitxhub cert priv pid --path ./node1/node.priv
# 示例输出：QmTfiYrUL1naiv64uwNvVinvTvvkdJW4QEuRgECUhosAKc
```



##### 2.3 order.toml文件配置修改

order.toml文件是bitxhub共识配置文件。各配置项说明如下：

| 配置项 | 说明          |
| ------ | ------------- |
| [raft] | raft 相关配置 |
| [rbft] | rbft 相关配置 |
| [solo] | solo相关配置  |

配置示例如下（无特殊情况不要修改此配置）：

```shell
[raft]
batch_timeout               = "0.3s"  # Block packaging time period.
tick_timeout                = "0.1s" # TickTimeout is the internal logical clock for the Node by a single tick, Election timeouts and heartbeat timeouts are in units of ticks.
election_tick               = 10 # ElectionTick is the number of Node.Tick invocations that must pass between elections.
heartbeat_tick              = 1  # HeartbeatTick is the number of Node.Tick invocations that must pass between heartbeats.
max_size_per_msg            = 1048576 # 1024*1024, MaxSizePerMsg limits the max size of each append message.
max_inflight_msgs           = 500  # MaxInflightMsgs limits the max number of in-flight append messages during optimistic replication phase.
check_quorum                = true # Leader steps down when quorum is not active for an electionTimeout.
pre_vote                    = true # PreVote prevents reconnected node from disturbing network.
disable_proposal_forwarding = true # This prevents blocks from being accidentally proposed by followers.

    [raft.mempool]
        batch_size          = 200   # How many transactions should the primary pack.
        pool_size           = 50000 # How many transactions could the txPool stores in total.
        tx_slice_size       = 10    # How many transactions should the node broadcast at once
        tx_slice_timeout    = "0.1s"  # Node broadcasts transactions if there are cached transactions, although set_size isn't reached yet

    [raft.syncer]
        sync_blocks = 1 # How many blocks should the behind node fetch at once
        snapshot_count = 1000  # How many apply index(blocks) should the node trigger at once

[rbft]        #RBFT configurations
set_size         = 25    # How many transactions should the node broadcast at once
batch_size       = 500   # How many transactions should the primary pack before sending pre-prepare
pool_size        = 50000 # How many transactions could the txPool stores in total
vc_period        = 0     # After how many checkpoint periods( Blocks = 10 * vcperiod ) the primary gets cycled automatically. ( Set 0 to disable )
check_interval   = "3m"  # interval of the check loop
tolerance_time   = "5m"  # The max tolerance time duration (in seconds) of out-of-date
batch_mem_limit  = false # Indicates whether limit batch mem size or not
batch_max_mem    = 10000 # The max memory size of one batch

    [rbft.timeout]
    	sync_state        = "3s"  # How long to wait quorum sync state response
        sync_interval     = "1m"  # How long to restart sync state process
        recovery          = "15s" # How long to wait before recovery finished(This is for release1.2)
        first_request     = "30s" # How long to wait before first request should come
        batch             = "0.5s"# Primary send a pre-prepare if there are pending requests, although batchsize isn't reached yet,
        request           = "6s"  # How long may a request(transaction batch) take between reception and execution, must be greater than the batch timeout
        null_request      = "9s"  # Primary send it to inform aliveness, must be greater than request timeout
        viewchange        = "8s"  # How long may a view change take
        resend_viewchange = "10s" # How long to wait for a view change quorum before resending (the same) view change
        clean_viewchange  = "60s" # How long to clean out-of-data view change message
        update            = "4s"  # How long may a update-n take
        set               = "0.1s" # Node broadcasts transactions if there are cached transactions, although set_size isn't reached yet

    [rbft.syncer]
        sync_blocks = 1 # How many blocks should the behind node fetch at once

[solo]
batch_timeout = "0.3s"  # Block packaging time period.

   [solo.mempool]
        batch_size          = 200   # How many transactions should the primary pack.
        pool_size           = 50000 # How many transactions could the txPool stores in total.
        tx_slice_size       = 10    # How many transactions should the node broadcast at once
        tx_slice_timeout    = "0.1s"  # Node broadcasts transactions if there are cached transactions, although set_size isn't reached yet
```

#### 3. 启动节点

修改配置后，使用如下命令启动BitXHub

```shell
# 终端启动各节点， repo为各节点配置文件所在路径
bitxhub --repo ./node1 start
bitxhub --repo ./node2 start
bitxhub --repo ./node3 start
bitxhub --repo ./node4 start
```

待节点集群打印出bitxhub的LOGO，表示BitXHub集群使用raft共识，并开始正常工作。

```shell
Order is ready                                module=app plugin_path=plugins/raft.so

=======================================================
    ____     _    __    _  __    __  __            __
   / __ )   (_)  / /_  | |/ /   / / / /  __  __   / /_
  / __  |  / /  / __/  |   /   / /_/ /  / / / /  / __ \
 / /_/ /  / /  / /_   /   |   / __  /  / /_/ /  / /_/ /
/_____/  /_/   \__/  /_/|_|  /_/ /_/   \__,_/  /_.___/

=======================================================
```



