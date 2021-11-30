
# bitxhub key
## 功能

该命令`bitxhub key`提供针对bitxhub节点的私钥管理。
它包含了私钥生成、私钥格式转换、私钥查看、地址查看。
子命令信息如下:
```bigquery
NAME:
BitXHub key - Create and show key information

USAGE:
BitXHub key command [command options] [arguments...]

COMMANDS:
gen      Create new Secp256k1 private key in specified directory
convert  Convert the Secp256k1 private key to BitXHub key format
show     Show BitXHub key from repo
address  Show address from Secp256k1 private key
```

## 子命令
### bitxhub key gen
- 生成节点私钥
- target: 指定生成路径
- passwd: 指定私钥密码
```bigquery
NAME:
   BitXHub key gen - Create new Secp256k1 private key in specified directory

USAGE:
   BitXHub key gen [command options] [arguments...]

OPTIONS:
   --target value  Specific target directory
   --passwd value  Specify password

```
样例
```bigquery
bitxhub key gen --target ~/.bitxhub

key.json key is generated under directory /Users/windiyi/.bitxhub
```
### bitxhub key convert
- 私钥格式转换
- save：是否同时保存目标私钥
- priv: Secp256k1私钥地址，在bitxhub中一般指node.priv
- passwd: Secp256k1私钥密码
```bigquery
NAME:
   BitXHub key convert - Convert the Secp256k1 private key to BitXHub key format

USAGE:
   BitXHub key convert [command options] [arguments...]

OPTIONS:
   --save, -s      Save BitXHub key into repo
   --priv value    Specify private key path
   --passwd value  Specify password
```
### bitxhub key show
样例：
```bigquery
bitxhub key show     

{
 "type": 3,
 "cipher": {
  "data": "18bc2c685cc79d7098fa1f3b97f14541121b61b1d3492ef4f298c84844e7b380fa8e8a84dc44d910aa9dafddc0347c670bace2c74d8553a4c843ae58ce4aeb78",
  "cipher": "AES-256"
 }
}
```
### bitxhub key address
样例：
```bigquery
bitxhub key address --path ./key.json --passwd 123

0x3Db84D019fCd3A845e2fB3A21B56812Ffd933f19
```