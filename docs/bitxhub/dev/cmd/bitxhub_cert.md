
# bitxhub cert
## 功能

该命令`bitxhub cert`提供一系列x509标准的证书工具，服务于中继链节点的准入机制。
它包含了CA生成、CSR描述生成、证书签发、证书解析、证书私钥生成以及证书校验。
子命令信息如下:
```
bitxhub --repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node3 cert                             │-repo=/Users/windiyi/IdeaProjects/bitxhub/scripts/build/node4 start                               

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

## 子命令
### bitxhub cert ca
生成根CA证书
```bigquery
bitxhub cert ca -h
NAME:
   BitXHub cert ca - Generate ca cert and private key

USAGE:
   BitXHub cert ca [arguments...]


```
### bitxhub cert csr
- `生成CSR描述`
- key: 指定私钥路径
- org: 指定被签发人组织名称
- target: 指定目标CSR路径
```bigquery
NAME:
   BitXHub cert csr - Generate csr file

USAGE:
   BitXHub cert csr [command options] [arguments...]

OPTIONS:
   --key value     Specify Secp256r1 private key path
   --org value     Specify organization name
   --target value  Specific target directory

```

### bitxhub client issue
- `证书签发`
- csr: 指定CSR描述文件路径
- is_ca: 指定签发人是否为CA证书
- key: 指定当前签发人私钥路径
- cert: 指定当前签发人证书路径
- target: 指定生成的证书路径

```bigquery
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
### bitxhub client parse
- `证书解析`
- path: 指定证书路径

```bigquery
NAME:
   BitXHub cert parse - parse certification

USAGE:
   BitXHub cert parse [command options] [arguments...]

OPTIONS:
   --path value  certification path
```

### bitxhub client priv

```bigquery
NAME:
   BitXHub cert priv - Generate and show private key for certificate

USAGE:
   BitXHub cert priv command [command options] [arguments...]

COMMANDS:
   gen  Create new private key
   pid  Show pid from private key

```
#### 私钥生成
```bigquery
NAME:
   BitXHub cert priv gen - Create new private key

USAGE:
   BitXHub cert priv gen [command options] [arguments...]

OPTIONS:
   --name value    Specific private key name
   --target value  Specific target directory
```
样例：
```bigquery
bitxhub cert priv gen --name server --target ./

server.priv key is generated under directory /Users/windiyi/GolandProjectsTest/bitxhub-docs
```
#### 获取libp2p节点pid
```bigquery
NAME:
   BitXHub cert priv pid - Show pid from private key

USAGE:
   BitXHub cert priv pid [command options] [arguments...]

OPTIONS:
   --path value  Specific private key path

```
样例：
```bigquery
bitxhub cert priv pid --path ./server.priv     

QmWw4hPtm51jpxzKjyYMe3E7bNei9wdoug6B1rZqSwVSL4

```