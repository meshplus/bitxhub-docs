# bitxhub

中继链命令描述如下：

```shell
$ bitxhub -h

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

GLOBAL OPTIONS:
   --repo value   BitXHub storage repo path
   --help, -h     show help
   --version, -v  print the version
```

**全局参数解释**：

- `--repo`: 可选参数，指定bitxhub节点配置文件所在目录，如果不指定，默认使用`~/.bitxhub`目录。

注：若通过bitxhub项目代码编译，并通过make cluster启动，默认有4个管理员节点，它们的配置文件目录(--repo)分别在bitxhub/script/build/node1~node4。

**子命令**：

## 1. 显示配置

`bitxhub config`命令用于显示当前节点配置，若未指定`--repo`则默认使用`~/.bitxhub/`路径。

**示例**：

```shell
$ bitxhub config

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
  "repo_root": "/Users/xxx/.bitxhub",
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

## 2. 初始化

`bitxhub init`命令用于初始化节点配置：**若未指定`--repo`则默认使用`~/.bitxhub/`路径**，若路径下存在旧配置，则会提示覆盖与否。

**示例**：

```shell
$ bitxhub init

initializing bitxhub at /Users/xxx/.bitxhub
bitxhub configuration file already exists
reinitializing would overwrite your configuration, Y/N?
y
```

注：初始化后，会在目录下生成节点的配置文件，可参考[中继链部署](../../../quick_start/build_cross_network/single_bitxhub/deploy_bitxhub)修改相关配置。其中，bitxhub.toml文件定义了管理员节点。

## 3. 启动

`bitxhub start`命令用于启动BitXHub的节点。

```shell
$ bitxhub start -h

NAME:
   bitxhub start - Start a long-running daemon process

USAGE:
   bitxhub start [command options] [arguments...]

OPTIONS:
   --config value   Specify BitXHub config path
   --network value  Specify BitXHub network config path
   --order value    Specify BitXHub order config path
   --passwd value   Specify BitXHub node private key password
```

**参数解释**：

- `--config`：指定节点配置路径，一般指`bitxhub.toml`；
- `--network`：指定节点p2p网络配置文件，一般指`network.toml`；
- `--order`：指定节点共识配置文件，一般指`order.toml`；
- `--passwd`：指定节点私钥密码。

若不指定以上配置，则至`--repo`路径下寻找，若未指定`--repo`，则默认至`~/.bitxhub/`路径下寻找。

**示例**：

```shell
# 若未指定`--repo`，则默认至`~/.bitxhub/`路径下寻找
$ bitxhub start

BitXHub version: dev-release-2.0-9222137
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

## 4. 私钥管理

`bitxhub key`命令提供针对bitxhub节点的私钥管理。它包含了私钥生成、私钥格式转换、私钥查看、地址查看。

```shell
$ bitxhub key -h

NAME:
   BitXHub key - BitXHub private key tools

USAGE:
   BitXHub key command [command options] [arguments...]

COMMANDS:
   gen      Generate new private key in specified directory
   show     Show BitXHub private key info
   address  Show address from BitXHub private key

OPTIONS:
   --help, -h  show help
```

**子命令**：

### 4.1. 生成私钥

`bitxhub key gen`命令用于生成私钥文件`key.json`。

```shell
$ bitxhub key gen -h

NAME:
   BitXHub key gen - Generate new private key in specified directory

USAGE:
   BitXHub key gen [command options] [arguments...]

OPTIONS:
   --target value  Specify target directory
   --passwd value  Specify password
   --algo value    Specify crypto algorithm (default: "Secp256k1")
```

**参数解释**：

- `--target`：指定生成路径；
- `--passwd`：指定私钥密码;
- `--algo`: 指定私钥加密算法。

**示例**：

```shell
$ bitxhub key gen --target ~/.bitxhub

key.json key is generated under directory /Users/xxx/.bitxhub
```

### 4.2. 显示私钥内容

`bitxhub key show`命令用于显示`key.json`具体内容。

```shell
$ bitxhub key show -h

NAME:
   BitXHub key show - Show BitXHub private key info

USAGE:
   BitXHub key show [command options] [arguments...]

OPTIONS:
   --path value    Specify private key path
   --passwd value  Specify password
```

**参数解释**：

- `--path`：指定私钥文件所在路径；
- `--passwd`：指定私钥密码。

**示例**：

```shell
$ bitxhub key show --path ~/.bitxhub/key.json

private key: aeb91e2d478950094b3490c0d916583c53993e6767b1cebd335c0e5b6021ef6d
public key: 0476832ada8f6cfef41ef226fb5ed1812a4d5f8dd66ad133dd9459ff014a2de108eb63617a731451398b258e92c19c44a4c78e645c35fd2921beab9e38b4aae0b2
address: 0x17f8e065ca9e743Cb23557d1eb4561Bd0eF52f64
```

### 4.3. 显示私钥地址

`bitxhub key address`命令用于显示key.json对应地址。

```shell
$ bitxhub key address -h

NAME:
   BitXHub key address - Show address from BitXHub private key

USAGE:
   BitXHub key address [command options] [arguments...]

OPTIONS:
   --path value    Specify private key path
   --passwd value  Specify password
```

**参数解释**：

- `--path`：指定私钥文件所在路径；
- `--passwd`：指定私钥密码。

**示例**：

```shell
$ bitxhub key address --path ~/.bitxhub/key.json

0x3Db84D019fCd3A845e2fB3A21B56812Ffd933f19
```

## 5. 显示版本

**功能**：

`bitxhub version`命令用于显示BitXHub的版本信息。它会在命令行显示版本、构建日期、操作系统/架构和Go编译器版本。

```shell
$ bitxhub version -h

NAME:
   bitxhub version - BitXHub version

USAGE:
   bitxhub version [arguments...]
```

**示例**：

```shell
$ bitxhub version

BitXHub version: dev-master-7a9d335
App build date: 2022-06-17T16:20:52
System version: darwin/amd64
Golang version: go1.15
```

## 6. 证书管理

**功能**：

`bitxhub cert`命令提供一系列x509标准的证书工具，服务于中继链节点的准入机制。它包含了CA生成、CSR描述生成、证书签发、证书解析、证书私钥生成以及证书校验。

```shell
$ bitxhub cert -h

NAME:
   BitXHub cert - Certification tools

USAGE:
   BitXHub cert command [command options] [arguments...]

COMMANDS:
   ca      Generate ca cert and private key
   csr     Generate csr file
   issue   Issue certification by ca
   parse   Parse certification
   priv    Generate and show private key for certificate
   verify  Verify cert

OPTIONS:
   --help, -h  show help
```

**子命令**：

### 6.1. 生成根CA证书

`bitxhub cert ca`命令用于生成根CA证书。

```shell
$ bitxhub cert ca -h

NAME:
   BitXHub cert ca - Generate ca cert and private key

USAGE:
   BitXHub cert ca [arguments...]
```

**示例**：

```shell
$ cd ~/.bitxhub/certs
$ bitxhub cert ca

# 命令会在目录下生成如下文件
├── ca.cert
├── ca.priv
```

### 6.2. 管理证书私钥

`bitxhub cert priv`命令用于生成或展示用于p2p网络通信的私钥。

```shell
$ bitxhub cert priv -h

NAME:
   BitXHub cert priv - Generate and show private key for certificate

USAGE:
   BitXHub cert priv command [command options] [arguments...]

COMMANDS:
   gen  Create new private key
   pid  Show pid from private key

OPTIONS:
   --help, -h  show help
```

**子命令**：

#### 6.2.1. 生成私钥

`bitxhub cert priv gen`命令用于生成p2p网络通信私钥。

```shell
$ bitxhub cert priv gen -h

NAME:
   BitXHub cert priv gen - Create new private key

USAGE:
   BitXHub cert priv gen [command options] [arguments...]

OPTIONS:
   --name value    Specify private key name
   --target value  Specify target directory
```

**示例**：

```shell
$ cd ~/.bitxhub/certs
$ bitxhub cert priv gen --name test --target .

test.priv key is generated under directory /Users/xxx/.bitxhub/certs
```

#### 6.2.2. 显示私钥pid

`bitxhub cert priv pid`命令用于获取libp2p节点pid。

```shell
$ bitxhub cert priv pid -h

NAME:
   BitXHub cert priv pid - Show pid from private key

USAGE:
   BitXHub cert priv pid [command options] [arguments...]

OPTIONS:
   --path value  Specify private key path
```

**示例**：

```shell
$ cd ~/.bitxhub/certs
$ bitxhub cert priv pid --path ./test.priv

QmWw4hPtm51jpxzKjyYMe3E7bNei9wdoug6B1rZqSwVSL4
```

### 6.3. 生成csr文件

`bitxhub cert csr`命令用于生成CSR描述信息。

```shell
$ bitxhub cert csr -h

NAME:
   BitXHub cert csr - Generate csr file

USAGE:
   BitXHub cert csr [command options] [arguments...]

OPTIONS:
   --key value     Specify Secp256r1 private key path
   --org value     Specify organization name
   --target value  Specify target directory
```

**参数解释**：

- `--key`: 指定私钥路径；
- `--org`: 指定被签发人组织名称；
- `--target`: 指定目标CSR路径。

