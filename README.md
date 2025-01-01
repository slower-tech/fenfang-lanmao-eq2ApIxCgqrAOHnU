
### 【写在前面】


毛玻璃效果（Acrylic Effect）是一种常见的 UI 设计风格，它通过模糊背景并添加透明度和噪声效果，使界面元素看起来像是半透明的磨砂玻璃。


本文将介绍如何使用 Qml 实现这种效果，并提供一个完整的示例代码。




---


### 【正文开始】


#### 1\. 效果图


![image](https://img2024.cnblogs.com/blog/802097/202412/802097-20241231180806959-1970590838.png)


#### 2\. 毛玻璃效果的实现原理


毛玻璃效果的核心是通过模糊背景图像，并叠加透明度和噪声效果来实现。


具体来说，实现毛玻璃效果的步骤如下：


1. **捕获背景图像**：首先需要捕获背景图像，作为模糊效果的输入。
2. **模糊处理**：对捕获的背景图像进行模糊处理，生成模糊效果。
3. **添加亮度和色调**：通过叠加一个半透明的矩形，调整模糊区域的亮度和色调。
4. **添加噪声效果**：在模糊区域上叠加噪声图像，增加磨砂玻璃的质感。


#### 3\. 实现毛玻璃效果


`DelAcrylic.qml` 文件定义了一个自定义的 Qml 组件，用于实现毛玻璃效果。以下是该文件的主要部分：


* **`ShaderEffectSource`**: 用于捕获源项（`sourceItem`）的内容，并将其作为模糊效果的输入。`sourceRect` 属性定义了捕获的区域。



```
ShaderEffectSource {
    id: __source
    anchors.fill: parent
    visible: false
    sourceRect: Qt.rect(control.x, control.y, control.width, control.height)
}

```

* **`FastBlur`**: 对 `ShaderEffectSource` 的内容进行模糊处理。`radius` 属性控制模糊的强度。



```
FastBlur {
    id: __fastBlur
    anchors.fill: parent
    source: __source
    radius: 32
}

```

* **`Rectangle`**: 有两个矩形，第一个矩形用于设置背景的亮度（`luminosity`），第二个矩形用于设置颜色的色调（`colorTint`）和透明度（`opacityTint`）。



```
Rectangle {
    anchors.fill: parent
    color: Qt.rgba(1, 1, 1, luminosity)
    radius: control.radiusBg
}

Rectangle {
    anchors.fill: parent
    color: Qt.rgba(colorTint.r, colorTint.g, colorTint.b, opacityTint)
    radius: control.radiusBg
}

```

* **`Image`**: 用于添加噪声效果，噪声图像通过 base64 编码嵌入到 Qml 文件中。`opacity` 属性控制噪声的透明度。



```
Image {
    id: __noiseImage
    anchors.fill: parent
    source: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAAEnQAABJ0Ad5mH3gAAAGHaVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8P3hwYWNrZXQgYmVnaW49J++7vycgaWQ9J1c1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCc/Pg0KPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyI+PHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj48cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0idXVpZDpmYWY1YmRkNS1iYTNkLTExZGEtYWQzMS1kMzNkNzUxODJmMWIiIHhtbG5zOnRpZmY9Imh0dHA6Ly9ucy5hZG9iZS5jb20vdGlmZi8xLjAvIj48dGlmZjpPcmllbnRhdGlvbj4xPC90aWZmOk9yaWVudGF0aW9uPjwvcmRmOkRlc2NyaXB0aW9uPjwvcmRmOlJERj48L3g6eG1wbWV0YT4NCjw/eHBhY2tldCBlbmQ9J3cnPz4slJgLAAAMNElEQVRYR02XW1NTd9vGf0nYZEFMVha7JBAIYYihEQVSUQQVtOrUtipVsTPtTOs38KDtUQ+Y6ZfoTI88aT3o2Jk6bUfa4QFEKZWIsgm7ECKQHZu1FpiQQAx5DnyfzHv6n//Jvbl+13Vrvv/++5zRaOSPP/7giy++wGQyMTw8TCgUoquri2QySSgUorm5mcbGRh4/fozT6cTtdvPgwQMMBgPnzp0DYHR0FEmSiEag=="
    fillMode: Image.Tile
    opacity: 0.02
}

```

#### 4\. 如何使用


`main.qml` 是示例文件，使用 `DelAcrylic` 组件并提供了交互式的滑块来控制模糊效果的参数。


以下是该文件的主要部分：


* **`Window`**: 定义了一个窗口，宽度为 750，高度为 500，标题为 "Acrylic Test"。



```
Window {
    width: 750
    height: 500
    visible: true
    title: qsTr("Acrylic Test")
}

```

* **`Image`**: 作为背景图像，填充整个窗口。



```
Image {
    id: bg
    anchors.fill: parent
    source: "qrc:/img.jpg"
}

```

* **`DelAcrylic`**: 使用 `DelAcrylic` 组件，将其放置在窗口的中心位置，并绑定背景图像作为模糊效果的源项。`opacityNoise`、`opacityTint` 和 `radiusBlur` 属性分别绑定到三个滑块的值。



```
DelAcrylic {
    id: acrylic
    x: (bg.width - width) * 0.5
    y: (bg.height - height) * 0.5
    width: 300
    height: 300
    sourceItem: bg
    opacityNoise: slider1.value
    opacityTint: slider2.value
    radiusBlur: slider3.value

    MouseArea {
        anchors.fill: parent
        drag.target: parent
    }
}

```

* **`Column`**: 包含三个滑块，分别用于控制 `opacityNoise`、`opacityTint` 和 `radiusBlur` 属性。



```
Column {
    Slider {
        id: slider1
        anchors.horizontalCenter: parent.horizontalCenter
        from: 0
        to: 1
        stepSize: 0.01
        value: 0.02
        ToolTip.visible: hovered
        ToolTip.text: value.toFixed(2)

        Text {
            anchors.left: parent.right
            anchors.leftMargin: 10
            anchors.verticalCenter: parent.verticalCenter
            text: qsTr("opacityNoise")
        }
    }

    Slider {
        id: slider2
        anchors.horizontalCenter: parent.horizontalCenter
        from: 0
        to: 1
        stepSize: 0.01
        value: 0
        ToolTip.visible: hovered
        ToolTip.text: value.toFixed(2)

        Text {
            anchors.left: parent.right
            anchors.leftMargin: 10
            anchors.verticalCenter: parent.verticalCenter
            text: qsTr("opacityTint")
        }
    }

    Slider {
        id: slider3
        from: 0
        to: 100
        value: 48
        ToolTip.visible: hovered
        ToolTip.text: value.toFixed(0)

        Text {
            anchors.left: parent.right
            anchors.leftMargin: 10
            anchors.verticalCenter: parent.verticalCenter
            text: qsTr("radiusBlur")
        }
    }
}

```



---


### 【结语】


通过 `DelAcrylic.qml` 和 `main.qml` 文件，我们实现了一个具有毛玻璃效果的 Qml 应用程序。`DelAcrylic` 组件通过模糊背景图像、添加亮度和色调、以及叠加噪声效果，实现了毛玻璃效果。


这种毛玻璃效果可以广泛应用于现代 UI 设计中，特别是在需要模糊背景或实现类似 `Acrylic` 效果的场景中。通过调整模糊强度、透明度和噪声效果，我们可以创建出丰富多样的视觉效果，提升用户体验。


改进建议：


* **性能优化**：模糊效果可能会对性能产生影响，特别是在高分辨率或复杂场景下。可以考虑优化模糊算法或降低模糊的强度以提高性能。
* **更多参数控制**：可以添加更多的参数控制，例如噪声的类型、模糊的方向等，以提供更丰富的视觉效果。


最后：项目链接(多多star呀..⭐\_⭐)：


Github: [https://github.com/mengps/QmlControls](https://github.com)


Gitee: [https://gitee.com/MenPenS/QmlControls](https://github.com)


 ![](https://github.com/blog/802097/202404/802097-20240417231514692-194340162.png)    - **本文作者：** [梦起丶](https://github.com)
 - **本文链接：** [https://github.com/mengps/p/18644624](https://github.com):[CMESPEED\-楚门加速器](https://cmnspeed.com)
 - **关于博主：** 🎉🎉🎉专注于 C / C\+\+ / Qt / JS / Python 编程技巧🎉🎉🎉

✨个人微信✨：MenPenS612 (交流合作都行\~)

✨交流群✨：83986890 ヾ(￣▽￣)欢迎来玩\~

✨公众号✨：程序梦 (扫左侧二维码)
 - **版权声明：** 本博客所有文章除特别声明外，均采用 [BY\-NC\-SA](https://github.com "BY-NC-SA") 许可协议。转载请注明出处！
 - **声援博主：** 如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。
     
