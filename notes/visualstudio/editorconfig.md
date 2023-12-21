# 关于 EditorConfig 文件

EditorConfig 有助于为跨不同编辑器和 IDE 处理同一项目的多个开发人员保持一致的编码风格。

EditorConfig 项目由用于定义编码样式的文件格式和文本编辑器插件集合组成，这些插件使编辑器能够读取文件格式并遵循定义的样式。EditorConfig 文件易于读取，并且与版本控制系统配合得很好。

[https://editorconfig.org](https://editorconfig.org)

# Visual Studio 中的 EditorConfig 支持

Visual Studio 2019 及更高版本支持 EditorConfig 文件，可以在 Visual Studio 中使用 EditorConfig 文件来定义和维护代码样式设置。

[https://github.com/dotnet/aspnetcore/blob/main/.editorconfig](https://github.com/dotnet/aspnetcore/blob/main/.editorconfig)

# 版权说明模板

```ini
// Licensed to the .NET Foundation under one or more agreements.
// The .NET Foundation licenses this file to you under the MIT license.
```

```
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License.
```

# 使用 EditorConfig 添加文件头版权信息

在 Visual Studio 中使用 EditorConfig 文件添加文件头版权信息。

```
// Copyright (c) HelloShop Corporation. All rights reserved.
// Licensed under the MIT License.
```

```
file_header_template = Copyright (c) HelloShop Corporation. All rights reserved.\nLicensed under the MIT License.
```