**示例**：

```shell
$ cd ~/.bitxhub/certs
# 使用上述 bitxhub cert priv gen 命令示例中生成的私钥 test.priv
# 命令会在目录下生成下级csr文件test.csr
$ bitxhub cert csr --key test.priv --org test --target .
```

### 6.4. 签发证书

`bitxhub cert issue`命令用于证书签发。

```shell
$ bitxhub cert issue -h

NAME:
   BitXHub cert issue - Issue certification by ca

USAGE:
   BitXHub cert issue [command options] [arguments...]

OPTIONS:
   --csr value     Specify csr path
   --is_ca value   Specify whether it's ca
   --key value     Specify ca's secp256r1 private key path
   --cert value    Specify ca certification path
   --target value  Specific target directory
```

**参数解释**：

- `--csr`: 指定CSR描述文件路径；
- `--is_ca`: 指定签发人是否为CA证书；
- `--key`: 指定当前签发人私钥路径；
- `--cert`: 指定当前签发人证书路径；
- `--target`: 指定生成的证书路径。

**示例**：

```shell
$ cd ~/.bitxhub/certs
# 使用上述 bitxhub cert csr 命令示例中生成的 test.csr
# 命令会在目录生成下级证书文件 test.cert
$ bitxhub cert issue --csr test.csr --is_ca true --key ca.priv --cert ca.cert --target .
```

### 6.5. 解析证书

`bitxhub cert parse`命令用于证书解析。

```shell
$ bitxhub cert parse -h

NAME:
   BitXHub cert parse - Parse certification

USAGE:
   BitXHub cert parse [command options] [arguments...]

OPTIONS:
   --path value  Specify certification path
```

**参数解释**：

- `--path`: 指定证书路径。

**示例**：

```shell
$ cd ~/.bitxhub/certs
$ bitxhub cert parse --path ca.cert

{"Raw":"MIICkjCCAjegAwIBAgICIKUwCgYIKoZIzj0EAwIwgaExCzAJBgNVBAYTAkNOMREwDwYDVQQIEwhaaGVKaWFuZzERMA8GA1UEBxMISGFuZ1pob3UxHzANBgNVBAkTBnN0cmVldDAOBgNVBAkTB2FkZHJlc3MxDzANBgNVBBETBjMyNDAwMDETMBEGA1UEChMKSHlwZXJjaGFpbjEQMA4GA1UECxMHQml0WEh1YjETMBEGA1UEAxMKYml0eGh1Yi5jbjAgFw0yMjAxMTQwNTQzNTNaGA8yMDcyMDEwMjA1NDM1M1owgZsxCzAJBgNVBAYTAkNOMREwDwYDVQQIEwhaaGVKaWFuZzERMA8GA1UEBxMISGFuZ1pob3UxHzANBgNVBAkTBnN0cmVldDAOBgNVBAkTB2FkZHJlc3MxDzANBgNVBBETBjMyNDAwMDEQMA4GA1UEChMHdGVzdE9yZzEQMA4GA1UECxMHQml0WEh1YjEQMA4GA1UEAxMHQml0WEh1YjBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABLasY+fj/B1NnEThaYgWOsH8vya9js0hdZ4B9t6CXbLXIUvHWNIXTsjXleHAsi6EzCFmGTSGfqMIkGXZ4pYDxrGjYTBfMA4GA1UdDwEB/wQEAwIBpjAPBgNVHSUECDAGBgRVHSUAMA8GA1UdEwEB/wQFMAMBAf8wKwYDVR0jBCQwIoAgALrjuzAzMBMHVnRjgANMbl/Bl7jmmXmfIUksD+50s7kwCgYIKoZIzj0EAwIDSQAwRgIhAMMYaKjIiDHG7LTqiA2J3DmGUZDwb3VQnFx3ItXKpqozAiEAwBn1I2JmXMcBkCFFwNGxyrfCnmHuezumNNB2AuokKvU=","RawTBSCertificate":"MIICN6ADAgECAgIgpTAKBggqhkjOPQQDAjCBoTELMAkGA1UEBhMCQ04xETAPBgNVBAgTCFpoZUppYW5nMREwDwYDVQQHEwhIYW5nWmhvdTEfMA0GA1UECRMGc3RyZWV0MA4GA1UECRMHYWRkcmVzczEPMA0GA1UEERMGMzI0MDAwMRMwEQYDVQQKEwpIeXBlcmNoYWluMRAwDgYDVQQLEwdCaXRYSHViMRMwEQYDVQQDEwpiaXR4aHViLmNuMCAXDTIyMDExNDA1NDM1M1oYDzIwNzIwMTAyMDU0MzUzWjCBmzELMAkGA1UEBhMCQ04xETAPBgNVBAgTCFpoZUppYW5nMREwDwYDVQQHEwhIYW5nWmhvdTEfMA0GA1UECRMGc3RyZWV0MA4GA1UECRMHYWRkcmVzczEPMA0GA1UEERMGMzI0MDAwMRAwDgYDVQQKEwd0ZXN0T3JnMRAwDgYDVQQLEwdCaXRYSHViMRAwDgYDVQQDEwdCaXRYSHViMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEtqxj5+P8HU2cROFpiBY6wfy/Jr2OzSF1ngH23oJdstchS8dY0hdOyNeV4cCyLoTMIWYZNIZ+owiQZdnilgPGsaNhMF8wDgYDVR0PAQH/BAQDAgGmMA8GA1UdJQQIMAYGBFUdJQAwDwYDVR0TAQH/BAUwAwEB/zArBgNVHSMEJDAigCAAuuO7MDMwEwdWdGOAA0xuX8GXuOaZeZ8hSSwP7nSzuQ==","RawSubjectPublicKeyInfo":"MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEtqxj5+P8HU2cROFpiBY6wfy/Jr2OzSF1ngH23oJdstchS8dY0hdOyNeV4cCyLoTMIWYZNIZ+owiQZdnilgPGsQ==","RawSubject":"MIGbMQswCQYDVQQGEwJDTjERMA8GA1UECBMIWmhlSmlhbmcxETAPBgNVBAcTCEhhbmdaaG91MR8wDQYDVQQJEwZzdHJlZXQwDgYDVQQJEwdhZGRyZXNzMQ8wDQYDVQQREwYzMjQwMDAxEDAOBgNVBAoTB3Rlc3RPcmcxEDAOBgNVBAsTB0JpdFhIdWIxEDAOBgNVBAMTB0JpdFhIdWI=","RawIssuer":"MIGhMQswCQYDVQQGEwJDTjERMA8GA1UECBMIWmhlSmlhbmcxETAPBgNVBAcTCEhhbmdaaG91MR8wDQYDVQQJEwZzdHJlZXQwDgYDVQQJEwdhZGRyZXNzMQ8wDQYDVQQREwYzMjQwMDAxEzARBgNVBAoTCkh5cGVyY2hhaW4xEDAOBgNVBAsTB0JpdFhIdWIxEzARBgNVBAMTCmJpdHhodWIuY24=","Signature":"MEYCIQDDGGioyIgxxuy06ogNidw5hlGQ8G91UJxcdyLVyqaqMwIhAMAZ9SNiZlzHAZAhRcDRscq3wp5h7ns7pjTQdgLqJCr1","SignatureAlgorithm":10,"PublicKeyAlgorithm":3,"PublicKey":{"Curve":{"P":115792089210356248762697446949407573530086143415290314195533631308867097853951,"N":115792089210356248762697446949407573529996955224135760342422259061068512044369,"B":41058363725152142129326129780047268409114441015993725554835256314039467401291,"Gx":48439561293906451759052585252797914202762949526041747995844080717082404635286,"Gy":36134250956749795798585127919587881956611106672985015071877198253568414405109,"BitSize":256,"Name":"P-256"},"X":82625525661941981250491613743007096153774500696862681832425790330116014715607,"Y":15060213375229657175963991955251470872818906153418440931864025589557900854961},"Version":3,"SerialNumber":8357,"Issuer":{"Country":["CN"],"Organization":["Hyperchain"],"OrganizationalUnit":["BitXHub"],"Locality":["HangZhou"],"Province":["ZheJiang"],"StreetAddress":["street","address"],"PostalCode":["324000"],"SerialNumber":"","CommonName":"bitxhub.cn","Names":[{"Type":[2,5,4,6],"Value":"CN"},{"Type":[2,5,4,8],"Value":"ZheJiang"},{"Type":[2,5,4,7],"Value":"HangZhou"},{"Type":[2,5,4,9],"Value":"street"},{"Type":[2,5,4,9],"Value":"address"},{"Type":[2,5,4,17],"Value":"324000"},{"Type":[2,5,4,10],"Value":"Hyperchain"},{"Type":[2,5,4,11],"Value":"BitXHub"},{"Type":[2,5,4,3],"Value":"bitxhub.cn"}],"ExtraNames":null},"Subject":{"Country":["CN"],"Organization":["testOrg"],"OrganizationalUnit":["BitXHub"],"Locality":["HangZhou"],"Province":["ZheJiang"],"StreetAddress":["street","address"],"PostalCode":["324000"],"SerialNumber":"","CommonName":"BitXHub","Names":[{"Type":[2,5,4,6],"Value":"CN"},{"Type":[2,5,4,8],"Value":"ZheJiang"},{"Type":[2,5,4,7],"Value":"HangZhou"},{"Type":[2,5,4,9],"Value":"street"},{"Type":[2,5,4,9],"Value":"address"},{"Type":[2,5,4,17],"Value":"324000"},{"Type":[2,5,4,10],"Value":"testOrg"},{"Type":[2,5,4,11],"Value":"BitXHub"},{"Type":[2,5,4,3],"Value":"BitXHub"}],"ExtraNames":null},"NotBefore":"2022-01-14T05:43:53Z","NotAfter":"2072-01-02T05:43:53Z","KeyUsage":101,"Extensions":[{"Id":[2,5,29,15],"Critical":true,"Value":"AwIBpg=="},{"Id":[2,5,29,37],"Critical":false,"Value":"MAYGBFUdJQA="},{"Id":[2,5,29,19],"Critical":true,"Value":"MAMBAf8="},{"Id":[2,5,29,35],"Critical":false,"Value":"MCKAIAC647swMzATB1Z0Y4ADTG5fwZe45pl5nyFJLA/udLO5"}],"ExtraExtensions":null,"UnhandledCriticalExtensions":null,"ExtKeyUsage":[0],"UnknownExtKeyUsage":null,"BasicConstraintsValid":true,"IsCA":true,"MaxPathLen":-1,"MaxPathLenZero":false,"SubjectKeyId":null,"AuthorityKeyId":"ALrjuzAzMBMHVnRjgANMbl/Bl7jmmXmfIUksD+50s7k=","OCSPServer":null,"IssuingCertificateURL":null,"DNSNames":null,"EmailAddresses":null,"IPAddresses":null,"URIs":null,"PermittedDNSDomainsCritical":false,"PermittedDNSDomains":null,"ExcludedDNSDomains":null,"PermittedIPRanges":null,"ExcludedIPRanges":null,"PermittedEmailAddresses":null,"ExcludedEmailAddresses":null,"PermittedURIDomains":null,"ExcludedURIDomains":null,"CRLDistributionPoints":null,"PolicyIdentifiers":null}
```

