# 创建代码仓库

## 仓库命名规范

在 Github 上创建一个名为 HelloShop 的代码仓库，关于代码仓库的命名规范使用，使用 SnakeCaseLower 命名法。


| Naming policy  | Original | Converted |
|----------------|--------------|--------------|
| PascalCase     | HelloShop  | HelloShop  |
| CamelCase      | HelloShop  | helloShop  |
| SnakeCaseLower | HelloShop  | hello_shop | 
| SnakeCaseUpper | HelloShop  | HELLO_SHOP |
| KebabCaseLower | HelloShop  | hello-shop |
| KebabCaseUpper | HelloShop  | HELLO-SHOP |

## 仓库文件夹结构

在仓库中创建一个名为 src 的文件夹，用于存放源代码。

## 说明文件

在仓库中创建一个名为 README.md 的文件，用于存放仓库的说明文档，当然如果可能每个文件夹都应该有一个说明文档。


## 忽略文件

gitignore 文件的作用是指定不需要提交到代码仓库的文件，例如编译后的文件、日志文件等。

https://github.com/github/gitignore/blob/main/VisualStudio.gitignore


## 属性文件

gitattributes 文件的作用是指定文件的属性，例如文件的换行符、文件的编码等。

## 目录结构

`assets` 静态资源，包括图片，图标，视频，音频等。

`build` 构建脚本，包括编译脚本，打包脚本，发布脚本等。

`docs` 相关文档，包括设计文档，架构文档，开发文档，部署文档等。`

`samples` 演示示例，包括代码示例，配置示例，数据示例，文档示例等。

`src` 源代码，包括源代码，配置文件，资源文件，脚本文件等。

`tests` 测试代码，包括单元测试，集成测试，端到端测试等。

`tools` 项目所使用的一些工具。


## 仓库分支

仓库分支用于管理仓库的版本，例如 master，develop，release，hotfix, feature 等。


## 仓库标签

仓库标签用于标记仓库的版本，例如 v1.0.0，v1.0.1，v1.1.0，v2.0.0 等。

