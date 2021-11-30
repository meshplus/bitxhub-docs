
# bitxhub
## 功能

该命令`bitxhub`提供中继链节点的客户端操作命令。
子命令信息如下:
```
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 client

NAME:
   BitXHub - A leading inter-blockchain platform

USAGE:
   bitxhub [global options] command [command options] [arguments...]

COMMANDS:
   config   Operate bitxhub config
   init     Initialize BitXHub local configuration
   start    Start a long-running start process
   key      Create and show key information
   version  BitXHub version
   cert     Certification tools
   client   BitXHub client command
   help, h  Shows a list of commands or help for one command
            
```

## 参数
- `repo`: 指定bitxhub节点运行目录；
```
GLOBAL OPTIONS:
   --repo value   BitXHub storage repo path
   --help, -h     show help
   --version, -v  print the version
```
### bitxhub init
初始化节点配置：如未指定`repo`则默认使用`~/.bitxhub/`路径
```bigquery
bitxhub init   

initializing bitxhub at /Users/windiyi/.bitxhub
bitxhub configuration file already exists
reinitializing would overwrite your configuration, Y/N?
y

```
### bitxhub config
显示当前节点配置：如未指定`repo`则默认使用`~/.bitxhub/`路径，若路径下存在旧配置，则提示覆盖与否。

```bigquery
bitxhub --repo ./ config 

{
  "cert": {
    "agency_cert_path": "certs/agency.cert",
    "ca_cert_path": "certs/ca.cert",
    "node_cert_path": "certs/node.cert",
    "verify": true
  },
  "executor": {
    "type": "serial"
  },
  "gateway": {
    "AllowedOrigins": [
      "*"
    ]
  },
  "genesis": {
    "admins": [
      {
        "address": "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
        "weight": 2
      },
      {
        "address": "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
        "weight": 2
      },
      {
        "address": "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
        "weight": 2
      },
      {
        "address": "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8",
        "weight": 2
      }
    ],
    "strategy": {
      "appchainmgr": "SimpleMajority",
      "nodemgr": "SimpleMajority",
      "rulemgr": "SimpleMajority",
      "servicemgr": "SimpleMajority"
    }
  },
  "license": {
    "key": "133847740289a8dc5447bfbaedf10ed5ee9460110e4ba6b455e33cf941253527f18dd721515c5142fcea9b6415ddad6b3f1696011a4bbc86f35833ae9296a3ea",
    "verifier": "https://172.16.5.3:11443/v1/validation"
  },
  "limiter": {
    "capacity": 10000,
    "interval": 50000000,
    "quantum": 500
  },
  "log": {
    "dir": "logs",
    "filename": "bitxhub.log",
    "level": "info",
    "module": {
      "api": "info",
      "consensus": "info",
      "coreapi": "info",
      "executor": "info",
      "p2p": "info",
      "profile": "info",
      "router": "info",
      "storage": "info"
    },
    "report_caller": false
  },
  "monitor": {
    "Enable": true
  },
  "order": {
    "plugin": "plugins/raft.so"
  },
  "ping": {
    "duration": 15000000000,
    "enable": true
  },
  "port": {
    "gateway": 9091,
    "grpc": 60011,
    "monitor": 40011,
    "pprof": 53121
  },
  "pprof": {
    "duration": 30000000000,
    "enable": true,
    "mode": "memory",
    "ptype": "http"
  },
  "repo_root": "/Users/windiyi/.bitxhub",
  "security": {
    "EnableTLS": false,
    "pem_file_path": "certs/node.cert",
    "server_key_path": "certs/node.priv"
  },
  "solo": false,
  "title": "BitXHub configuration file",
  "txpool": {
    "batch_size": 500,
    "batch_timeout": 500000000
  }
}

```