### 6.6. 验证证书

`bitxhub cert verify`用于验证证书。

```shell
$ bitxhub cert verify -h
NAME:
   BitXHub cert verify - Verify cert

USAGE:
   BitXHub cert verify [command options] [arguments...]

OPTIONS:
   --sub value  Specify sub cert path
   --ca value   Specify ca cert path
```

**参数解释**：

- `--sub`: 下级证书路径；
- `--ca`: ca证书路径。

**示例**：

```shell
# 使用上述 bitxhub cert issue 命令示例中生成的下级证书文件 test.cert
# 若验证通过，则控制台无输出，若验证失败，则会有报错信息
$ bitxhub cert verify --sub test.cert --ca ca.cert
```

## 7. 中继链客户端

**功能**：

`bitxhub client`提供中继链节点的客户端操作命令。它包含了账户信息查询、中继链信息、区块查询、节点网络信息、回执查询、交易操作、验证者集查询、删除共识节点、提案治理。

```shell
$ bitxhub client -h

NAME:
   BitXHub client - BitXHub client command

USAGE:
   BitXHub client command [command options] [arguments...]

COMMANDS:
   account     Query account information
   chain       Query BitXHub chain info
   block       Query block by block hash or block height
   network     Query network info from node
   transfer    Transfer balance from address to address
   receipt     Query transaction receipt by transaction hash
   tx          Query transaction by transaction hash
   validators  Query validator address
   governance  BitXHub governance command

OPTIONS:
   --gateway value  Specific gateway address (default: "http://localhost:9091/v1/")
   --ca value       Specific ca cert file if https is enabled
   --cert value     Specific access cert file if https is enabled
   --key value      Specific access key file if https is enabled
   --help, -h       show help
```

**参数解释**：

- `--gateway`: 指定客户端http服务地址，如果节点开启了TLS功能，则需要传递https地址；
- `--ca`: 指定CA证书，如果节点开启了TLS功能，需要传递；
- `--cert`: 指定节点的证书，如果节点开启了TLS功能，需要传递；
- `--key`: 指定节点的私钥，如果节点开启了TLS功能，需要传递。

**子命令**：

### 7.1. 查询账户信息

`bitxhub client account`命令用于账户信息查询。

```shell
$ bitxhub client account -h

NAME:
   BitXHub client account - Query account information

USAGE:
   BitXHub client account [command options] [arguments...]

OPTIONS:
   --address value  Specify account address
```

**参数解释**：

- `--address`: 指定账户地址，需要传递。

**示例**：

```shell
# ~/.bitxhub/ 对应节点的地址可通过 bitxhub key address --path ~/.bitxhub/key.json 命令查看
$ bitxhub client account --address 0x79a1215469FaB6f9c63c1816b45183AD3624bE34

{
  "type": "normal",
  "balance": 100000000000000000000000000000000000,
  "contract_count": 0,
  "code_hash": "0xc5d2460186f7233C927e7db2dCc703c0E500B653cA82273b7bFaD8045d85A470"
}
```

### 7.2. 查询中继链信息和状态

`bitxhub client chain`命令用于中继链信息查询和状态。

```shell
$ bitxhub client chain -h

NAME:
   BitXHub client chain - Query BitXHub chain info

USAGE:
   BitXHub client chain command [command options] [arguments...]

COMMANDS:
   meta    Query BitXHub chain meta
   status  Query BitXHub chain status

OPTIONS:
   --help, -h  show help
```

**子命令**：

#### 7.2.1. 查询中继链信息

`bitxhub client chain meta`用于查询中继链信息。

```shell
$ bitxhub client chain meta -h

NAME:
   BitXHub client chain meta - Query BitXHub chain meta

USAGE:
   BitXHub client chain meta [arguments...]
```

**示例**：

```shell
$ bitxhub client chain meta

{"height":"1","block_hash":"0x47dBBdcE25e2ab4BbA8ee325Db8502Aa6c2A065Af1350450F0917d5e139465A1"}
```

#### 7.2.2. 查询中继链状态

`bitxhub client chain status`命令用于查询中继链状态是否可用。

```shell
$ bitxhub client chain status -h

NAME:
   BitXHub client chain status - Query BitXHub chain status

USAGE:
   BitXHub client chain status [arguments...]
```

**示例**：

```shell
# normal: 已处在共识状态
# abnormal：未共识状态，集群没有正常运行
$ bitxhub client chain status

normal
```

### 7.3. 查询区块信息

`bitxhub client block`命令用于区块信息查询，**可指定区块号或区块Hash**。

```shell
$ bitxhub client block -h

NAME:
   BitXHub client block - Query block by block hash or block height

USAGE:
   BitXHub client block [arguments...]
```

**示例**：

```shell
$ bitxhub client block 1

{"block_header":{"number":"1","state_root":"0x5B8A0f51c912c38501C263DEC58542453F1a6B399a491C27363dF1D9c3029328","tx_root":"0x0000000000000000000000000000000000000000000000000000000000000000","receipt_root":"0x0000000000000000000000000000000000000000000000000000000000000000","parent_hash":"0x0000000000000000000000000000000000000000000000000000000000000000","timestamp":"1642052145652059000","Bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"},"transactions":{},"block_hash":"0x47dBBdcE25e2ab4BbA8ee325Db8502Aa6c2A065Af1350450F0917d5e139465A1","signature":"7B4yjNFnnQO+mw2poSnrsGkXMxMkDcGyzXlPioahKo1MCirTQ1hc+imoIr6d/59ZD//62N5asPQjEwVLpOBuowE="}

# 查询最新区块
$ bitxhub client block
{"block_header": { "number": "1", "state_root": "0x4e9781F52F46eF1E1BE918F57469C9e28B8E706cE5e38eA9f821598Bf7751842", "tx_root": "0x0000000000000000000000000000000000000000000000000000000000000000", "receipt_root": "0x0000000000000000000000000000000000000000000000000000000000000000", "parent_hash": "0x0000000000000000000000000000000000000000000000000000000000000000", "timestamp": "1673417754854410000", "Bloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000" }, "transactions": {}, "block_hash": "0x0ad3187dCBa835EEc70C2C3844E8fED56d21A639fB6c1ca03879e6F3c2195C8f", "signature": "TEMhM1Xzv7+dKjtPwP3g4aHZABdRRGwhXyBqVavby4VguFPXXI4oEVZoQFhsi5qDjp7VWPBD6ZCYBIWVP1krzgE=" }
```

### 7.4. 查询网络信息

`bitxhub client network`命令用于查询全网节点信息。

```shell
$ bitxhub client network -h

NAME:
   BitXHub client network - Query network info from node

USAGE:
   BitXHub client network [arguments...]
```

**示例**：

```shell
$ bitxhub client network

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

### 7.5. 发送转账交易

`bitxhub client transfer`命令用于发送转账交易。

```shell
$ bitxhub client transfer -h

NAME:
   BitXHub client transfer - Transfer balance from address to address

USAGE:
   BitXHub client transfer [command options] [arguments...]

OPTIONS:
   --key value     Specify transfer account private key path
   --to value      Specify transfer target address
   --amount value  Specify transfer amount (default: "1000000000000000000")
