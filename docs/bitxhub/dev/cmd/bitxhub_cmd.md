
# bitxhub
中继链命令描述如下：

```shell
NAME:
   BitXHub - A leading inter-blockchain platform

USAGE:
   bitxhub [global options] command [command options] [arguments...]

VERSION:
   0.0.0

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

**参数解释**

- `repo`: 可选参数，指定bitxhub节点配置文件所在目录，如果不指定，默认使用$HOME/.bitxhub目录。
```shell
GLOBAL OPTIONS:
   --repo value   BitXHub storage repo path
   --help, -h     show help
   --version, -v  print the version
```
## 子命令描述

### 1. bitxhub config
**功能**
`bitxhub config`命令用于显示当前节点配置：如未指定`repo`则默认使用`~/.bitxhub/`路径。

**示例说明**

```shell
$ bitxhub config 

#控制台输出
{
  "appchain": {
    "EthHeaderPath": "",
    "enable": false
  },
  "cert": {
    "agency_cert_path": "certs/agency.cert",
    "ca_cert_path": "certs/ca.cert",
    "node_cert_path": "certs/node.cert",
    "verify": true
  },
  "crypto": {
    "algorithms": [
      "Secp256k1"
    ]
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
    "admins": null,
    "balance": "100000000000000000000000000000000000",
    "bvm_gas_price": 0,
    "chainid": 1,
    "gas_limit": 100000000,
    "strategy": null
  },
  "ledger": {
    "type": "complex"
  },
  "license": {
    "key": "",
    "verifier": ""
  },
  "limiter": {
    "capacity": 0,
    "interval": 0,
    "quantum": 0
  },
  "log": {
    "dir": "logs",
    "filename": "bitxhub.log",
    "level": "info",
    "module": {
      "api": "debug",
      "consensus": "debug",
      "coreapi": "debug",
      "executor": "debug",
      "p2p": "debug",
      "profile": "",
      "router": "debug",
      "storage": ""
    },
    "report_caller": false
  },
  "monitor": {
    "Enable": true
  },
  "order": {
    "type": "raft"
  },
  "ping": {
    "duration": 0,
    "enable": false
  },
  "port": {
    "gateway": 9091,
    "grpc": 60011,
    "jsonrpc": 0,
    "monitor": 40011,
    "pprof": 53121
  },
  "pprof": {
    "duration": 0,
    "enable": true,
    "mode": "",
    "ptype": ""
  },
  "repo_root": "/Users/zhupeiwen/.bitxhub",
  "security": {
    "EnableTLS": false,
    "gateway_cert_path": "",
    "gateway_key_path": "",
    "pem_file_path": "",
    "server_key_path": ""
  },
  "solo": false,
  "title": "BitXHub configuration file",
  "txpool": {
    "batch_size": 500,
    "batch_timeout": 500000000
  }
}

```



### 2. bitxhub init
**功能**
`bitxhub init`命令用于初始化节点配置：如未指定`repo`则默认使用`~/.bitxhub/`路径，若路径下存在旧配置，则提示覆盖与否。

**示例说明**

```shell
$ bitxhub init   

#控制台输出
initializing bitxhub at /Users/xxx/.bitxhub
bitxhub configuration file already exists
reinitializing would overwrite your configuration, Y/N?
y

```



### 3. bitxhub start
**功能**
`bitxhub start`命令用于启动BitXHub的节点。

```shell
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

**参数解释**

- `passwd`：指定节点私钥密码；
- `config`：指定节点配置路径，一般指`bitxhub.toml`；
- `network`：指定节点p2p网络配置文件，一般指`network.toml`；
- `order`：指定节点共识配置文件，一般指`order.toml`。

**若不指定以上配置，则默认至bitxhub --repo路径下寻找**。

**示例说明**

```shell
$ bitxhub --repo <node_config_path> start                         

# 控制台输出
BitXHub version: dev-release-1.18-9222137
App build date: 2022-01-13T11:02:55
System version: darwin/amd64
Golang version: go1.14.15

Supported crypto type:Secp256k1 ECDSA_P256 ECDSA_P384 ECDSA_P521

INFO[2022-01-13T11:04:02.951] MemPool batch size = 200                      module=order
INFO[2022-01-13T11:04:02.952] MemPool tx slice size = 200                   module=order
INFO[2022-01-13T11:04:02.952] MemPool batch seqNo = 3                       module=order
INFO[2022-01-13T11:04:02.952] MemPool pool size = 50000                     module=order
INFO[2022-01-13T11:04:02.953] SOLO lastExec = 3                             module=order
INFO[2022-01-13T11:04:02.953] SOLO batch timeout = 300ms                    module=order
INFO[2022-01-13T11:04:02.953] Start monitor                                 module=profile port=40011
INFO[2022-01-13T11:04:02.953] Start http pprof                              module=profile port=53121
INFO[2022-01-13T11:04:02.953] GRPC service started                          module=api port=60011
INFO[2022-01-13T11:04:02.954] Gateway service started                       module=api port=9091
INFO[2022-01-13T11:04:02.954] JSON-RPC service started                      module=api port=8881
INFO[2022-01-13T11:04:02.955] Ulimit raised                                 module=app ulimit=2048
```



### 4. bitxhub key
**功能**
`bitxhub key`命令提供针对bitxhub节点的私钥管理。它包含了私钥生成、私钥格式转换、私钥查看、地址查看。子命令信息如下:

```shell
NAME:
   BitXHub key - Create and show key information

USAGE:
   BitXHub key command [command options] [arguments...]

COMMANDS:
   gen      Create new private key in specified directory
   show     Show BitXHub key from repo
   address  Show address from Secp256k1 private key
```

**子命令**

#### bitxhub key gen
`bitxhub key gen`命令用于生成私钥文件`key.json`

```shell
NAME:
   BitXHub key gen - Create new private key in specified directory

USAGE:
   BitXHub key gen [command options] [arguments...]

OPTIONS:
   --target value  Specific target directory
   --passwd value  Specify password
   --algo value    crypto algorithm (default: "Secp256k1")
```

**参数解释**

- `target`：指定生成路径；
- `passwd`：指定私钥密码;
- `algo`: 指定私钥加密算法。

**示例说明**

```shell
$ bitxhub key gen --target ~/.bitxhub

# 控制台输出
key.json key is generated under directory /Users/xxx/.bitxhub
```

#### bitxhub key show
`bitxhub key show`命令用于显示`key.json`具体内容。

```shell
NAME:
   BitXHub key show - Show BitXHub key from repo

USAGE:
   BitXHub key show [command options] [arguments...]

OPTIONS:
   --path value    Specify private key path
   --passwd value  Specify password
```

**参数解释**

- `path`：指定私钥文件所在路径；
- `passwd`：指定私钥密码。

**示例说明**

```shell
bitxhub key show --path ~/.bitxhub/key.json     

#控制台输出
private key: aeb91e2d478950094b3490c0d916583c53993e6767b1cebd335c0e5b6021ef6d
public key: 0476832ada8f6cfef41ef226fb5ed1812a4d5f8dd66ad133dd9459ff014a2de108eb63617a731451398b258e92c19c44a4c78e645c35fd2921beab9e38b4aae0b2
address: 0x17f8e065ca9e743Cb23557d1eb4561Bd0eF52f64
```

#### bitxhub key address
`bitxhub key address`命令用于显示`key.json对应地址。

```shell
NAME:
   BitXHub key address - Show address from Secp256k1 private key

USAGE:
   BitXHub key address [command options] [arguments...]

OPTIONS:
   --path value    Specify private key path
   --passwd value  Specify password
```

**参数解释**

- `path`：指定私钥文件所在路径；
- `passwd`：指定私钥密码。

**示例说明**

```shell
bitxhub key address --path ./key.json --passwd 123

#控制台输出
0x3Db84D019fCd3A845e2fB3A21B56812Ffd933f19
```



### 5. bitxhub version
**功能**
`bitxhub version`命令用于显示BitXHub的版本信息。它会在命令行显示版本、构建日期、操作系统/架构和Go编译器版本。

```shell
NAME:
   bitxhub version - BitXHub version

