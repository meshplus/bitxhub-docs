<p align="center">
  <img src="https://raw.githubusercontent.com/meshplus/bitxhub/master/docs/logo.png" />
</p>

# BitXHub 技术文档

## 项目说明
项目地址：https://github.com/meshplus/bitxhub-docs

开发基于release分支，如对应1.11版本改动，基于release1.11切分支进行修改。

## 环境配置
文档站使用mkdocs进行部署，mkdocs的使用流程如下：

```shell
# 1. 下载mkdoc
$ pip install mkdocs

# 2. 当前所使用的主题为mkdocs-material，需单独下载插件
$ pip install mkdocs-material

# 3. 切换到bitxhub-docs目录下，访问localhost:8000查看当前分支效果
$ mkdocs serve
```

如需进行多版本切换，需要使用mike，使用流程如下：
```shell
$ pip install mkdocs mike mkdocs-material pymdown-extensions lightgallery

# 切换到指定分支，如release1.6
$ mike deploy v1.6
$ mike deploy v1.11

# 将最新的分支设为默认访问地址
$ mike deploy v1.18 latest

# 查看多版本效果
$ mike serve
```
由于网站支持markdown格式，也可以就在本地查看即可。