```

**参数说明**：

- `--key` 转账发起人的密钥；
- `--to` 目标账户的地址；
- `--amount` 转账金额数量，不填则默认1000000000000000000。

**示例**：

```shell
# 不指定 --repo 则默认使用 ~/.bitxhub 目录下的节点配置
# ~/.bitxhub 目录对应节点的密钥在 ~/.bitxhub/key.json
# 此笔交易的发起人为 ~/.bitxhub 目录对应节点
$ bitxhub client transfer --key ~/.bitxhub/key.json --to 0x134213199d40c4b26cB8B0E5D53d71E309223403 --amount 1000000000

{"tx_hash":"0xB3D1365F0154dB1294B718c57F9DAC6E59b34F75496082B4C57C8Cc9900Eb087"}
```

### 7.7. 查询交易回执

`bitxhub client receipt`命令用于交易回执查询。

```shell
$ bitxhub client receipt -h

NAME:
   BitXHub client receipt - Query transaction receipt by transaction hash

USAGE:
   BitXHub client receipt [arguments...]
```

**示例**：

```shell
# 此处使用的交易地址为上述 bitxhub client transfer 命令示例中的交易地址
$ bitxhub client receipt 0xB3D1365F0154dB1294B718c57F9DAC6E59b34F75496082B4C57C8Cc9900Eb087

{"tx_hash":"0xB3D1365F0154dB1294B718c57F9DAC6E59b34F75496082B4C57C8Cc9900Eb087","ret":"aW5zdWZmaWNlaWVudCBiYWxhbmNlOiBhZGRyZXNzIDB4NUYyZTdCNTJEQ2Y4NTAyMmIwQjk3MDAzQkFhMjBhOTIxNDEyNUQwRCBoYXZlIDAgd2FudCAxMDUwMDAwMDAw","status":1,"gas_used":"21000","bloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"}
```

### 7.8. 查询交易

`bitxhub client tx`命令用于交易查询。

```shell
$ bitxhub client tx -h

NAME:
   BitXHub client tx - Query transaction by transaction hash

USAGE:
   BitXHub client tx [arguments...]
```

**示例**：

```shell
# 此处使用的交易地址为上述 bitxhub client transfer 命令示例中的交易地址
$ bitxhub client tx 0xB3D1365F0154dB1294B718c57F9DAC6E59b34F75496082B4C57C8Cc9900Eb087

{"tx":{"from":"0x5F2e7B52DCf85022b0B97003BAa20a9214125D0D","to":"0x134213199d40c4b26cB8B0E5D53d71E309223403","timestamp":"1655714692398511000","transaction_hash":"0xB3D1365F0154dB1294B718c57F9DAC6E59b34F75496082B4C57C8Cc9900Eb087","payload":"EhMxMDAwMDAwMDAwMDAwMDAwMDAw","signature":"A1YUuO0Rv62WBpxXMe1Wc0kqvv9Sz6UjGCmlYjoJBeXpD4jeFpmChbMjQmYY3I6wZcbcS6RNxFqwzLS5Usq/jGAB"},"tx_meta":{"block_hash":"U+U0l8GbZHFNf11pzMZpXkzZ/yD69V72T2YTmYkhJ70=","block_height":"3"}}
```

### 7.9. 查询全网验证者

`bitxhub client validators`命令用于查询全网验证人信息。

```shell
$ bitxhub client validators -h

NAME:
   BitXHub client validators - Query validator address

USAGE:
   BitXHub client validators [arguments...]
```

**示例**：

```shell
$ bitxhub client validators

[
  "0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013",
  "0x79a1215469FaB6f9c63c1816b45183AD3624bE34",
  "0x97c8B516D19edBf575D72a172Af7F418BE498C37",
  "0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8"
]
```

### 7.10. 治理

`bitxhub client governance`命令用于治理。

```shell
$ bitxhub client governance -h

NAME:
   BitXHub client governance - BitXHub governance command

USAGE:
   BitXHub client governance command [command options] [arguments...]

COMMANDS:
   vote        Vote to a proposal
   proposal    Proposal manage command
   appchain    Appchain manage command
   interchain  Interchain manage command
   rule        Rule manage command
   node        Node manage command
   role        Role manage command
   dapp        Dapp manage command
   service     Service manage command
   strategy    Proposal strategy command

OPTIONS:
   --help, -h  show help
```

**子命令**：

#### 7.10.1. 投票

`bitxhub client governance vote`命令用于提案投票。

```shell
$ bitxhub client governance vote -h

NAME:
   BitXHub client governance vote - Vote to a proposal

USAGE:
   BitXHub client governance vote [command options] [arguments...]

OPTIONS:
   --id value      Specify proposal id
   --info value    Specify voting information, approve or reject
   --reason value  Specify reason to vote
```

**参数解释**：

- `--id`：必选参数。提案id，生成提案时将在控制台打印。
- `--info`：必选参数。是否通过提案。approve为通过，reject为拒绝。
- `--reason`：必选参数。投票理由说明。

**示例**：

```shell
# 只有管理员节点才能对提案进行投票
# 若通过bitxhub项目代码编译，并通过make cluster启动bitxhub，管理员1节点的repo在bitxhub/script/build/node1目录下
# 生成提案时，会在控制台打印提案id
$ BITXHUB_HOME="xxx/bitxhub" # bitxhub 项目路径
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance vote --id 0x134213199d40c4b26cB8B0E5D53d71E309223403-0 --info approve --reason "test"

vote successfully!
```

注：对于提案，都需要超过半数管理员投同意票，提案才算通过。

#### 7.10.2. 管理提案

`bitxhub client governance proposals`命令用于提案管理。

```shell
$ bitxhub client governance proposal -h

NAME:
   BitXHub client governance proposal - Proposal manage command

USAGE:
   BitXHub client governance proposal command [command options] [arguments...]

COMMANDS:
   query     Query proposals based on the condition
   withdraw  Withdraw a proposal

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询提案

`bitxhub client governance proposal query`命令用于提案查询。查询中继链提案的内容以及相关的状态。

```shell
$ bitxhub client governance proposal query -h

NAME:
   BitXHub client governance proposal query - Query proposals based on the condition

USAGE:
   BitXHub client governance proposal query [command options] [arguments...]

OPTIONS:
   --id value      Specify proposal id
   --type value    Specify proposal type, currently only appchain_mgr, rule_mgr, node_mgr, service_mgr, role_mgr, proposal_strategy_mgr and dapp_mgr are supported
   --status value  Specify proposal status, one of proposed, paused, approve or reject
   --from value    Specify the address of the account to which the proposal was made
   --objId value   Specify the ID of the managed object
```

**参数解释**：

- `--id`: 提案id；
- `--type`: 提案类型，当前支持应用链管理、规则管理、节点管理、角色管理；
- `--status`: 提案状态，是否通过；
- `--from`: 提案发起者地址；
- `--objId`: 提案管理对象id，如应用链id。

**示例**：

```shell
$ bitxhub client governance proposal query --id 0x134213199d40c4b26cB8B0E5D53d71E309223403-0 --type appchain_mgr --status proposed --from 0x5F2e7B52DCf85022b0B97003BAa20a9214125D0D --objId appchain1

========================================================================================
Id                                            ManagedObjectId  Type          EventType  Status    A/R  IE/AE  Special/Super  StrategyExp  CreateTime           Reason  EndReason  extra
--                                            ---------------  ----          ---------  ------    ---  -----  -------------  -----------  ----------           ------  ---------  -----
0x134213199d40c4b26cB8B0E5D53d71E309223403-0  appchain1        appchain_mgr  register   proposed  1/0  4/4    false/true     a > 0.5 * t  1655775371509223000  reason             {"chain_info":{"id":"appchain1","chain_name":"test","chain_type":"ETH","trust_root":"MHgwMDBmMWE3YTA4Y2NjNDhlNWQzMGY4MDg1MGNmMWNmMjgzYWEzYWJkLDB4ZTkzYjkyZjFkYTA4ZjkyNWJkZWU0NGU5MWU3NzY4MzgwYWU4MzMwNywweGIxOGM4NTc1ZTMyODRlNzliOTIxMDAwMjVhMzEzNzhmZWI4MTAwZDYsMHg4NTZFMkI5QTVGQTgyRkQxQjAzMUQxRkY2ODYzODY0REJBQzc5OTVE","broker":"MHg4NTcxMzNjNUM2OWU2Q2U2NkY3QUQ0NkYyMDBCOUIzNTczZTc3NTgy","desc":"desc","version":0,"did":"did:bitxhub:appchain1:.","status":"available","fsm":null},"master_rule":{"address":"0x00000000000000000000000000000000000000a2","rule_url":"http://github.com","chain_id":"","master":true,"builtIn":false,"create_time":0,"status":"available","fsm":null},"admin_addrs":"0x134213199d40c4b26cB8B0E5D53d71E309223403"}
========================================================================================
* A/R：approve num / reject num
* IE/AE/TE：the total number of electorate at the time of the initial proposal / the number of available electorate currently
* Special/Super：is special proposal / is super admin voted
```

##### 撤回提案

`bitxhub client governance proposal withdraw`命令用于提案撤回。