USAGE:
   bitxhub version [arguments...]

```


**示例说明**

```shell
$ bitxhub version

#控制台输出
BitXHub version: dev-release-1.18-9222137
App build date: 2022-01-13T11:02:55
System version: darwin/amd64
Golang version: go1.14.15
```



### 6. bitxhub cert

**功能**
`bitxhub cert`命令提供一系列x509标准的证书工具，服务于中继链节点的准入机制。它包含了CA生成、CSR描述生成、证书签发、证书解析、证书私钥生成以及证书校验。说明信息如下:

```shell
NAME:
   BitXHub cert - Certification tools

USAGE:
   BitXHub cert command [command options] [arguments...]

COMMANDS:
   ca      Generate ca cert and private key
   csr     Generate csr file
   issue   Issue certification by ca
   parse   parse certification
   priv    Generate and show private key for certificate
   verify  verify cert
```


**子命令**

#### bitxhub cert ca
`bitxhub cert ca`命令用于生成根CA证书

```shell
NAME:
   BitXHub cert ca - Generate ca cert and private key

USAGE:
   BitXHub cert ca [arguments...]
```

**示例说明**

```shell
$ bitxhub cert ca

# 在当前目录下生成如下文件
├── ca.cert
├── ca.priv
```

#### bitxhub cert csr
`bitxhub cert csr`命令用于生成CSR描述信息。

```shell
NAME:
   BitXHub cert csr - Generate csr file

USAGE:
   BitXHub cert csr [command options] [arguments...]

OPTIONS:
   --key value     Specify Secp256r1 private key path
   --org value     Specify organization name
   --target value  Specific target directory
```

**参数解释**

- `key`: 指定私钥路径；
- `org`: 指定被签发人组织名称；
- `target`: 指定目标CSR路径。

**示例说明**

```shell
$ bitxhub cert csr --key <sub_private_key> --org testOrg --target .

#在指定目录生成下级csr文件
```


#### bitxhub cert issue
`bitxhub cert issue`命令用于证书签发。

```shell
NAME:
   BitXHub cert issue - Issue certification by ca

USAGE:
   BitXHub cert issue [command options] [arguments...]

OPTIONS:
   --csr value          Specify csr path
   --is_ca value        Specify whether it's ca
   --key value          Specify ca's secp256r1 private key path
   --cert value         Specify ca certification path
   --target value       Specific target directory
```

**参数解释**

- `csr`: 指定CSR描述文件路径；
- `is_ca`: 指定签发人是否为CA证书；
- `key`: 指定当前签发人私钥路径；
- `cert`: 指定当前签发人证书路径；
- `target`: 指定生成的证书路径。

**示例说明**

```shell
$ bitxhub cert issue --csr <sub_csr> --is_ca true --key ca.priv --cert ca.cert --target .

#在指定目录生成下级cert文件
```



#### bitxhub cert parse
`bitxhub cert parse`命令用于证书解析。

```shell
NAME:
   BitXHub cert parse - parse certification

USAGE:
   BitXHub cert parse [command options] [arguments...]

OPTIONS:
   --path value  certification path
```

**参数解释**

- `path`: 指定证书路径。

**示例说明**

```shell
$ bitxhub cert parse --path ca.cert

