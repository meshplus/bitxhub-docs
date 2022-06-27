# 验证规则
BitXHub的验证引擎的规则除了内置的规则以外主要是通过WASM字节码来运行逻辑的。该文档将会以rust为例说明验证规则的编写方法。

## 1. 下载规则模板

在GitHub的BitXHub项目已经提供了规则编写[__模板__](https://github.com/meshplus/bitxhub/tree/master/example/rule_example)，用户可以下载这个模板然后对模板进行改造来形成自己所希望运行的规则逻辑。

## 2. 规则模板的目录结构

```text
.
├── Cargo.toml
└── src
    ├── app
    │   ├── contract.rs
    │   └── mod.rs
    ├── crypto
    │   ├── ecdsa.rs
    │   ├── fabric.rs
    │   └── mod.rs
    ├── lib.rs
    ├── memory.rs
    └── model
        ├── mod.rs
        └── transaction.rs
```

从上面的目录结构图中，我们可以看到除去rust项目自己本身自带的Cargo.toml文件以外了，目录的源码目录src下面有三个文件夹和两个rs文件，其中lib.rs文件存放着能够被wasm虚拟机export识别的函数，在规则编写中用户不需要理会这个文件，这个是由模板自己预先编写好的能够读取外部输入参数的函数。还有就是memory.rs这个文件是模板用来处理wasm虚拟机的字符串和字节数组输入输出使用的，也是用户不需要理会的文件。

除了上述的两个rs文件，还有三个文件夹，分别是app, crypto以及model。model文件夹可以让用户存放一些他们规则逻辑中需要使用的数据原型，在模板当中我们使用了fabric的ChaincodeAction的proto作为数据原型作为例子来展现如何引用或者定义自己的数据模型。再其次是crypto库，在BitXHub的规划中会逐渐丰富能够提供给用户的方便使用的密码库，这样能够让用户更方便的调用加密函数来编写自己的验证逻辑。

除了上述的目录和文件，整个合约模板最重要的就是app这个目录，用户主要需要编写的文件也是在这个目录中，其中app这个目录下的contract.rs的文件就是用户编写验证规则逻辑的文件。

```rust

pub fn verify(proof: &[u8], validator: &[u8], payload: &[u8]) -> bool {
  return true
}
```

在模板中我们可以看到这个函数，这个就是用户需要进行逻辑编写的地方，其中该函数的输入由三个参数组成：proof, validator和payload，都是字节数组，用户直接可以用这三个参数完成自己的验证逻辑。如何用proof, validator和payload进行验证就看应用链自己是如何来规定自己的验证逻辑的。

以最简单的椭圆曲线验证为例，在模板库里面提供了一个ecdsa的函数：

```rust
fn ecdsa_verify(sig_ptr: i64, digest_ptr: i64, pubkey_ptr: i64, opt: i32) -> i32;
```

如果我们的验证逻辑是proof就是ecdsa中的签名，payload就是ecdsa中的digest，然后validator就是公钥，那么验证逻辑就可以简单写成：

```rust

pub fn verify(proof: &[u8], validator: &[u8], payload: &[u8]) -> bool {
  return ecdsa::verify(
     &proof,
     &payload,
     &validator,
     ecdsa::EcdsaAlgorithmn::P256,
  );
}
```

更加复杂的逻辑就留给用户自己来实现。

## 3. 编译代码成为WASM字节码

下面来介绍如何用rust将代码编译成简洁的wasm字节码。

首先我们需要添加rust的nightly版本：

```shell
rustup toolchain install nightly
```

更新rust：

```shell
rustup update
```

添加需要的目标工具链：

```shell
rustup target add wasm32-unknown-unknown --toolchain nightly
```

接下来就可以编译我们的项目了：

```text
cargo  +nightly build --target wasm32-unknown-unknown --release
```

我们编译出来的wasm字节码将会在target/wasm32-unknown-unknown/release下面，注意这个时候我们编译出来的wasm字节码的大小是非常大的，我们需要将其精简一下，首先下载工具wasm-gc

```text
cargo install --git https://github.com/alexcrichton/wasm-gc
```

然后使用wasm-gc进行精简：

```text
wasm-gcxxx.wasm small-xxx.wasm
```

获取的small-xxx.wasm就是可以部署到中继链的验证规则。

## AssemblyScript编写验证规则

### 什么是AssemblyScript？

BitXHub v1.18增加了对AssemblyScript编写合约的支持。

AssemblyScript 是一个把 TypeScript 转换到 WebAssembly 的编译器。

Typescript是微软开发的编程语言，通过在JavaScript的基础上添加类型定义构建而成，现在TypeScript已经变得相当受欢迎。由于AssemblyScript只允许TypeScript的有限功能子集，对新人十分友好，开发者不需要花太多时间就可以上手。

AssemblyScript具有以下特点：

- AssemblyScript是专门针对WebAssembly而集成的功能集，使开发人员可以通过代码进行底层控制；

- AssemblyScript作为TypeScript的变体，可以轻松地将其编译为WebAssembly，而无需学习新的语言；

- 集成在现有的Web生态中，无需安装环境和工具链，只需npm install它即可；

### 如何配置AssemblyScript环境？

【预置条件】

首先我们需要下载最新的稳定的Node.js以及npm包管理工具。

【新建项目】

我们首先创建一个新的目录并且像往常一样初始化Node.js的模块：

```bash
npm init
```
然后我们需要安装AssemblyScript的编译器：

```bash
npm install --save-dev assemblyscript
```
当安装完成以后，编译器提供了非常便捷的方法来新建AssemblyScript项目：

```bash
npx asinit
```
【目录结构解析】

在上述完成项目的构建以后，我们可以看到项目下会自动生成一些文件和文件夹，下面会对这些生成的目录结构进行一个解析：
```bash
./assembly
  所有需要被编码成wasm的相关代码都需要放在该文件夹下。

  ./assembly/tsconfig.json
  TypeScript的配置文件，其中集成了一些推荐的AssemblyScript的配置。

  ./assembly/index.ts
  将被编译成wasm的入口文件

  ./build
  所有最终生成的wasm以及相关文件都会被存放在该目录下

  ./build/.gitignore
  Git配置文件

  ./asconfig.json
  配置文件，定义了release版本和Debug版本的相关配置

  ./package.json
  相关的包信息，同时提供了能够编译WebAssembly的命令

  ./tests/index.js
  项目测试的入口文件

  ./index.html
  能够加载模块的网页文件.
```
【编译项目】

从上面的项目目录我们可以看到我们需要在assembly/index.ts中编写自己的wasm模块，在编写完模块之后，我们就可以将整个项目编译成wasm文件了：
```bash
npm run asbuild
```
生成的编译好的二进制文件将会被存放在build目录下，如果想要对整个模块进行测试，可以运行命令：
```bash
npm test
```

### 如何编写合约？
前文我们介绍了「如何用AssemblyScript编写代码和编译生成wasm文件」，那么，如何利用wasm虚拟机将AssemblyScript结合到区块链体系里呢？

首先要处理的是合约方法的生成，在AssemblyScript中，如果我们想要一个加法的合约方法，可以在AssemblyScript中添加如下代码：

```javascript
export function add(a: i32, b: i32): i32 {
  return a + b;
}
```
这样编译出的wasm文件就会直接提供add的方法让合约调用者调用来做加法。

AssemblyScript也提供了wasi的接口，利用这个接口来给方法提供字符串或者字节数组的输入和输出。例如：我们可以直接通过操作虚拟机的内存给合约传递参数：

```javascript
export function allocate(size: usize): usize {
  return heap.alloc(size);
}
```
这段代码中我们就是向虚拟机堆上申请特定大小的内存空间，返回的是申请的内存的地址，可以在虚拟机外向这块内存输入上下文，从而达到输入输出字符串的目的。