```shell
$ bitxhub client governance proposal withdraw -h

NAME:
   BitXHub client governance proposal withdraw - Withdraw a proposal

USAGE:
   BitXHub client governance proposal withdraw [command options] [arguments...]

OPTIONS:
   --id value      Specify proposal id
   --reason value  Specify withdraw reason
```

**参数解释**：

- `--id`: 提案id；
- `--reason`: 提案撤回理由。

**示例**：

```shell
# 只有发起提案的节点才有权撤回提案，--repo需指定该节点的配置文件目录
$ NODE_CONFIG_PATH="xxx/xxx" # 发起提案节点配置文件的路径
$ bitxhub --repo $NODE_CONFIG_PATH client governance proposal withdraw --id 0x134213199d40c4b26cB8B0E5D53d71E309223403-0 --reason "test"

withdraw proposal successfully!
```

#### 7.10.3. 管理应用链

`bitxhub client governance appchain`命令用于应用链管理。

```shell
$ bitxhub client governance appchain -h

NAME:
   BitXHub client governance appchain - Appchain manage command

USAGE:
   BitXHub client governance appchain command [command options] [arguments...]

COMMANDS:
   info      Query appchain info by appchain name
   status    Query chain status by appchain id
   freeze    Freeze appchain by appchain id
   activate  Activate appchain by appchain id

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询应用链信息

`bitxhub client governance appchain info`命令用于查询应用链信息。

```shell
$ bitxhub client governance appchain info -h

NAME:
   BitXHub client governance appchain info - Query appchain info by appchain name

USAGE:
   BitXHub client governance appchain info [command options] [arguments...]

OPTIONS:
   --name value  Specify appchain name
```

**参数解释**：

- `--name`: 应用链名称。

**示例**：

```shell
# 需要先注册应用链
$ bitxhub client governance appchain info --name test

Id         Name  DID                      Type  Broker                                      Status     Desc  Version
--         ----  ---                      ----  ------                                      ------     ----  -------
appchain1  test  did:bitxhub:appchain1:.  ETH   0x857133c5C69e6Ce66F7AD46F200B9B3573e77582  available  desc  0
```

##### 查询应用链状态

`bitxhub client governance appchain status`命令用于查询应用链状态。

```shell
$ bitxhub client governance appchain status -h

NAME:
   BitXHub client governance appchain status - Query chain status by appchain id

USAGE:
   BitXHub client governance appchain status [command options] [arguments...]

OPTIONS:
   --id value  Specify appchain id
```

**参数解释**：

- `--id`: 应用链id。

**示例**：

```shell
$ bitxhub client governance appchain status --id appchain1

appchain appchain1 is available
```

##### 冻结应用链

`bitxhub client governance appchain freeze`命令用于冻结应用链。

```shell
$ bitxhub client governance appchain freeze -h

NAME:
   BitXHub client governance appchain freeze - Freeze appchain by appchain id

USAGE:
   BitXHub client governance appchain freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify appchain id
   --reason value  Specify freeze reason
```

**参数解释**：

- `--id`: 应用链id；
- `--reason`: 冻结应用链理由。

**示例**：

```shell
# 冻结应用链的提案需要管理员节点才有权发起
# 管理员对提案投票通过（超半数投同意票）后，应用链状态由freezing转换为frozen
$ BITXHUB_HOME="xxx/bitxhub" # bitxhub项目路径
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance appchain freeze --id appchain1 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
```

##### 激活应用链

`bitxhub client governance appchain activate`命令用于激活应用链。

```shell
$ bitxhub client governance appchain activate -h

NAME:
   BitXHub client governance appchain activate - Activate appchain by appchain id

USAGE:
   BitXHub client governance appchain activate [command options] [arguments...]

OPTIONS:
   --id value      Specify appchain id
   --reason value  Specify activate reason
```

**参数解释**：

- `--id`: 应用链id；
- `--reason`: 激活应用链理由。

**示例**：

```shell
# 管理员节点才有权激活
# 管理员对提案投票通过（超半数同意票）后，应用链状态由frozen转换为available
$ BITXHUB_HOME="xxx/bitxhub" # bitxhub项目路径
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance appchain activate --id appchain1 reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
```
#### 7.10.4. Interchain信息查询

`bitxhub client governance interchain` 命令用于查询Interchain信息。

```shell
$ bitxhub client governance interchain -h

NAME:
   BitXHub client governance interchain - Interchain manage command

USAGE:
   BitXHub client governance interchain command [command options] [arguments...]

COMMANDS:
   counter  Query interchain counter by full ibtp id
   tx       Query tx hash by full ibtp id

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询counter信息

`bitxhub client governance interchain counter`命令用于通过IBTP id查询交易的counter信息。

```shell
$ bitxhub client governance interchain counter -h

NAME:
   BitXHub client governance interchain counter - Query interchain counter by full ibtp id

USAGE:
   BitXHub client governance interchain counter [command options] [arguments...]

OPTIONS:
   --id value  Specify full ibtp id
```

**参数解释**：

- `--id`: IBTP的完整id。

**示例**

```shell
$ bitxhub --repo=/Users/xxx/github.com/meshplus/bitxhub/scripts/build/node1 client governance interchain counter --id 1356:eth1:0x5AE7C57eC31AD0c391cCA6d64127C714d6d39Ba3

{
  "ID": "1356:eth1:0x5AE7C57eC31AD0c391cCA6d64127C714d6d39Ba3",
  "InterchainCounter": {
    "1356:eth2:0x1f552e0d2939BfDd29a40c6Ad3785801A3b7683F": 49,
    "1356:eth2:0xea375610055446aEBacA9173b764048D4Bb8B7D8": 49
  },
  "ReceiptCounter": {
    "1356:eth2:0x1f552e0d2939BfDd29a40c6Ad3785801A3b7683F": 49,
    "1356:eth2:0xea375610055446aEBacA9173b764048D4Bb8B7D8": 49
  },
  "SourceInterchainCounter": {},
  "SourceReceiptCounter": {}
}
```

##### 查询Interchain的交易Hash

`bitxhub client governance interchain tx`命令用于查询interchain交易的hash。

```shell
$ bitxhub client governance interchain tx -h

NAME:
   BitXHub client governance interchain tx - Query tx hash by full ibtp id

USAGE:
   BitXHub client governance interchain tx [command options] [arguments...]

OPTIONS:
   --id value  Specify full ibtp id
   --is_req    Specify interchain or receipt
```

**参数解释**：

- `--id`: IBTP的完整id。
- `is_req`: 用于区别查询interchain还是receipt类型，不加该option则默认为查询receipt，反之则查询interchain。

**示例**

```shell
$ bitxhub --repo=/Users/xxx/github.com/meshplus/bitxhub/scripts/build/node1 client governance interchain tx --id 1356:eth1:0x5AE7C57eC31AD0c391cCA6d64127C714d6d39Ba3-1356:eth2:0x1f552e0d2939BfDd29a40c6Ad3785801A3b7683F-10

0x3168cF3eEB8fa74c59bb168b1D299B4ee5Cf192276e62c27711c76E7965b9DE7

$ bitxhub --repo=/Users/xxx/github.com/meshplus/bitxhub/scripts/build/node1 client governance interchain tx --id 1356:eth1:0x5AE7C57eC31AD0c391cCA6d64127C714d6d39Ba3-1356:eth2:0x1f552e0d2939BfDd29a40c6Ad3785801A3b7683F-10 --is_req

0x7BF0EB037eAEE04D4bA8C6635e17D330C57E98e622BbaFFb4a9205ba22f87FC4
```

#### 7.10.5. 管理验证规则

`bitxhub client governance rule`命令用于验证规则管理。

```shell
$ bitxhub client governance rule -h

NAME:
   BitXHub client governance rule - Rule manage command

USAGE:
   BitXHub client governance rule command [command options] [arguments...]

COMMANDS:
   all     Query all rules info of one appchain
   master  Query master rule address of one appchain
   status  Query rule status by rule address and appchain id

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询所有验证规则

`bitxhub client governance rule all`命令用于查询某条应用链所有的验证规则。

```shell
$ bitxhub client governance rule all -h

NAME:
   BitXHub client governance rule all - Query all rules info of one appchain

USAGE:
   BitXHub client governance rule all [command options] [arguments...]

OPTIONS:
   --id value  Specify appchain id
```

**参数解释**：

- `--id`: 应用链id。

**示例**：

```shell
$ bitxhub client governance rule all --id appchain1

ChainID    RuleAddress                                 Status     Master  CreateTime
-------    -----------                                 ------     ------  ----------
appchain1  0x00000000000000000000000000000000000000a2  available  true    1655777266993908000
```

##### 查询主验证规则

`bitxhub client governance rule master`命令用于查询某条应用链的主验证规则。

```shell
$ bitxhub client governance rule master -h

NAME:
   BitXHub client governance rule master - Query master rule address of one appchain

USAGE:
   BitXHub client governance rule master [command options] [arguments...]

OPTIONS:
   --id value  Specify appchain id
```

**参数解释**：

- `--id`: 应用链id。

**示例**：

```shell
$ bitxhub client governance rule master --id appchain1