#控制台输出
{"Raw":"MIICkjCCAjegAwIBAgICIKUwCgYIKoZIzj0EAwIwgaExCzAJBgNVBAYTAkNOMREwDwYDVQQIEwhaaGVKaWFuZzERMA8GA1UEBxMISGFuZ1pob3UxHzANBgNVBAkTBnN0cmVldDAOBgNVBAkTB2FkZHJlc3MxDzANBgNVBBETBjMyNDAwMDETMBEGA1UEChMKSHlwZXJjaGFpbjEQMA4GA1UECxMHQml0WEh1YjETMBEGA1UEAxMKYml0eGh1Yi5jbjAgFw0yMjAxMTQwNTQzNTNaGA8yMDcyMDEwMjA1NDM1M1owgZsxCzAJBgNVBAYTAkNOMREwDwYDVQQIEwhaaGVKaWFuZzERMA8GA1UEBxMISGFuZ1pob3UxHzANBgNVBAkTBnN0cmVldDAOBgNVBAkTB2FkZHJlc3MxDzANBgNVBBETBjMyNDAwMDEQMA4GA1UEChMHdGVzdE9yZzEQMA4GA1UECxMHQml0WEh1YjEQMA4GA1UEAxMHQml0WEh1YjBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABLasY+fj/B1NnEThaYgWOsH8vya9js0hdZ4B9t6CXbLXIUvHWNIXTsjXleHAsi6EzCFmGTSGfqMIkGXZ4pYDxrGjYTBfMA4GA1UdDwEB/wQEAwIBpjAPBgNVHSUECDAGBgRVHSUAMA8GA1UdEwEB/wQFMAMBAf8wKwYDVR0jBCQwIoAgALrjuzAzMBMHVnRjgANMbl/Bl7jmmXmfIUksD+50s7kwCgYIKoZIzj0EAwIDSQAwRgIhAMMYaKjIiDHG7LTqiA2J3DmGUZDwb3VQnFx3ItXKpqozAiEAwBn1I2JmXMcBkCFFwNGxyrfCnmHuezumNNB2AuokKvU=","RawTBSCertificate":"MIICN6ADAgECAgIgpTAKBggqhkjOPQQDAjCBoTELMAkGA1UEBhMCQ04xETAPBgNVBAgTCFpoZUppYW5nMREwDwYDVQQHEwhIYW5nWmhvdTEfMA0GA1UECRMGc3RyZWV0MA4GA1UECRMHYWRkcmVzczEPMA0GA1UEERMGMzI0MDAwMRMwEQYDVQQKEwpIeXBlcmNoYWluMRAwDgYDVQQLEwdCaXRYSHViMRMwEQYDVQQDEwpiaXR4aHViLmNuMCAXDTIyMDExNDA1NDM1M1oYDzIwNzIwMTAyMDU0MzUzWjCBmzELMAkGA1UEBhMCQ04xETAPBgNVBAgTCFpoZUppYW5nMREwDwYDVQQHEwhIYW5nWmhvdTEfMA0GA1UECRMGc3RyZWV0MA4GA1UECRMHYWRkcmVzczEPMA0GA1UEERMGMzI0MDAwMRAwDgYDVQQKEwd0ZXN0T3JnMRAwDgYDVQQLEwdCaXRYSHViMRAwDgYDVQQDEwdCaXRYSHViMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEtqxj5+P8HU2cROFpiBY6wfy/Jr2OzSF1ngH23oJdstchS8dY0hdOyNeV4cCyLoTMIWYZNIZ+owiQZdnilgPGsaNhMF8wDgYDVR0PAQH/BAQDAgGmMA8GA1UdJQQIMAYGBFUdJQAwDwYDVR0TAQH/BAUwAwEB/zArBgNVHSMEJDAigCAAuuO7MDMwEwdWdGOAA0xuX8GXuOaZeZ8hSSwP7nSzuQ==","RawSubjectPublicKeyInfo":"MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEtqxj5+P8HU2cROFpiBY6wfy/Jr2OzSF1ngH23oJdstchS8dY0hdOyNeV4cCyLoTMIWYZNIZ+owiQZdnilgPGsQ==","RawSubject":"MIGbMQswCQYDVQQGEwJDTjERMA8GA1UECBMIWmhlSmlhbmcxETAPBgNVBAcTCEhhbmdaaG91MR8wDQYDVQQJEwZzdHJlZXQwDgYDVQQJEwdhZGRyZXNzMQ8wDQYDVQQREwYzMjQwMDAxEDAOBgNVBAoTB3Rlc3RPcmcxEDAOBgNVBAsTB0JpdFhIdWIxEDAOBgNVBAMTB0JpdFhIdWI=","RawIssuer":"MIGhMQswCQYDVQQGEwJDTjERMA8GA1UECBMIWmhlSmlhbmcxETAPBgNVBAcTCEhhbmdaaG91MR8wDQYDVQQJEwZzdHJlZXQwDgYDVQQJEwdhZGRyZXNzMQ8wDQYDVQQREwYzMjQwMDAxEzARBgNVBAoTCkh5cGVyY2hhaW4xEDAOBgNVBAsTB0JpdFhIdWIxEzARBgNVBAMTCmJpdHhodWIuY24=","Signature":"MEYCIQDDGGioyIgxxuy06ogNidw5hlGQ8G91UJxcdyLVyqaqMwIhAMAZ9SNiZlzHAZAhRcDRscq3wp5h7ns7pjTQdgLqJCr1","SignatureAlgorithm":10,"PublicKeyAlgorithm":3,"PublicKey":{"Curve":{"P":115792089210356248762697446949407573530086143415290314195533631308867097853951,"N":115792089210356248762697446949407573529996955224135760342422259061068512044369,"B":41058363725152142129326129780047268409114441015993725554835256314039467401291,"Gx":48439561293906451759052585252797914202762949526041747995844080717082404635286,"Gy":36134250956749795798585127919587881956611106672985015071877198253568414405109,"BitSize":256,"Name":"P-256"},"X":82625525661941981250491613743007096153774500696862681832425790330116014715607,"Y":15060213375229657175963991955251470872818906153418440931864025589557900854961},"Version":3,"SerialNumber":8357,"Issuer":{"Country":["CN"],"Organization":["Hyperchain"],"OrganizationalUnit":["BitXHub"],"Locality":["HangZhou"],"Province":["ZheJiang"],"StreetAddress":["street","address"],"PostalCode":["324000"],"SerialNumber":"","CommonName":"bitxhub.cn","Names":[{"Type":[2,5,4,6],"Value":"CN"},{"Type":[2,5,4,8],"Value":"ZheJiang"},{"Type":[2,5,4,7],"Value":"HangZhou"},{"Type":[2,5,4,9],"Value":"street"},{"Type":[2,5,4,9],"Value":"address"},{"Type":[2,5,4,17],"Value":"324000"},{"Type":[2,5,4,10],"Value":"Hyperchain"},{"Type":[2,5,4,11],"Value":"BitXHub"},{"Type":[2,5,4,3],"Value":"bitxhub.cn"}],"ExtraNames":null},"Subject":{"Country":["CN"],"Organization":["testOrg"],"OrganizationalUnit":["BitXHub"],"Locality":["HangZhou"],"Province":["ZheJiang"],"StreetAddress":["street","address"],"PostalCode":["324000"],"SerialNumber":"","CommonName":"BitXHub","Names":[{"Type":[2,5,4,6],"Value":"CN"},{"Type":[2,5,4,8],"Value":"ZheJiang"},{"Type":[2,5,4,7],"Value":"HangZhou"},{"Type":[2,5,4,9],"Value":"street"},{"Type":[2,5,4,9],"Value":"address"},{"Type":[2,5,4,17],"Value":"324000"},{"Type":[2,5,4,10],"Value":"testOrg"},{"Type":[2,5,4,11],"Value":"BitXHub"},{"Type":[2,5,4,3],"Value":"BitXHub"}],"ExtraNames":null},"NotBefore":"2022-01-14T05:43:53Z","NotAfter":"2072-01-02T05:43:53Z","KeyUsage":101,"Extensions":[{"Id":[2,5,29,15],"Critical":true,"Value":"AwIBpg=="},{"Id":[2,5,29,37],"Critical":false,"Value":"MAYGBFUdJQA="},{"Id":[2,5,29,19],"Critical":true,"Value":"MAMBAf8="},{"Id":[2,5,29,35],"Critical":false,"Value":"MCKAIAC647swMzATB1Z0Y4ADTG5fwZe45pl5nyFJLA/udLO5"}],"ExtraExtensions":null,"UnhandledCriticalExtensions":null,"ExtKeyUsage":[0],"UnknownExtKeyUsage":null,"BasicConstraintsValid":true,"IsCA":true,"MaxPathLen":-1,"MaxPathLenZero":false,"SubjectKeyId":null,"AuthorityKeyId":"ALrjuzAzMBMHVnRjgANMbl/Bl7jmmXmfIUksD+50s7k=","OCSPServer":null,"IssuingCertificateURL":null,"DNSNames":null,"EmailAddresses":null,"IPAddresses":null,"URIs":null,"PermittedDNSDomainsCritical":false,"PermittedDNSDomains":null,"ExcludedDNSDomains":null,"PermittedIPRanges":null,"ExcludedIPRanges":null,"PermittedEmailAddresses":null,"ExcludedEmailAddresses":null,"PermittedURIDomains":null,"ExcludedURIDomains":null,"CRLDistributionPoints":null,"PolicyIdentifiers":null}
```


#### bitxhub cert priv
`bitxhub cert priv`命令用于生成或展示用于p2p网络通信的私钥。

```shell
NAME:
   BitXHub cert priv - Generate and show private key for certificate

USAGE:
   BitXHub cert priv command [command options] [arguments...]

COMMANDS:
   gen  Create new private key
   pid  Show pid from private key
```

**子命令**

##### bitxhub cert priv gen
`bitxhub cert priv gen`命令用于生成p2p网络通信私钥

```shell
NAME:
   BitXHub cert priv gen - Create new private key

USAGE:
   BitXHub cert priv gen [command options] [arguments...]

OPTIONS:
   --name value    Specific private key name
   --target value  Specific target directory
```

**示例说明**

```shell
bitxhub cert priv gen --name server --target ./

#控制台输出
server.priv key is generated under directory /Users/xxx/GolandProjectsTest/bitxhub-docs
```

##### bitxhub cert priv pid
`bitxhub cert priv pid`命令用于获取libp2p节点pid。

```shell
NAME:
   BitXHub cert priv pid - Show pid from private key

USAGE:
   BitXHub cert priv pid [command options] [arguments...]

OPTIONS:
   --path value  Specific private key path
```

**示例说明**

```shell
bitxhub cert priv pid --path ./server.priv   
  
# 控制台输出
QmWw4hPtm51jpxzKjyYMe3E7bNei9wdoug6B1rZqSwVSL4
```

#### bitxhub cert verify
`bitxhub cert verify`用于验证证书

```shell
NAME:
   BitXHub cert verify - verify cert

USAGE:
   BitXHub cert verify [command options] [arguments...]

OPTIONS:
   --sub value  sub cert path
   --ca value   ca cert path
```

**参数解释**

- `sub`: 下级证书路径；
- `ca`: ca证书路径。

**示例说明**

```shell
$ bitxhub cert verify --sub <sub_cert> --ca ca.cert

#若验证通过，则控制台无输出，若验证失败，则会有报错信息
```


### 7. bitxhub client
**功能**

该命令`bitxhub client`提供中继链节点的客户端操作命令。它包含了账户信息查询、中继链信息、区块查询、节点网络信息、回执查询、交易操作、验证者集查询、删除共识节点、提案治理。
子命令信息如下:

```shell
NAME:
   BitXHub client - BitXHub client command

USAGE:
   BitXHub client command [command options] [arguments...]

COMMANDS:
   account     Query account information
   chain       Query bitxhub chain info
   block       Query block
   network     Query network info from node
   receipt     Query receipt
   tx          Transaction manipulation
   validators  Query validator address
   governance  governance command                      
```

**参数解释**

- `gateway`: 指定客户端http服务地址，如果节点开启了TLS功能，则需要传递https地址；
- `ca`: 指定CA证书，如果节点开启了TLS功能，需要传递；
- `cert`: 指定节点的证书，如果节点开启了TLS功能，需要传递；
- `key`: 指定节点的私钥，如果节点开启了TLS功能，需要传递；

```shell
OPTIONS:
   --gateway value  Specific gateway address (default: "http://localhost:9091/v1/")
   --ca value       Specific ca cert file if https is enabled
   --cert value     Specific access cert file if https is enabled
   --key value      Specific access key file if https is enabled
   --help, -h       show help
