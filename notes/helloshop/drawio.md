# 使用 Drawio 画架构

## 微软 Visio 工具

Visio 是微软的画图工具，非常强大，但是收费，而且只能在 Windows 系统上使用，不支持 Mac 和 Linux 系统，虽然微软推出了 Visio Online，但是功能还是比较弱，而且收费。

## 开源 Drawio 工具

Drawio 是一个开源的画图工具，支持在线和桌面两种方式，支持多种格式，支持多种平台，支持多种语言，支持多种图形，支持多种导出格式，支持多种插件，支持多种集成方式，也支持在 Visual Studio Code 中使用插件的方式画图。

## 画图入口

[Drawio 官网](https://www.drawio.com)

[在线画图](https://www.drawio.com)

## 容器和容器大小缩放

设置图形 Container 属性为 true，可以将多个图形组合成一个图形，然后可以对这个图形进行缩放，设置 Collapsed 属性为 true，可以将图形折叠起来，只显示标题。设置 Resizable 属性为 false，可以禁止容器子图形缩放。

## 浮动链接和固定链接

浮动链接是指链接的位置随着图形的移动而移动，固定链接是指链接的位置不随着图形的移动而移动，可以添加自定义连接点。

## 拖动连接线文字

设置连接线文字可以拖动，可以将连接线文字拖动到连接线的任意位置，可以自定难以连接点的位置。

## 连接线的样式和反转

可以设置连接线的样式，可以设置连接线的反转，可以设置连接线的弯曲程度。

## 图形和连接线文字位置

设置文字位置，可以将文字放在图形或连接线左上角，设置文字位置为中心，可以将文字放在图形的中心。

## 连接线添加航点

可以在连接线上添加航点，可以在连接线上添加箭头，可以在连接线上添加标签。

## 任何目标可设置链接

可以设置任何对象的超级链接，可以设置连接线的超级链接，可以设置图形的超级链接。

## 替换图形

可以将新图形拖动到现有图形上替换图形，替换后，图形的属性和连接线都会保留。

## 对齐方式和分布方式

可以设置图形的对齐方式，可以设置图形的分布方式。

## 带着样式画图

可以带着样式画图，可以将样式应用到图形上，也可以清除默认样式。

## 嵌入图像和在线图像

理解几何图形，图形库图形，嵌入图像，在线图像的区别。

## 嵌入 SVG 图像修改样式

使用阿里图标库 iconfont 找图，可以下载 SVG 图像，然后嵌入到 Drawio 中，可以修改 SVG 图像的样式，但需要配置规则，如下代码。

```xml
<svg>
    <style type="text/css">
        .st1{fill:#ffffff;stroke:#ffffff;}
    </style>
    <path class="st1" d="points"/>
</svg>
```

```
editableCssRules=.*;
```

## 导出 SVG 格式选项

设置缩放系数，边框，设置背景颜色，图像副本，嵌入图像，链接打开方式。

## 连接线流程动画

设置 Flow Animation 属性为 true，可以设置连接线的流程动画。该功能基于 CSS 的动画能力实现，简单好用，基本满足需求。

## 自定义 SVG 复杂动画

可以使用 SVG 自定义动画，可以设置动画的开始时间，持续时间，重复次数，动画类型，动画属性，动画值，动画路径，动画方向，动画速度，动画延迟，动画填充，动画结束后的状态。

[W3C 关于 SVG 的动画标准](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/animate)

定义一个简单的路径动画。

```xml
<circle r="3.5" fill="#00aa9e">
    <animateMotion dur="5s" repeatCount="indefinite" path="points"/>
</circle>
```

使用 XLINK 优雅的连接动画

```xml
<path id="stage1-1" d="points"/>

<circle r="3" fill="red">
    <animateMotion dur="5s" repeatCount="indefinite">
        <mpath xlink:href="#stage1-1"/>
    </animateMotion>
</circle>
```