available rule address is 0x00000000000000000000000000000000000000a2
```

##### 查询验证规则状态

`bitxhub client governance rule status`命令用于查询某条应用链的某条验证规则的状态。

```shell
$ bitxhub client governance rule status -h

NAME:
   BitXHub client governance rule status - Query rule status by rule address and appchain id

USAGE:
   BitXHub client governance rule status [command options] [arguments...]

OPTIONS:
   --id value    Specify appchain id
   --addr value  Specify rule addr
```

**参数解释**：

- `--id`: 应用链id；
- `--addr`: 验证规则地址。

**示例**：

```shell
$ bitxhub client governance rule status --id appchain1 --addr 0x00000000000000000000000000000000000000a2

the rule 0x00000000000000000000000000000000000000a2 is available
```

#### 7.10.6. 管理节点

`bitxhub client governance node`命令用于节点管理。

```shell
$ bitxhub client governance node -h

NAME:
   BitXHub client governance node - Node manage command

USAGE:
   BitXHub client governance node command [command options] [arguments...]

COMMANDS:
   all       Query all nodes info
   status    Query node status by node account
   register  Register node
   update    Update node info
   logout    Logout node by node account

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询节点状态

`bitxhub client governance node status`命令用于查询节点状态。

```shell
$ bitxhub client governance node status -h

NAME:
   BitXHub client governance node status - Query node status by node account

USAGE:
   BitXHub client governance node status [command options] [arguments...]

OPTIONS:
   --account value  Specify node account
```

**参数解释**：

- `--account`: 节点账户地址。

**示例**：

```shell
$ bitxhub client governance node status --account 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013

node QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL is available
```

##### 注册节点

`bitxhub client governance node register`命令用于注册节点。

```shell
$ bitxhub client governance node register -h

NAME:
   BitXHub client governance node register - Register node

USAGE:
   BitXHub client governance node register [command options] [arguments...]

OPTIONS:
   --account value     Specify node account
   --type value        Specify node type (vpNode or nvpNode) (default: "vpNode")
   --pid value         Specify vp node pid, only useful for vpNode
   --id value          Specify vp node id, only useful for vpNode (default: 0)
   --name value        Specify nvp node name, only useful for nvpNode
   --permission value  Specify nvp node permission, only useful for nvpNode, multiple appchain addresses are separated by commas
   --reason value      Specify register reason
```

**参数解释**：

- `--account`: 节点账户地址；
- `--type`: 节点类型，共识节点或者审计节点，默认是共识节点；
- `--pid`: 共识节点pid；
- `--id`: 共识节点id；
- `--name`: 审计节点名称；
- `--permission`: 审计节点权限；
- `--reason`: 注册节点理由。

**示例**：

```shell
# 管理员节点才有权注册
# 管理员对提案投票通过（超半数同意票）后才算注册成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance node register --account 0x5F2e7B52DCf85022b0B97003BAa20a9214125D0D --type nvpNode --name audit0 --permission appchain1 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
```

##### 更新节点信息

`bitxhub client governance node update`命令用于更新审计节点相关信息。

```shell
$ bitxhub client governance node update -h

NAME:
   BitXHub client governance node update - Update node info

USAGE:
   BitXHub client governance node update [command options] [arguments...]

OPTIONS:
   --account value     Specify node account
   --name value        Specify nvp node name, only useful for nvpNode
   --permission value  Specify nvp node permission, only useful for nvpNode, multiple appchain addresses are separated by commas
   --reason value      Specify update reason
```

**参数解释**：

- `--account`: 节点账户地址；
- `--name`: 审计节点名称；
- `--permission`：更新后审计节点权限；
- `--reason`: 更新节点信息理由。

**示例**：

```shell
# 管理员节点才有权更新
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance node update --account 0x5F2e7B52DCf85022b0B97003BAa20a9214125D0D --name audit1 --permission appchain1 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
```

##### 注销节点

`bitxhub client governance node logout`命令用于注销节点。

```shell
$ bitxhub client governance node logout -h

NAME:
   BitXHub client governance node logout - Logout node by node account

USAGE:
   BitXHub client governance node logout [command options] [arguments...]

OPTIONS:
   --account value  Specify node account
   --reason value   Specify logout reason
```

**参数解释**：

- `--account`: 节点账户地址；
- `--reason`: 注销节点理由。

**示例**：

```shell
# 管理员节点才有权注销
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance node logout --account 0x5F2e7B52DCf85022b0B97003BAa20a9214125D0D --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-4
```

##### 查询所有节点

`bitxhub client governance node all`命令用于查询所有节点信息。

```shell
$ bitxhub client governance node all -h

NAME:
   BitXHub client governance node all - Query all nodes info

USAGE:
   BitXHub client governance node all [arguments...]
```

**示例**：

```shell
$ bitxhub client governance node all

Account                                     Type     Pid                                             VpNodeId  Name    Permission  Status     AuditAdminAddr
-------                                     ----     ---                                             --------  ----    ----------  ------     --------------
0x5F2e7B52DCf85022b0B97003BAa20a9214125D0D  nvpNode                                                  0         audit0  appchain1   logouting
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  vpNode   QmbmD1kzdsxRiawxu7bRrteDgW1ituXupR8GH6E2EUAHY4  2                             available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  vpNode   QmQUcDYCtqbpn5Nhaw4FAGxQaSSNvdWfAFcpQT9SPiezbS  3                             available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  vpNode   QmQW3bFn8XX1t4W14Pmn37bPJUpUVBrBjnPuBZwPog3Qdy  4                             available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  vpNode   QmXi58fp9ZczF3Z5iz1yXAez3Hy5NYo1R8STHWKEM9XnTL  1                             available
```

#### 7.10.7. 管理角色

`bitxhub client governance role`命令用于角色管理。

```shell
$ bitxhub client governance role -h

NAME:
   BitXHub client governance role - Role manage command

USAGE:
   BitXHub client governance role command [command options] [arguments...]

COMMANDS:
   all       Query all roles info
   status    Query role status by role id
   register  Register role
   freeze    Freeze role by role id
   activate  Activate role by role id
   logout    Logout role by role id
   bind      Bind audit role with node

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询所有角色

`bitxhub client governance role all`命令用于查询所有角色信息。

```shell
$ bitxhub client governance role all -h

NAME:
   BitXHub client governance role all - Query all roles info

USAGE:
   BitXHub client governance role all [arguments...]
```

**示例**：

```shell
$ bitxhub client governance role all

RoleId                                      type                  Status     NodeAccount  AppchainID
------                                      ----                  ------     -----------  ----------
0x134213199d40c4b26cB8B0E5D53d71E309223403  appchainAdmin         available               appchain1
0x79a1215469FaB6f9c63c1816b45183AD3624bE34  superGovernanceAdmin  available
0x97c8B516D19edBf575D72a172Af7F418BE498C37  superGovernanceAdmin  available
0xc0Ff2e0b3189132D815b8eb325bE17285AC898f8  superGovernanceAdmin  available
0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013  superGovernanceAdmin  available
```

##### 查询角色状态

`bitxhub client governance role status`命令用于查询角色状态。

```shell
$ bitxhub client governance role status -h

NAME:
   BitXHub client governance role status - Query role status by role id

USAGE:
   BitXHub client governance role status [command options] [arguments...]

OPTIONS:
   --id value  Specify role id(address)
```

**参数解释**：

- `--id`: 角色id。

**示例**：

```shell
$ bitxhub client governance role status --id 0x79a1215469FaB6f9c63c1816b45183AD3624bE34

role 0x79a1215469FaB6f9c63c1816b45183AD3624bE34 is available
```

##### 注册角色

`bitxhub client governance role register`命令用于注册角色。

```shell
$ bitxhub client governance role register -h

NAME:
   BitXHub client governance role register - Register role

USAGE:
   BitXHub client governance role register [command options] [arguments...]

OPTIONS:
   --address value      Specify role address(id)
   --type value         Specify role type, one of governanceAdmin or auditAdmin (default: "governanceAdmin")
   --nodeAccount value  Specify node account for auditAdmin, only useful for auditAdmin
   --reason value       Specify register reason
```

**参数解释**：

- `--address`: 角色地址；
- `--type`: 角色类型，治理管理员或者审计管理员，默认治理管理员；
- `--nodeAccount`: 审计管理员对应审计节点地址；
- `--reason`: 注册角色理由。

**示例**：

```shell
# 管理员节点才有权利注册
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance role register --address 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --type governanceAdmin --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
```

##### 冻结角色

`bitxhub client governance role freeze`命令用于冻结角色。

```shell
$ bitxhub client governance role freeze -h

NAME:
   BitXHub client governance role freeze - Freeze role by role id

USAGE:
   BitXHub client governance role freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify role id
   --reason value  Specify freeze reason
```

**参数解释**：

- `--id`: 角色id；
- `--reason`: 冻结角色理由。

**示例**：

```shell
# 管理员节点才有权利冻结
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance role freeze --id 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
```

##### 激活角色

`bitxhub client governance role activate`命令用于激活角色。

```shell
$ bitxhub client governance role activate -h

NAME:
   BitXHub client governance role activate - Activate role by role id

USAGE:
   BitXHub client governance role activate [command options] [arguments...]

OPTIONS:
   --id value      Specify role id
   --reason value  Specify activate reason