```
**子命令**

#### bitxhub client account
`bitxhub client account`命令用于账户信息查询。

```shell
NAME:
   BitXHub client account - Query account information

USAGE:
   BitXHub client account [arguments...]
```

**示例说明**

```shell
$ bitxhub client account 0x79a1215469FaB6f9c63c1816b45183AD3624bE34                                         

# 控制台输出
{
  "type": "normal",
  "balance": 100000000000000000000000000000000000,
  "contract_count": 0,
  "code_hash": "0xc5d2460186f7233C927e7db2dCc703c0E500B653cA82273b7bFaD8045d85A470"
}
```


#### bitxhub client chain
`bitxhub client chain`命令用于中继链信息查询。

```shell
NAME:
   BitXHub client chain - Query bitxhub chain info

USAGE:
   BitXHub client chain command [command options] [arguments...]

COMMANDS:
   meta    Query bitxhub chain meta
   status  Query bitxhub chain status
```

**子命令**
##### bitxhub client chain meta
`bitxhub client chain meta`用于查询中继链信息。

```shell
NAME:
   BitXHub client chain meta - Query bitxhub chain meta

USAGE:
   BitXHub client chain meta [arguments...]
```

**示例说明**

```shell
$ bitxhub client chain meta
# 控制台输出
{"height":"1","block_hash":"0x47dBBdcE25e2ab4BbA8ee325Db8502Aa6c2A065Af1350450F0917d5e139465A1"}
```

##### bitxhub client chain status
`bitxhub client chain status`命令用于查询中继链状态是否可用。

```shell
NAME:
   BitXHub client chain status - Query bitxhub chain status

USAGE:
   BitXHub client chain status [arguments...]
```

**示例说明**

```shell
# normal: 已处在共识状态
# abnormal：未共识状态，集群没有正常运行
$ bitxhub client chain status

# 控制台输出
normal
```

#### bitxhub client block
`bitxhub client block`命令用于区块信息查询，**可指定区块号或区块Hash**。

```shell
NAME:
   BitXHub client block - Query block

USAGE:
   BitXHub client block [arguments...]
```

**示例说明**

```shell
$ bitxhub client block 1

# 控制台输出
{"block_header":{"number":"1","state_root":"0x5B8A0f51c912c38501C263DEC58542453F1a6B399a491C27363dF1D9c3029328","tx_root":"0x0000000000000000000000000000000000000000000000000000000000000000","receipt_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x0000000000000000000000000000000000000000000000000000000000000000","timestamp":"1642052145652059000","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"transactions":{},"block_hash":"0x47dBBdcE25e2ab4BbA8ee325Db8502Aa6c2A065Af1350450F0917d5e139465A1","signature":"7B4yjNFnnQO+mw2poSnrsGkXMxMkDcGyzXlPioahKo1MCirTQ1hc+imoIr6d/59ZD//62N5asPQjEwVLpOBuowE="}
```

#### bitxhub client network
`bitxhub client network`命令用于查询全网节点信息。

```shell
NAME:
   BitXHub client network - Query network info from node

USAGE:
   BitXHub client network [arguments...]
```

**示例说明**

```shell
$ bitxhub client network

# 控制台输出
{
  "1": {
    "id": 1,
    "pid": "QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL",
    "account": "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4001/p2p/"
    ]
  },
  "2": {
    "id": 2,
    "pid": "QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4",
    "account": "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4002/p2p/"
    ]
  },
  "3": {
    "id": 3,
    "pid": "QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS",
    "account": "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4003/p2p/"
    ]
  },
  "4": {
    "id": 4,
    "pid": "QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy",
    "account": "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8",
    "hosts": [
      "/ip4/127.0.0.1/tcp/4004/p2p/"
    ]
  }
}
```

#### bitxhub client receipt
`bitxhub client receipt`命令用于交易回执查询。

```shell
NAME:
   BitXHub client receipt - Query receipt

USAGE:
   BitXHub client receipt [arguments...]
```

**示例说明**

```shell
$ bitxhub client receipt 0xF85689DF09EA521307981301E60dAf75C2E73407BDab8E5D5a968Ff3b65F2e28

# 控制台输出
{"tx_hash":"0xF85689DF09EA521307981301E60dAf75C2E73407BDab8E5D5a968Ff3b65F2e28","gas_used":"21000","bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"}
```

#### bitxhub client tx
`bitxhub client tx`命令用于交易发送与交易查询。

```shell
NAME:
   BitXHub client tx - Transaction manipulation

USAGE:
   BitXHub client tx command [command options] [arguments...]

COMMANDS:
   get   Query transaction
   send  Send transaction
```

**子命令**

##### bitxhub client tx send
`bitxhub client tx send`命令用于交易发送

```shell
NAME:
   BitXHub client tx send - Send transaction

USAGE:
   BitXHub client tx send [command options] [arguments...]

OPTIONS:
   --key value     Private key path
   --to value      Target address
   --amount value  Transfer amount
   --type value    Transaction type (default: 0)
```

**示例说明**

```shell
$ bitxhub client tx send --key scripts/build/node1/key.json --to 0x97c8B516D19edBf575D72a172Af7F418BE498C37 --amount 2 --type 0

# 控制台输出
{"tx_hash":"0x96efaE88d1a964a8e3E6e1F5ce76ad9067858799aE58400254bdD7A53feA455c"}
```
##### bitxhub client tx get
`bitxhub client tx get`命令用于交易查询

```shell
NAME:
   BitXHub client tx get - Query transaction

USAGE:
   BitXHub client tx get [arguments...]
```

**示例说明**

```shell
$ bitxhub client tx get 0x96efaE88d1a964a8e3E6e1F5ce76ad9067858799aE58400254bdD7A53feA455c

# 控制台输出
{"tx":{"from":"0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013","to":"0x97c8B516D19edBf575D72a172Af7F418BE498C37","timestamp":"1642053116691193000","transaction_hash":"0x96efaE88d1a964a8e3E6e1F5ce76ad9067858799aE58400254bdD7A53feA455c","payload":"EgEy","nonce":"1","signature":"A2HcA78zg4HZwAPThRLJCL45Y5favAx1DPzaXwlUGREiDNSSd6vSAjtF0qIDQTJs3Pk58tHniegNWQ/Ai5J+ZyMB"},"tx_meta":{"block_hash":"MOxvkudqJKDvlxCIWwLnekAZ78WTPnxSK5Q9z1tT4ig=","block_height":"3"}}
```

#### bitxhub client validators
`bitxhub client validators`命令用于查询全网验证人信息。

```shell
NAME:
   BitXHub client validators - Query validator address

USAGE:
   BitXHub client validators [arguments...]
```

**示例说明**

```shell
$ bitxhub client validators

# 控制台输出
[
  "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
  "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
  "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
  "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"
]
```

#### bitxhub client governance
`bitxhub client governance`命令用于提案治理。
```shell
NAME:
   BitXHub client governance - governance command

USAGE:
   BitXHub client governance command [command options] [arguments...]

COMMANDS:
   vote      vote to a proposal
   proposal  proposal manage command
   chain     appchain manage command
   rule      rule manage command
   node      node manage command
   role      role manage command
   dapp      dapp manage command
   service   service manage command
   strategy  proposal strategy command
```

**子命令**

##### bitxhub client governance vote
`bitxhub client governance vote`命令用于提案投票。

```shell
NAME:
   BitXHub client governance vote - vote to a proposal

