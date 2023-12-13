# 如何优雅使用 Git 版本控制

## Git 简介

Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

## 克隆存储库

将远程存储库克隆到本地，在本地进行修改，然后提交到远程存储库。

## 创建存储库

在本地创建存储库，然后将本地存储库推送到远程存储库。关于代码存储库的命名规范使用，使用 KebabCaseLower 命名法。


| Naming policy  | Original | Converted |
|----------------|--------------|--------------|
| PascalCase     | HelloShop  | HelloShop  |
| CamelCase      | HelloShop  | helloShop  |
| SnakeCaseLower | HelloShop  | hello_shop | 
| SnakeCaseUpper | HelloShop  | HELLO_SHOP |
| KebabCaseLower | HelloShop  | hello-shop |
| KebabCaseUpper | HelloShop  | HELLO-SHOP |


## 本地仓库设置

Git 设置包括全局设置和存储库设置，可设置多个远程存储库。

### 说明文件

在存储库中创建一个名为 README.md 的文件，用于存放存储库的说明文档，当然如果可能每个文件夹都应该有一个说明文档。


### 忽略文件

gitignore 文件的作用是指定不需要提交到代码存储库的文件，例如编译后的文件、日志文件等。

https://github.com/github/gitignore/blob/main/VisualStudio.gitignore


## 常规操作

提取、拉取、提交、推送、同步、回滚、标签、冲突、暂存（行）、拉取。

## 分支管理

存储库分支用于管理存储库的版本，例如 master，develop，release，hotfix, feature 等。主要操作包括：创建分支，切换分支，合并分支，删除分支。

## 存储库管理

浏览存储库、管理存储库、使用多个存储库、解决合并冲突。