```

**参数解释**：

- `--id`: 角色id；
- `--reason`: 激活角色理由。

**示例**：

```shell
# 管理员节点才有权利激活
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance role activate --id 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
```

##### 注销角色

`bitxhub client governance role logout`命令用于注销角色。

```shell
$ bitxhub client governance role logout -h

NAME:
   BitXHub client governance role logout - Logout role by role id

USAGE:
   BitXHub client governance role logout [command options] [arguments...]

OPTIONS:
   --id value      Specify role id
   --reason value  Specify logout reason
```

**参数解释**：

- `--id`: 角色id；
- `--reason`: 注销角色理由。

**示例**：

```shell
# 管理员节点才有权利注销
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance role logout --id 0xb1714Cda9364a9b70a22fe664ba398314b173ae7 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
```

##### 绑定角色

`bitxhub client governance role bind`命令用于审计管理员与审计节点绑定，当审计节点注销后，与之绑定的审计管理员会变为冻结状态，审计管理员需要重新与审计节点绑定才能恢复正常状态。

```shell
$ bitxhub client governance role bind -h

NAME:
   BitXHub client governance role bind - Bind audit role with node

USAGE:
   BitXHub client governance role bind [command options] [arguments...]

OPTIONS:
   --id value       Specify role id
   --account value  Specify node account
   --reason value   Specify bind reason
```

**参数解释**：

- `--id`: 角色id；
- `--account`: 审计节点地址；
- `--reason`: 审计管理员绑定审计节点理由。

**示例**：

```shell
# 管理员节点才有权利绑定
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/scripts/build/node1 client governance role bind --id 0x25418CfDdc3453B394b2c000e14141dA80606953 --account 0x29366700f2E3963646D61Cd66d0d38Fd4Db1e664 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-8
```

#### 7.10.8. 管理dapp

`bitxhub client governance dapp`命令用于Dapp管理。

```shell
$ bitxhub client governance dapp -h

NAME:
   BitXHub client governance dapp - Dapp manage command

USAGE:
   BitXHub client governance dapp command [command options] [arguments...]

COMMANDS:
   all         Query all dapps
   permission  Query permission dapps
   status      Query dapp status by dapp id
   myDapps     Query dapps by owner addr
   register    Register dapp
   update      Update dapp info
   freeze      Freeze dapp by dapp id
   activate    Activate dapp by dapp id
   transfer    Transfer dapp to other user
   confirm     Confirm dapp transfer
   evaluate    Evaluate dapp

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询所有dapp

`bitxhub client governance dapp all`命令用于查询所有dapp。

```shell
$ bitxhub client governance dapp all -h

NAME:
   BitXHub client governance dapp all - Query all dapps

USAGE:
   BitXHub client governance dapp all [arguments...]
```

**示例**：

```shell
$ bitxhub client governance dapp all

========================================================================================
Id                                            Name          Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----          ----  -----                                       ----------           -----  ------     -------  ------
0x66A442010FD68A57bb2e835A869b23C3220981F0-0  dapp_example  tool  0x66A442010FD68A57bb2e835A869b23C3220981F0  1642124785975579000  0      available
```

##### 查询dapp权限列表

`bitxhub client governance dapp permission`命令用于查询dapp权限列表，返回某地址所有能够使用的dapp信息。

```shell
$ bitxhub client governance dapp permission -h

NAME:
   BitXHub client governance dapp permission - Query permission dapps

USAGE:
   BitXHub client governance dapp permission [command options] [arguments...]

OPTIONS:
   --caller value  Specify caller addr
```

**参数解释**：

- `--caller`: 调用者地址。

**示例**：

```shell
$ bitxhub client governance dapp permission --caller 0x66A442010FD68A57bb2e835A869b23C3220981F0

========================================================================================
Id                                            Name          Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----          ----  -----                                       ----------           -----  ------     -------  ------
0x66A442010FD68A57bb2e835A869b23C3220981F0-0  dapp_example  tool  0x66A442010FD68A57bb2e835A869b23C3220981F0  1642124785975579000  0      availabl
```

##### 查询dapp状态

`bitxhub client governance dapp status`命令用于查询某个dapp状态。

```shell
$ bitxhub client governance dapp status -h

NAME:
   BitXHub client governance dapp status - Query dapp status by dapp id

USAGE:
   BitXHub client governance dapp status [command options] [arguments...]

OPTIONS:
   --id value  Specify dapp id
```

**参数解释**：

- `--id`: Dapp id。

**示例**：

```shell
$ bitxhub client governance dapp status --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0

dapp 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 is available
```

##### 查询特定用户的dapp

`bitxhub client governance dapp myDapps`命令用于查询某个用户的所有dapp。

```shell
$ bitxhub client governance dapp myDapps -h

NAME:
   BitXHub client governance dapp myDapps - Query dapps by owner addr

USAGE:
   BitXHub client governance dapp myDapps [command options] [arguments...]

OPTIONS:
   --addr value  Specify user addr
```

**参数解释**：

- `--addr`: 用户地址

**示例**：

```shell
$ bitxhub client governance dapp myDapps --addr 0x66A442010FD68A57bb2e835A869b23C3220981F0

========================================================================================
Id                                            Name          Type  Owner                                       Createtime           Score  Status     TranRec  EvaRec
--                                            ----          ----  -----                                       ----------           -----  ------     -------  ------
0x66A442010FD68A57bb2e835A869b23C3220981F0-0  dapp_example  tool  0x66A442010FD68A57bb2e835A869b23C3220981F0  1642124785975579000  0      available
```

##### 注册dapp

`bitxhub client governance dapp register`命令用于注册dapp。

```shell
$ bitxhub client governance dapp register -h

NAME:
   BitXHub client governance dapp register - Register dapp

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

**参数解释**：

- `--name`: dapp名称；
- `--type`: dapp类型，工具类、应用类、游戏类或者其他类；
- `--desc`: dapp描述；
- `--url`: dapp网址；
- `--contractAddrs`: dapp合约地址，需要在BitXHub中部署；
- `--permission`: dapp使用者地址黑名单；
- `--reason`: 注册dapp理由。

**示例**：

```shell
# --repo 需指定注册该DAPP的节点的配置文件目录
# 管理员对提案投票通过（超半数同意票）后才算成功
$ NODE_CONFIG_PATH="xxx/xxx"
$ bitxhub --repo $NODE_CONFIG_PATH client governance dapp register --name dapp_example --type tool --desc "a test tool" --url https://github.com/meshplus/bitxhub --contractAddrs 0x49cc3DA4DADb222B6bFBdD5B2b148472B67Aa409 --reason test

proposal id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-0, dapp id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-0
```

##### 更新dapp信息

`bixthub client governance dapp update`命令用于更新dapp信息。

```shell
$ bitxhub client governance dapp update -h

NAME:
   BitXHub client governance dapp update - Update dapp info

USAGE:
   BitXHub client governance dapp update [command options] [arguments...]

OPTIONS:
   --id value             Specify dapp id
   --name value           Specify dapp name
   --desc value           Specify dapp description
   --url value            Specify dapp url
   --contractAddrs value  Specify dapp contract addr. If there are multiple contract addresses, separate them with ','
   --permission value     Specify the addr of users which are not allowed to see the dapp. If there are multiple contract addresses, separate them with ','
   --reason value         Specify update reason
```

**参数解释**：

- `--id`: dapp id；
- `--name`: 更新后dapp名称；
- `--desc`: 更新后dapp描述；
- `--url`: 更新后dapp网址；
- `--contractAddrs`: 更新后dapp合约地址；
- `--permission`: 更新后dapp使用黑名单；
- `--reason`: 更新dapp信息理由。

**示例**：

```shell
# --repo 需指定注册该DAPP的节点的配置文件目录
# 管理员对提案投票通过（超半数同意票）后才算成功
$ NODE_CONFIG_PATH="xxx/xxx"
$ bitxhub --repo $NODE_CONFIG_PATH client governance dapp update --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --name dappUpdate --desc "dapp update" --reason test

proposal id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-1
```

##### 冻结dapp

`bitxhub client governance dapp freeze`命令用于冻结dapp。

```shell
$ bitxhub client governance dapp freeze -h

NAME:
   BitXHub client governance dapp freeze - Freeze dapp by dapp id

USAGE:
   BitXHub client governance dapp freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --reason value  Specify freeze reason
```

**参数解释**：

- `--id`: dapp id；
- `--reason`: 冻结dapp理由。

**示例**：

```shell
# 管理员节点才有权利冻结
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/script/build/node1 client governance dapp freeze --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-0
```

##### 激活dapp

`bitxhub client governance dapp activate`命令用于激活dapp。

```shell
$ bitxhub client governance dapp activate -h

NAME:
   BitXHub client governance dapp activate - Activate dapp by dapp id

