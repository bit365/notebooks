# 在项目中添加文件头注释

## 文件头注释

文件头注释是一种用于说明文件版权和许可的注释。它通常包括版权声明、许可证信息、作者信息和其他相关信息。文件头注释是一种很好的实践，可以帮助开发人员了解文件的版权和许可信息。

## 版权申明规范

```csharp
// Copyright (c) HelloShop Corporation. All rights reserved.
// See the license file in the project root for more information.

namespace HelloShop.AppHost
{
    internal class Class1
    {
    }
}
```

## 关于 EditorConfig 配置文件

为跨不同编辑器和 IDE 处理同一项目的多个开发人员保持一致的编码风格。

Visual Studio 2019 及更高版本支持 EditorConfig 文件，可以在 Visual Studio 中使用 EditorConfig 文件来定义和维护代码样式设置。

## 版权说明模板

```
file_header_template = Copyright (c) HelloShop Corporation. All rights reserved.\nSee the license file in the project root for more information.
```

对于文件名，可以使用 `{fileName}` 作为占位符。