USAGE:
   BitXHub client governance vote [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --info value    voting information, approve or reject
   --reason value  reason to vote
```

**参数解释**

- `id`：必选参数。提案id，生成提案时将在控制台打印。
- `info`：必选参数。是否通过提案。approve为通过，reject为拒绝。
- `reason`：必选参数。投票理由说明。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance vote --id 0x79b2c9802ea404AFF4a32a70C288b84465016DE3-0 --info approve --reason test

# 控制台输出
vote successfully! 
```

##### bitxhub client governance proposal
`bitxhub client governance proposals`命令用于提案查询。查询中继链提案的内容以及相关的状态。命令说明如下：

```shell
NAME:
   BitXHub client governance proposal - proposal manage command

USAGE:
   BitXHub client governance proposal command [command options] [arguments...]

COMMANDS:
   query     query proposals based on the condition
   withdraw  withdraw a proposal
```

**子命令**
###### bitxhub client governance proposal query
`bitxhub client governance proposal query`命令用于提案查询。查询中继链提案的内容以及相关的状态。命令说明如下：

```shell
NAME:
   BitXHub client governance proposal query - query proposals based on the condition

USAGE:
   BitXHub client governance proposal query [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --type value    proposal type, currently only AppchainMgr, RuleMgr, NodeMgr, RoleMgr are supported
   --status value  proposal status, one of proposed, paused, approve or reject
   --from value    the address of the account to which the proposal was made
   --objId value   the ID of the managed object
```

**参数解释**

- `id`: 提案id；
- `type`: 提案类型，当前支持应用链管理、规则管理、节点管理、角色管理；
- `status`: 提案状态，是否通过；
- `from`: 提案发起者地址；
- `objId`: 提案管理对象id，如应用链id。

**示例说明**

```shell
$ bitxhub client governance proposal query --id 0x79b2c9802ea404AFF4a32a70C288b84465016DE3-0 --type appchain_mgr --status proposed --from 0x79b2c9802ea404AFF4a32a70C288b84465016DE3 --objId chain1

# 控制台输出
========================================================================================
Id                                            ManagedObjectId  Type          EventType  Status    A/R  IE/AE/TE  Special/Super  CreateTime           Reason  EndReason  extra
--                                            ---------------  ----          ---------  ------    ---  --------  -------------  ----------           ------  ---------  -----
0x79b2c9802ea404AFF4a32a70C288b84465016DE3-0  chain1           appchain_mgr  register   proposed  1/0  4/4/3     false/true     1642054244671602000  reason             {"chain_info":{"id":"chain1","chain_name":"cita1","chain_type":"cita","trust_root":null,"broker":"YnJva2Vy","desc":"desc","version":0,"status":"available","fsm":null},"master_rule":{"address":"0x00000000000000000000000000000000000000a2","rule_url":"url","chain_id":"","master":true,"builtIn":false,"create_time":0,"status":"available","fsm":null},"admin_addrs":"0x79b2c9802ea404AFF4a32a70C288b84465016DE3"}
========================================================================================
* A/R：approve num / reject num
* IE/AE/TE：the total number of electorate at the time of the initial proposal / the number of available electorate currently /the minimum threshold for votes to take effect
* Special/Super：is special proposal / is super admin voted
```

###### bitxhub client governance proposal withdraw
`bitxhub client governance proposal withdraw`命令用于提案撤回

```shell
NAME:
   BitXHub client governance proposal withdraw - withdraw a proposal

USAGE:
   BitXHub client governance proposal withdraw [command options] [arguments...]

OPTIONS:
   --id value      proposal id
   --reason value  withdraw reason
```

**参数解释**

- `id`: 提案id；
- `reason`: 提案撤回理由。

**示例说明**

```shell
$ bitxhub --repo <proposal_submit_private_key> client governance proposal withdraw --id 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2 --reason test

# 控制台输出
withdraw proposal successfully!
```

##### bitxhub client governance chain
`bitxhub client governance chain`命令用于应用链管理。命令说明如下：

```shell
NAME:
   BitXHub client governance chain - appchain manage command

USAGE:
   BitXHub client governance chain command [command options] [arguments...]

COMMANDS:
   info      query chain info by chain name
   status    query chain status by chain id
   freeze    freeze appchain by chain id
   activate  activate chain by chain id

```

**子命令**
###### bitxhub client governance chain info
`bitxhub client governance chain info`命令用于查询应用链信息

```shell
NAME:
   BitXHub client governance chain info - query chain info by chain name

USAGE:
   BitXHub client governance chain info [command options] [arguments...]

OPTIONS:
   --name value  Specify chain name
```

**参数解释**

- `name`: 应用链名称。

**示例说明**

```shell
$ bitxhub client governance chain info --name cita

#控制台输出
Id      Name  Type  Broker  Status     Desc  Version
--      ----  ----  ------  ------     ----  -------
chain0  cita  cita  broker  available  desc  0
```

###### bitxhub client governance chain status
`bitxhub client governance chain status`命令用于查询应用链状态

```shell
NAME:
   BitXHub client governance chain status - query chain status by chain id

USAGE:
   BitXHub client governance chain status [command options] [arguments...]

OPTIONS:
   --id value  Specify chain id
```

**参数解释**

- `id`: 应用链id。

**示例说明**

```shell
$ bitxhub client governance chain status --id chain0

#控制台输出
appchain chain0 is available
```

###### bitxhub client governance chain freeze
`bitxhub client governance chain freeze`命令用于冻结应用链

```shell
NAME:
   BitXHub client governance chain freeze - freeze appchain by chain id

USAGE:
   BitXHub client governance chain freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify chain id
   --reason value  Specify freeze reason
```

**参数解释**

- `id`: 应用链id；
- `reason`: 冻结应用链理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance chain freeze --id chain0 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
#管理员对上述提案治理投票通过后，应用链状态由freezing转换为frozen
```

###### bitxhub client governance chain activate
`bitxhub client governance chain activate`命令用于激活应用链

```shell
NAME:
   BitXHub client governance chain activate - activate chain by chain id

USAGE:
   BitXHub client governance chain activate [command options] [arguments...]

OPTIONS:
   --id value      Specify chain id
   --reason value  Specify activate reason
```

**参数解释**
- `id`: 应用链id；
- `reason`: 激活应用链理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance chain activate --id chain0 reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
#管理员对上述提案治理投票通过后，应用链状态由frozen转换为available
```

##### bitxhub client governance rule
`bitxhub client governance rule`命令用于验证规则管理。命令说明如下：

```shell
NAME:
   BitXHub client governance rule - rule manage command

USAGE:
   BitXHub client governance rule command [command options] [arguments...]

COMMANDS:
   all     query all rules info of one chain
   master  query master rule address of a chain
   status  query rule status by rule address and chain id

OPTIONS:
   --help, -h  show help
```

**子命令**
####### bitxhub client governance rule all
`bitxhub client governance rule all`命令用于查询某条应用链所有的验证规则

```shell
NAME:
   BitXHub client governance rule all - query all rules info of one chain

USAGE:
   BitXHub client governance rule all [command options] [arguments...]

OPTIONS:
   --id value  Specify chain id
```

**参数解释**

- `id`: 应用链id。

**示例说明**

```shell
$ bitxhub client governance rule all --id chain0

#控制台输出
ChainID  RuleAddress                                 Status     Master  CreateTime
-------  -----------                                 ------     ------  ----------
chain0   0x00000000000000000000000000000000000000a2  available  true    1642053991339695000
```

####### bitxhub client governance rule master
`bitxhub client governance rule master`命令用于查询某条应用链的主验证规则

```shell
NAME:
   BitXHub client governance rule master - query master rule address of a chain

USAGE:
   BitXHub client governance rule master [command options] [arguments...]

OPTIONS:
   --id value  Specify chain id
```

**参数解释**

- `id`: 应用链id。

**示例说明**

```shell
$ bitxhub client governance rule master --id chain0

#控制台输出
available rule address is 0x00000000000000000000000000000000000000a2
```

###### bitxhub client governance rule status
`bitxhub client governance rule status`命令用于查询某条应用链的某条验证规则的状态

```shell
NAME:
   BitXHub client governance rule status - query rule status by rule address and chain id

USAGE:
   BitXHub client governance rule status [command options] [arguments...]

OPTIONS:
   --id value    Specify chain id
   --addr value  Specify rule addr
```

**参数解释**

- `id`: 应用链id；
- `addr`: 验证规则地址。

**示例说明**

```shell
$ bitxhub client governance rule status --id chain0 --addr 0x00000000000000000000000000000000000000a2

#控制台输出
the rule 0x00000000000000000000000000000000000000a2 is available
```

##### bitxhub client governance node
`bitxhub client governance node`命令用于节点管理。命令说明如下：

```shell
NAME:
   BitXHub client governance node - node manage command

USAGE:
   BitXHub client governance node command [command options] [arguments...]

COMMANDS:
   status    query node status by node account
   register  register node
   update    update node
   logout    logout node by node account
   all       query all nodes info
```

**子命令**

###### bitxhub client governance node status
`bitxhub client governance node status`命令用于查询节点状态

```shell
NAME:
   BitXHub client governance node status - query node status by node account

USAGE:
   BitXHub client governance node status [command options] [arguments...]

OPTIONS:
   --account value  Specify node account
```

**参数解释**

- `account`: 节点账户地址。

**示例说明**

```shell
$ bitxhub client governance node status --account 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013

#控制台输出
node QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL is available
```

###### bitxhub client governance node register
`bitxhub client governance node register`命令用于注册节点

```shell
NAME:
   BitXHub client governance node register - register node

USAGE:
   BitXHub client governance node register [command options] [arguments...]

OPTIONS:
   --account value     Specify node account
   --type value        Specify node type (vpNode or nvpNode) (default: "vpNode")
   --pid value         Specify vp node pid, only useful for VPnode
   --id value          Specify vp node id, only useful for VPnode (default: 0)
   --name value        Specify nvp node name, only useful for NVPnode
   --permission value  Specify nvp node permission, only useful for NVPnode, multiple appchain addresses are separated by commas
   --reason value      Specify register reason
```

**参数解释**

- `account`: 节点账户地址；
- `type`: 节点类型，共识节点或者审计节点；
- `pid`: 共识节点pid；
- `id`: 共识节点id；
- `name`: 审计节点名称；
- `permission`: 审计节点权限；
- `reason`: 注册节点理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance node register --account 0x10FEC285E6a6930d313c05c7befFAfc8Bf96dff6 --type nvpNode --name audit0 --permission chain0 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
```

###### bitxhub client governance node update
`bitxhub client governance node update`命令用于更新审计节点相关信息

```shell
NAME:
   BitXHub client governance node update - update node

USAGE:
   BitXHub client governance node update [command options] [arguments...]

OPTIONS:
   --account value     Specify node account
   --name value        Specify nvp node name, only useful for NVPnode
   --permission value  Specify nvp node permission, only useful for NVPnode, multiple appchain addresses are separated by commas
   --reason value      Specify register reason
```

**参数解释**

- `account`: 节点账户地址；
- `name`: 审计节点名称；
- `permission`：更新后审计节点权限；
- `reason`: 更新节点信息理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance node update --account 0x10FEC285E6a6930d313c05c7befFAfc8Bf96dff6 --name audit111 --permission chain0 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-4
```

###### bitxhub client governance node logout
`bitxhub client governance node logout`命令用于注销节点

```shell
NAME:
   BitXHub client governance node logout - logout node by node account

USAGE:
   BitXHub client governance node logout [command options] [arguments...]

OPTIONS:
   --account value  Specify node account
   --reason value   Specify logout reason
```

**参数解释**

- `account`: 节点账户地址；
- `reason`: 注销节点理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance node logout --account 0x10FEC285E6a6930d313c05c7befFAfc8Bf96dff6 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-5
```

###### bitxhub client governance node all
`bitxhub client governance node all`命令用于查询所有节点信息

```shell
NAME:
   BitXHub client governance node all - query all nodes info

USAGE:
   BitXHub client governance node all [arguments...]
```

**示例说明**

```shell
$  bitxhub client governance node all

#控制台输出
Account                                     Type     Pid                                             VpNodeId  Name      Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----      ----------  ------     --------------
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                               available
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                               available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                               available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                               available
0x10FEC285E6a6930d313c05c7befFAfc8Bf96dff6  nvpNode                                                  0         audit111  chain0      forbidden
```

##### bitxhub client governance role
`bitxhub client governance role`命令用于角色管理。命令说明如下：

```shell
NAME:
   BitXHub client governance role - role manage command

USAGE:
   BitXHub client governance role command [command options] [arguments...]

COMMANDS:
   status    query role status by role id
   register  register role
   freeze    freeze role by role id
   activate  activate role by role id
   logout    logout role by role id
   bind      bind audit role with node
   all       query all roles info
```

**子命令**

###### bitxhub client governance role status
`bitxhub client governance role status`命令用于查询角色状态

```shell
NAME:
   BitXHub client governance role status - query role status by role id

USAGE:
   BitXHub client governance role status [command options] [arguments...]

OPTIONS:
   --id value  Specify role id(address)
```

**参数解释**

- `id`: 角色id。

**示例说明**

```shell
$ bitxhub client governance role status --id 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8

#控制台输出
role 0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8 is available
```

###### bitxhub client governance role register
`bitxhub client governance role register`命令用于注册角色

```shell
NAME:
   BitXHub client governance role register - register role

USAGE:
   BitXHub client governance role register [command options] [arguments...]

OPTIONS:
   --address value      Specify role address(id)
   --type value         Specify role type, one of governanceAdmin or auditAdmin (default: "governanceAdmin")
   --nodeAccount value  Specify node account for auditAdmin, only useful for auditAdmin
   --reason value       Specify register reason
```

**参数解释**

- `address`: 角色地址；
- `type`: 角色类型，治理管理员或者审计管理员；
- `nodeAccount`: 审计管理员对应审计节点地址；
- `reason`: 注册角色理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance role register --address 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --type governanceAdmin --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
```

###### bitxhub client governance role freeze
`bitxhub client governance role freeze`命令用于冻结角色

```shell
NAME:
   BitXHub client governance role freeze - freeze role by role id

USAGE:
   BitXHub client governance role freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify role id
   --reason value  Specify freeze reason
```

**参数解释**

- `id`: 角色id；
- `reason`: 冻结角色理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance role freeze --id 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
```

###### bitxhub client governance role activate
`bitxhub client governance role activate`命令用于激活角色

```shell
NAME:
   BitXHub client governance role activate - activate role by role id

USAGE:
   BitXHub client governance role activate [command options] [arguments...]

OPTIONS:
   --id value      Specify role id
   --reason value  Specify activate reason
```

**参数解释**

- `id`: 角色id；
- `reason`: 激活角色理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance role activate --id 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
```

###### bitxhub client governance role logout
`bitxhub client governance role logout`命令用于注销角色

```shell
NAME:
   BitXHub client governance role logout - logout role by role id

USAGE:
   BitXHub client governance role logout [command options] [arguments...]

OPTIONS:
   --id value      Specify role pid
   --reason value  Specify logout reason
```

**参数解释**

- `id`: 角色id；
- `reason`: 注销角色理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance role logout --id 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
```

###### bitxhub client governance role bind
`bitxhub client governance role bind`命令用于审计管理员与审计节点绑定，当审计节点注销后，与之绑定的审计管理员会变为冻结状态，审计管理员需要重新与审计节点绑定才能恢复正常状态。

```shell
NAME:
   BitXHub client governance role bind - bind audit role with node

USAGE:
   BitXHub client governance role bind [command options] [arguments...]

OPTIONS:
   --id value       Specify role id
   --account value  Specify node account
   --reason value   Specify freeze reason
```

**参数解释**

- `id`: 角色id；
- `account`: 审计节点地址；
- `reason`: 审计管理员绑定审计节点理由。

**示例说明**

```shell
$ bitxhub --repo scripts/build/node1 client governance role bind --id 0x25418CfDdc3453B394b2c000e14141dA80606953 --account 0x29366700f2E3963646D61Cd66d0d38Fd4Db1e664 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-8
```

###### bitxhub client governance role all
`bitxhub client governance role all`命令用于查询所有角色信息

```shell
NAME:
   BitXHub client governance role all - query all roles info

USAGE:
   BitXHub client governance role all [command options] [arguments...]

OPTIONS:
   --type value  Specify role type (default: "governanceAdmin")
```

**参数解释**

- `type`: 角色类型。

**示例说明**

```shell
$ bitxhub client governance role all

#控制台输出
RoleId                                      type                  Status     NodeAccount                                 AppchainID
------                                      ----                  ------     -----------                                 ----------
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0xbC2Ff66b81B936b255C1e028Ec9B1982a6c5Fa0e  appchainAdmin         available                                              chain0
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x25418CfDdc3453B394b2c000e14141dA80606953  auditAdmin            available  0x29366700f2E3963646D61Cd66d0d38Fd4Db1e664
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
0xb1714Cda9364a9b70a22fe664ba398314b173ae7  governanceAdmin       forbidden
```

##### bitxhub client governance dapp
`bitxhub client governance dapp`命令用于Dapp管理。命令说明如下：

```shell
NAME:
   BitXHub client governance dapp - dapp manage command

USAGE:
   BitXHub client governance dapp command [command options] [arguments...]

COMMANDS:
   all         query all dapps
   permission  query permission dapps
   status      query dapp status by dapp id
   myDapps     query dapps by owner addr
   register    register dapp
   update      update dapp
   freeze      freeze dapp by dapp id
   activate    activate dapp by dapp id
   transfer    transfer dapp to other user
   confirm     confirm dapp transfer
   evaluate    evaluate dapp
```

**子命令**

###### bitxhub client governance dapp all
`bitxhub client governance dapp all`命令用于查询所有dapp

```shell
NAME:
   BitXHub client governance dapp all - query all dapps

USAGE:
   BitXHub client governance dapp all [arguments...]
```

**示例说明**

```shell
$ bitxhub client governance dapp all

#控制台输出
========================================================================================
Id                                            Name          Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----          ----  -----                                       ----------           -----  ------     -------  ------
0x66A442010FD68A57bb2e835A869b23C3220981F0-0  dapp_example  tool  0x66A442010FD68A57bb2e835A869b23C3220981F0  1642124785975579000  0      available
```

###### bitxhub client governance dapp permission
`bitxhub client governance dapp permission`命令用于查询dapp权限列表，返回某地址所有能够使用的dapp信息

```shell
NAME:
   BitXHub client governance dapp permission - query permission dapps

USAGE:
   BitXHub client governance dapp permission [command options] [arguments...]

OPTIONS:
   --caller value  Specify caller addr
```

**参数解释**

- `caller`: 调用者地址。

**示例说明**

```shell
$ bitxhub client governance dapp permission --caller 0x66A442010FD68A57bb2e835A869b23C3220981F0

#控制台输出
========================================================================================
Id                                            Name          Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----          ----  -----                                       ----------           -----  ------     -------  ------
0x66A442010FD68A57bb2e835A869b23C3220981F0-0  dapp_example  tool  0x66A442010FD68A57bb2e835A869b23C3220981F0  1642124785975579000  0      availabl
```

###### bitxhub client governance dapp status
`bitxhub client governance dapp status`命令用于查询某个dapp状态

```shell
NAME:
   BitXHub client governance dapp status - query dapp status by dapp id

USAGE:
   BitXHub client governance dapp status [command options] [arguments...]

OPTIONS:
   --id value  Specify dapp id
```

**参数解释**

- `id`: Dapp id。

**示例说明**

```shell
$ bitxhub client governance dapp status --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0

#控制台输出
dapp 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 is available
```

###### bitxhub client governance dapp myDapps
`bitxhub client governance dapp myDapps`命令用于查询某个用户的所有dapp

```shell
NAME:
   BitXHub client governance dapp myDapps - query dapps by owner addr

USAGE:
   BitXHub client governance dapp myDapps [command options] [arguments...]

OPTIONS:
   --addr value  Specify user addr
```

**参数解释**

- `addr`: 用户地址

**示例说明**

```shell
$ bitxhub client governance dapp myDapps --addr 0x66A442010FD68A57bb2e835A869b23C3220981F0

#控制台输出
========================================================================================
Id                                            Name          Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----          ----  -----                                       ----------           -----  ------     -------  ------
0x66A442010FD68A57bb2e835A869b23C3220981F0-0  dapp_example  tool  0x66A442010FD68A57bb2e835A869b23C3220981F0  1642124785975579000  0      available
```

###### bitxhub client governance dapp register
`bitxhub client governance dapp register`命令用于注册dapp

```shell
NAME:
   BitXHub client governance dapp register - register dapp

USAGE:
   BitXHub client governance dapp register [command options] [arguments...]

OPTIONS:
   --name value           Specify dapp name
   --type value           Specify dapp type, one of tool, application, game and others
   --desc value           Specify dapp description
   --url value            Specify dapp url
   --contractAddrs value  Specify dapp contract addr. If there are multiple contract addresses, separate them with ','
   --permission value     Specify the addr of users which are not allowed to see the dapp. If there are multiple contract addresses, separate them with ','
   --reason value         Specify register reason
```

**参数解释**

- `name`: dapp名称；
- `type`: dapp类型，工具类、应用类、游戏类或者其他类；
- `desc`: dapp描述；
- `url`: dapp网址；
- `contractAddrs`: dapp合约地址，需要在BitXHub中部署；
- `permission`: dapp使用者地址黑名单；
- `reason`: 注册dapp理由。

**示例说明**

```shell
$ bitxhub --repo <private_key_dir> client governance dapp register --name dapp_example --type tool --desc "a test tool" --url https://github.com/meshplus/bitxhub --contractAddrs 0x49cc3DA4DADb222B6bFBdD5B2b148472B67Aa409 --reason test

proposal id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-0, dapp id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-0
```

###### bitxhub client governance dapp update
`bixthub client governance dapp update`命令用于更新dapp信息

```shell
NAME:
   BitXHub client governance dapp update - update dapp

USAGE:
   BitXHub client governance dapp update [command options] [arguments...]

OPTIONS:
   --id value             Specify dapp id
   --name value           Specify dapp name
   --desc value           Specify dapp description
   --url value            Specify dapp url
   --contractAddrs value  Specify dapp contract addr. If there are multiple contract addresses, separate them with ','
   --permission value     Specify the addr of users which are not allowed to see the dapp. If there are multiple contract addresses, separate them with ','
   --reason value         Specify register reason
```

**参数解释**

- `id`: dapp id；
- `name`: 更新后dapp名称；
- `desc`: 更新后dapp描述；
- `url`: 更新后dapp网址；
- `contractAddrs`: 更新后dapp合约地址；
- `permission`: 更新后dapp使用黑名单；
- `reason`: 更新dapp信息理由。

**示例说明**

```shell
$ bitxhub --repo <owner_private_key_dir> client governance dapp update --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --name dappUpdate --desc "dapp update" --reason test

#控制台输出
proposal id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-1
```

###### bitxhub client governance dapp freeze
`bitxhub client governance dapp freeze`命令用于冻结dapp

```shell
NAME:
   BitXHub client governance dapp freeze - freeze dapp by dapp id

USAGE:
   BitXHub client governance dapp freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --reason value  Specify freeze reason
```

**参数解释**

- `id`: dapp id；
- `reason`: 冻结dapp理由。

**示例说明**

```shell
$ bitxhub --repo <goernanceAdmin_private_key_dir> client governance dapp freeze --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --reason test

# 控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
```

###### bitxhub client governance dapp activate
`bitxhub client governance dapp activate`命令用于激活dapp

```shell
NAME:
   BitXHub client governance dapp activate - activate dapp by dapp id

USAGE:
   BitXHub client governance dapp activate [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --reason value  Specify activate reason
```

**参数解释**

- `id`: dapp id；
- `reason`: 激活dapp理由。

**示例说明**

```shell
$ bitxhub --repo <goernanceAdmin_private_key_dir> client governance dapp activate --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
```

###### bitxhub client governance dapp transfer
`bitxhub client governance dapp transfer`命令用于将某dapp所有权转给其他用户

```shell
NAME:
   BitXHub client governance dapp transfer - transfer dapp to other user

USAGE:
   BitXHub client governance dapp transfer [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --addr value    Specify new owner addr
   --reason value  Specify freeze reason
```

**参数解释**

- `id`: dapp id；
- `addr`: 新所有者地址；
- `reason`: 转让dapp所有权理由。

**示例说明**

```shell
$ bitxhub --repo <owner_private_key_dir> client governance dapp transfer --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --addr 0x29366700f2E3963646D61Cd66d0d38Fd4Db1e664 --reason test

#控制台输出
proposal id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-2
```

###### bitxhub client governance dapp confirm
`bitxhub client governance dapp confirm`命令用于被转让人确认dapp转让

```shell
NAME:
   BitXHub client governance dapp confirm - confirm dapp transfer

USAGE:
   BitXHub client governance dapp confirm [command options] [arguments...]

OPTIONS:
   --id value  Specify dapp id
```

**参数解释**

- `id`: dapp id。

**示例说明**

```shell
$ bitxhub --repo <tranferredUser_private_key_dir> client governance dapp confirm --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0

#控制台输出
confirm dapp transfer success
```

###### bitxhub client governance dapp evaluate
`bitxhub client governance dapp evaluate`命令用于评价某dapp

```shell
NAME:
   BitXHub client governance dapp evaluate - evaluate dapp

USAGE:
   BitXHub client governance dapp evaluate [command options] [arguments...]

OPTIONS:
   --id value     Specify dapp id
   --desc value   Specify evaluate desc
   --score value  Specify evaluate score, [0,5] (default: 0)
```

**参数解释**

- `id`: dapp id；
- `desc`: 评价内容；
- `score`: 评分。

**示例说明**

```shell
$ bitxhub --repo <evaluator_private_key_dir> client governance dapp evaluate --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --desc "good" --score 5

#控制台输出
evaluate dapp success
```

##### bitxhub client governance service
`bitxhub client governance service`命令用于服务管理。命令说明如下：

```shell
NAME:
   BitXHub client governance service - service manage command

USAGE:
   BitXHub client governance service command [command options] [arguments...]

COMMANDS:
   status       query service status by chainService id
   appServices  query services by appchain id
   freeze       freeze service by chainService id
   activate     activate service by chainService id
   evaluate     evaluate service
```

**子命令**

###### bitxhub client governance service status
`bitxhub client governance service status`命令用于查询服务状态

```shell
NAME:
   BitXHub client governance service status - query service status by chainService id

USAGE:
   BitXHub client governance service status [command options] [arguments...]

OPTIONS:
   --id value  Specify chainService id
```

**参数解释**

- `id`: 服务id。

**示例说明**

```shell
$ bitxhub client governance service status --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13

#控制台输出
service chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 is available
```

###### bitxhub client governance service appServices
`bitxhub client governance service appServices`命令用于查询某条应用链所有服务的信息

```shell
NAME:
   BitXHub client governance service appServices - query services by appchain id

USAGE:
   BitXHub client governance service appServices [command options] [arguments...]

OPTIONS:
   --chainID value  Specify appchain id
```

**参数解释**

- `chainID`: 应用链id。

**示例说明**

```shell
$ bitxhub client governance service appServices --chainID chain0

#控制台输出
========================================================================================
ChainID  ServiceID                                   Name  Type          Intro  Ordered  Createtime           Score  Status
-------  ---------                                   ----  ----          -----  -------  ----------           -----  ------
chain0   0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13  d0    CallContract  i      true     1642129992547460000  0      available
```

###### bitxhub client governance service freeze
`bitxhub client governance service freeze`命令用于冻结服务

```shell
NAME:
   BitXHub client governance service freeze - freeze service by chainService id

USAGE:
   BitXHub client governance service freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify chainService id
   --reason value  Specify freeze reason
```

**参数解释**

- `id`: 服务id；
- `reason`: 冻结服务理由。

**示例说明**

```shell
$ bitxhub --repo <governanceAdmin_private_key_dir> client governance service freeze --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
```

###### bitxhub client governance service activate
`bitxhub client governance service activate`命令用于激活服务

```shell
NAME:
   BitXHub client governance service activate - activate service by chainService id

USAGE:
   BitXHub client governance service activate [command options] [arguments...]

OPTIONS:
   --id value      Specify chainService id
   --reason value  Specify activate reason
```

**参数解释**

- `id`: 服务id；
- `reason`: 激活服务理由。

**示例说明**

```shell
$ bitxhub --repo <governanceAdmin_private_key_dir> client governance service activate --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
```

###### bitxhub client governance service evaluate
`bitxhub client governance service evaluate`命令用于评价服务

```shell
NAME:
   BitXHub client governance service evaluate - evaluate service

USAGE:
   BitXHub client governance service evaluate [command options] [arguments...]

OPTIONS:
   --id value     Specify service id
   --desc value   Specify evaluate desc
   --score value  Specify evaluate score, [0,5] (default: 0)
```

**参数解释**

- `id`: 服务id；
- `desc`: 评价内容；
- `score`: 评分。

**示例说明**

```shell
$ bitxhub --repo <evaluator_private_key_dir> client governance service evaluate --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 --desc "good" --score 5

#控制台输出
evaluate service success
```

##### bitxhub client governance strategy
`bitxhub client governance strategy`命令用于投票策略管理。命令说明如下：

```shell
NAME:
   BitXHub client governance strategy - proposal strategy command

USAGE:
   BitXHub client governance strategy command [command options] [arguments...]

COMMANDS:
   all     query all proposal strategy
   update  update proposal strategy
```

**子命令**

###### bitxhub client governance strategy all
`bitxhub client governance strategy all`命令用于查询所有模块投票策略

```shell
NAME:
   BitXHub client governance strategy all - query all proposal strategy

USAGE:
   BitXHub client governance strategy all [arguments...]
```

**示例说明**

```shell
$ bitxhub client governance strategy all 

#控制台输出
module                 strategy        Extra  Status
------                 --------        -----  ------
appchain_mgr           ZeroPermission         available
node_mgr               ZeroPermission         available
dapp_mgr               ZeroPermission         available
role_mgr               ZeroPermission         available
rule_mgr               ZeroPermission         available
service_mgr            ZeroPermission         available
proposal_strategy_mgr  ZeroPermission         available
```

###### bitxhub client governance strategy update
`bitxhub client governance strategy update`命令用于更新某模块投票策略

```shell
NAME:
   BitXHub client governance strategy update - update proposal strategy

USAGE:
   BitXHub client governance strategy update [command options] [arguments...]

OPTIONS:
   --module value  module name(appchain_mgr, rule_mgr, node_mgr, service_mgr, role_mgr, proposal_strategy_mgr, dapp_mgr, all_mgr)
   --typ value     proposal strategy(SimpleMajority or ZeroPermission) (default: "SimpleMajority")
   --extra value   expression of strategy. In this expression, 'a' represents the number of people approve, 'r' represents the number of people against, and 't' represents the total number of people who can vote. (default: "a > 0.5 * t")
   --reason value  update reason
```

**参数解释**

- `module`: 模块名称，应用链管理、规则管理、节点管理、服务管理、角色管理、提案管理、投票策略管理、dapp管理（all_mgr用于更新全部模块）；
- `typ`: 投票策略类型，目前支持一票通过制和简单多数制；
- `extra`: 投票通过策略；
- `reason`: 更新投票策略理由。

**示例说明**

```shell
$ bitxhub --repo <governanceAdmin_private_key_dir> client governance strategy update --module appchain_mgr --reason test

#控制台输出
proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-4
```