USAGE:
   BitXHub client governance dapp activate [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --reason value  Specify activate reason
```

**参数解释**：

- `--id`: dapp id；
- `--reason`: 激活dapp理由。

**示例**：

```shell
# 管理员节点才有权利冻结
# 管理员对提案投票通过（超半数同意票）后才算成功
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/script/build/node1 client governance dapp activate --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-1
```

##### 转让dapp

`bitxhub client governance dapp transfer`命令用于将某dapp所有权转给其他用户。

```shell
$ bitxhub client governance dapp transfer -h

NAME:
   BitXHub client governance dapp transfer - Transfer dapp to other user

USAGE:
   BitXHub client governance dapp transfer [command options] [arguments...]

OPTIONS:
   --id value      Specify dapp id
   --addr value    Specify new owner addr
   --reason value  Specify transfer reason
```

**参数解释**：

- `--id`: dapp id；
- `--addr`: 新所有者地址；
- `--reason`: 转让dapp所有权理由。

**示例**：

```shell
# 注册DAPP的节点才有权转让
$ NODE_CONFIG_PATH="xxx/xxx" # 节点路径
$ bitxhub --repo $NODE_CONFIG_PATH client governance dapp transfer --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --addr 0x29366700f2E3963646D61Cd66d0d38Fd4Db1e664 --reason test

proposal id is 0x66A442010FD68A57bb2e835A869b23C3220981F0-2
```

##### 确认转让

`bitxhub client governance dapp confirm`命令用于被转让人确认dapp转让。

```shell
$ bitxhub client governance dapp confirm -h

NAME:
   BitXHub client governance dapp confirm - Confirm dapp transfer

USAGE:
   BitXHub client governance dapp confirm [command options] [arguments...]

OPTIONS:
   --id value  Specify dapp id
```

**参数解释**：

- `--id`: dapp id。

**示例**：

```shell
# 被转发人确定转让
$ NODE_CONFIG_PATH="xxx/xxx" # 被转让节点的配置文件目录
$ bitxhub --repo $NODE_CONFIG_PATH client governance dapp confirm --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0

confirm dapp transfer success
```

##### 评价dapp

`bitxhub client governance dapp evaluate`命令用于评价某dapp。

```shell
$ bitxhub client governance dapp evaluate -h

NAME:
   BitXHub client governance dapp evaluate - Evaluate dapp

USAGE:
   BitXHub client governance dapp evaluate [command options] [arguments...]

OPTIONS:
   --id value     Specify dapp id
   --desc value   Specify evaluate desc
   --score value  Specify evaluate score, [0,5] (default: 0)
```

**参数解释**：

- `--id`: dapp id；
- `--desc`: 评价内容；
- `--score`: 评分。

**示例**：

```shell
$ NODE_CONFIG_PATH="xxx/xxx" # 评论节点的配置文件目录
$ bitxhub --repo $NODE_CONFIG_PATH client governance dapp evaluate --id 0x66A442010FD68A57bb2e835A869b23C3220981F0-0 --desc "good" --score 5

evaluate dapp success
```

#### 7.10.9. 管理服务

`bitxhub client governance service`命令用于服务管理。

```shell
$ bitxhub client governance service -h

NAME:
   BitXHub client governance service - Service manage command

USAGE:
   BitXHub client governance service command [command options] [arguments...]

COMMANDS:
   status       Query service status by chainService id
   appServices  Query services by appchain id
   freeze       Freeze service by chainService id
   activate     Activate service by chainService id
   evaluate     Evaluate service

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询服务状态

`bitxhub client governance service status`命令用于查询服务状态。

```shell
$ bitxhub client governance service status -h

NAME:
   BitXHub client governance service status - Query service status by chainService id

USAGE:
   BitXHub client governance service status [command options] [arguments...]

OPTIONS:
   --id value  Specify chainService id
```

**参数解释**：

- `--id`: 服务id。

**示例**：

```shell
$ bitxhub client governance service status --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13

service chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 is available
```

##### 查询特定应用链的服务

`bitxhub client governance service appServices`命令用于查询某条应用链所有服务的信息。

```shell
$ bitxhub client governance service appServices -h

NAME:
   BitXHub client governance service appServices - Query services by appchain id

USAGE:
   BitXHub client governance service appServices [command options] [arguments...]

OPTIONS:
   --chainID value  Specify appchain id
```

**参数解释**：

- `--chainID`: 应用链id。

**示例**：

```shell
$ bitxhub client governance service appServices --chainID chain0

========================================================================================
ChainID  ServiceID                                   Name  Type          Intro  Ordered  Createtime           Score  Status
-------  ---------                                   ----  ----          -----  -------  ----------           -----  ------
chain0   0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13  d0    CallContract  i      true     1642129992547460000  0      available
```

##### 冻结服务

`bitxhub client governance service freeze`命令用于冻结服务。

```shell
$ bitxhub client governance service freeze -h

NAME:
   BitXHub client governance service freeze - Freeze service by chainService id

USAGE:
   BitXHub client governance service freeze [command options] [arguments...]

OPTIONS:
   --id value      Specify chainService id
   --reason value  Specify freeze reason
```

**参数解释**：

- `--id`: 服务id；
- `--reason`: 冻结服务理由。

**示例**：

```shell
# 管理员才有权冻结
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/script/build/node1 client governance service freeze --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-2
```

##### 激活服务

`bitxhub client governance service activate`命令用于激活服务。

```shell
$ bitxhub client governance service activate -h

NAME:
   BitXHub client governance service activate - Activate service by chainService id

USAGE:
   BitXHub client governance service activate [command options] [arguments...]

OPTIONS:
   --id value      Specify chainService id
   --reason value  Specify activate reason
```

**参数解释**：

- `--id`: 服务id；
- `--reason`: 激活服务理由。

**示例**：

```shell
# 管理员才有权冻结
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/script/build/node1 client governance service activate --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-3
```

##### 评价服务

`bitxhub client governance service evaluate`命令用于评价服务。

```shell
$ bitxhub client governance service evaluate -h

NAME:
   BitXHub client governance service evaluate - Evaluate service

USAGE:
   BitXHub client governance service evaluate [command options] [arguments...]

OPTIONS:
   --id value     Specify service id
   --desc value   Specify evaluate desc
   --score value  Specify evaluate score, [0,5] (default: 0)
```

**参数解释**：

- `--id`: 服务id；
- `--desc`: 评价内容；
- `--score`: 评分。

**示例**：

```shell
$ NODE_CONFIG_PATH="xxx/xxx" # 评论节点配置文件路径
$ bitxhub --repo $NODE_CONFIG_PATH client governance service evaluate --id chain0:0x6DCB3337cd4Ec41d88E62A96123bF3a4E06A7e13 --desc "good" --score 5

evaluate service success
```

#### 7.10.10. 管理投票策略

`bitxhub client governance strategy`命令用于投票策略管理。

```shell
$ bitxhub client governance strategy -h

NAME:
   BitXHub client governance strategy - Proposal strategy command

USAGE:
   BitXHub client governance strategy command [command options] [arguments...]

COMMANDS:
   all     Query all proposal strategy
   update  Update proposal strategy

OPTIONS:
   --help, -h  show help
```

**子命令**：

##### 查询所有投票策略

`bitxhub client governance strategy all`命令用于查询所有模块投票策略。

```shell
$ bitxhub client governance strategy all -h

NAME:
   BitXHub client governance strategy all - Query all proposal strategy

USAGE:
   BitXHub client governance strategy all [arguments...]
```

**示例**：

```shell
$ bitxhub client governance strategy all

module                 strategy        Extra        Status
------                 --------        -----        ------
appchain_mgr           SimpleMajority  a > 0.5 * t  available
node_mgr               SimpleMajority  a > 0.5 * t  available
dapp_mgr               SimpleMajority  a > 0.5 * t  available
role_mgr               SimpleMajority  a > 0.5 * t  available
rule_mgr               SimpleMajority  a > 0.5 * t  available
service_mgr            SimpleMajority  a > 0.5 * t  available
proposal_strategy_mgr  SimpleMajority  a > 0.5 * t  available
```

##### 更新投票策略

`bitxhub client governance strategy update`命令用于更新某模块投票策略。

```shell
$ bitxhub client governance strategy update -h

NAME:
   BitXHub client governance strategy update - Update proposal strategy

USAGE:
   BitXHub client governance strategy update [command options] [arguments...]

OPTIONS:
   --module value  Specify module name(appchain_mgr, rule_mgr, node_mgr, service_mgr, role_mgr, proposal_strategy_mgr, dapp_mgr, all_mgr)
   --typ value     Specify proposal strategy(SimpleMajority or ZeroPermission) (default: "SimpleMajority")
   --extra value   Specify expression of strategy. In this expression, 'a' represents the number of people approve, 'r' represents the number of people against, and 't' represents the total number of people who can vote. (default: "a > 0.5 * t")
   --reason value  Specify Update reason
```

**参数解释**：

- `--module`: 模块名称，应用链管理、规则管理、节点管理、服务管理、角色管理、提案管理、投票策略管理、dapp管理（all_mgr用于更新全部模块）；
- `--typ`: 投票策略类型，目前支持一票通过制和简单多数制；
- `--extra`: 投票通过策略；
- `--reason`: 更新投票策略理由。

**示例**：

```shell
# 管理员才有权限更新
$ BITXHUB_HOME="xxx/bitxhub"
$ bitxhub --repo $BITXHUB_HOME/script/build/node1 client governance strategy update --module appchain_mgr --reason test

proposal id is 0xc7F999b83Af6DF9e67d0a37Ee7e900bF38b3D013-4
```
