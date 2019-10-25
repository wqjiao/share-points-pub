# tui-image-editor 图片编辑器

`tui-image-editor` 是一个可以对图片进行编辑的 js 库。

## 安装
```
npm install --save tui-image-editor
```

## 代码演示

* js 文件

```javascript
/*
 * @Author: wqjiao 
 * @Date: 2019-01-22 11:29:31 
 * @Last Modified by: wqjiao
 * @Last Modified time: 2019-02-25 14:35:58
 * @Description: EditImage 图片编辑器 https://github.com/nhnent/tui.image-editor.git
 */
import React from 'react';
import ImageEditor from "tui-image-editor";
import theme from "./theme.js";
import "tui-image-editor/dist/tui-image-editor.min.css";

import './index.less';

class EditImage extends React.Component {
    constructor(props) {
        super(props);
    }

    // 初始化图片编辑工具
    init = () => {
		this.imageEditor = new ImageEditor(
            document.querySelector('#tui-image-editor'), {
                includeUI: {
                    locale: {
                        'Text size': '文字大小',
                        'Range': '范围',
                        'Rectangle': '正方形',
                        'Circle': '圆形',
                        'Triangle': '正方形',
                        'Delete-all': '删除全部',
                        'Fill': '填充',
                        'Stroke': '线条',
                        'Undo': '撤消',
                        'Redo': '返回',
                        'Delete': '删除',
                        'Reset': '重置',
                        'Text': '文案',
                        'Draw': '画笔',
                        'Shape': '图形',
                        'Custom': '自定义',
                        'Square': '正方形',
                        'Apply': '应用',
                        'Cancel': '取消',

                        'Bold': '加粗',
                        'Italic': '倾斜',
                        'Underline': '下划线',
                        'Left': '左对齐',
                        'Right': '右对齐',
                        'Center': '居中',
                        'Color': '颜色',
                        'Straight': '直线',
                        'Free': '曲线',
                        'Rotate': '旋转',
                        'Crop': '截取'
                    },
                    loadImage: {
                        path: "../../assets/jiance/ios2703766E-6112-4E2B-A27C-E0BB02CA3CD3.jpg",
                        // path: "../../assets/ios2B24933D-1304-4EEE-B361-75469B5ED625.jpg?imageView2/1/w/800/h/600",
	                    name: 'DemoEditImage'
                    },
                    menu: ['shape', 'text', 'draw', 'rotate', 'crop'],
                    theme: theme.blackTheme,
                    menuBarPosition: 'left'
                },
                cssMaxWidth: 700,
                cssMaxHeight: 500
            }
        );

        this.imageEditor.ui.shape.setShapeStatus({
            strokeColor: '#ff4040',
            strokeWidth: 30
        });

        this.imageEditor.ui.text.fontSize = 130;
    }

    // 点击编辑，显示编辑框
    handleClick = () => {
        this.init();
    }

    /**
     * @method 保存图片为 base64
     * @param [Number] quality 画质
     * @param [String] format 图片类型
     */
    handleSave = () => {
        let me = this;
        let dataURL1 = me.imageEditor.toDataURL({
            quality: 0.3,
            format: 'jpeg'
        });
        let dataURL2 = me.imageEditor.toDataURL({
            quality: 1,
            format: 'jpeg'
        });

        console.log('保存图片1', dataURL1);
        console.log('保存图片2', dataURL2);
    }

    render() {
        return (
            <div>
                <div className="tui-editor-btn">
                    <div className="editor-btn" onClick={this.handleClick}>编辑</div>
                    <div className="editor-btn" onClick={this.handleSave}>保存</div>
                </div>
                <div className="image-editor-box">
                    <div id="tui-image-editor"></div>
                </div>
            </div>
        )
    }
}

export default EditImage;
```

* css 文件

```less
// 按钮样式
.tui-editor-btn {
    margin-bottom: 20px;
    .editor-btn {
        display: inline-block;
        padding: 3px 10px;
        border: 1px solid #e5e5e5;
        border-radius: 4px;
        margin-left: 10px;
        cursor: pointer;
        &:hover {
            color: #108ee9;
            border-color: #108ee9;
        }
    }
}
.image-editor-box {
    width: 1000px;
    height: 600px;
}

/*!
 * Toast UI Colorpicker
 * @version 2.2.0
 * @author NHNEnt FE Development Team <dl_javascript@nhnent.com>
 * @license MIT
 */

.tui-colorpicker-clearfix {
    zoom: 1;
}

.tui-colorpicker-clearfix:after {
    content: "";
    display: block;
    clear: both;
}

.tui-colorpicker-vml {
    behavior: url("#default#VML");
    display: block;
}

.tui-colorpicker-container {
    width: 152px;
}

.tui-colorpicker-palette-container {
    width: 152px;
}

.tui-colorpicker-palette-container ul {
    width: 152px;
    margin: 0px;
    padding: 0px;
}

.tui-colorpicker-palette-container li {
    float: left;
    margin: 0;
    padding: 0 3px 3px 0;
    list-style: none;
}

.tui-colorpicker-palette-button {
    display: block;
    border: none;
    overflow: hidden;
    outline: none;
    margin: 0px;
    padding: 0px;
    width: 16px;
    height: 16px;
    border: 1px solid #ccc;
    cursor: pointer;
}

.tui-colorpicker-palette-button.tui-colorpicker-selected {
    border: 2px solid #000;
}

.tui-colorpicker-palette-button.tui-colorpicker-color-transparent {
    barckground-repeat: repeat;
    background-repeat: no-repeat;
    background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA0AAAAOCAYAAAD0f5bSAAABfGlDQ1BJQ0MgUHJvZmlsZQAAKJFjYGAqSSwoyGFhYGDIzSspCnJ3UoiIjFJgv8PAzcDDIMRgxSCemFxc4BgQ4MOAE3y7xsAIoi/rgsxK8/x506a1fP4WNq+ZclYlOrj1gQF3SmpxMgMDIweQnZxSnJwLZOcA2TrJBUUlQPYMIFu3vKQAxD4BZIsUAR0IZN8BsdMh7A8gdhKYzcQCVhMS5AxkSwDZAkkQtgaInQ5hW4DYyRmJKUC2B8guiBvAgNPDRcHcwFLXkYC7SQa5OaUwO0ChxZOaFxoMcgcQyzB4MLgwKDCYMxgwWDLoMjiWpFaUgBQ65xdUFmWmZ5QoOAJDNlXBOT+3oLQktUhHwTMvWU9HwcjA0ACkDhRnEKM/B4FNZxQ7jxDLX8jAYKnMwMDcgxBLmsbAsH0PA4PEKYSYyjwGBn5rBoZt5woSixLhDmf8xkKIX5xmbARh8zgxMLDe+///sxoDA/skBoa/E////73o//+/i4H2A+PsQA4AJHdp4IxrEg8AAAGbaVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8eDp4bXBtZXRhIHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJYTVAgQ29yZSA1LjQuMCI+CiAgIDxyZGY6UkRGIHhtbG5zOnJkZj0iaHR0cDovL3d3dy53My5vcmcvMTk5OS8wMi8yMi1yZGYtc3ludGF4LW5zIyI+CiAgICAgIDxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSIiCiAgICAgICAgICAgIHhtbG5zOmV4aWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20vZXhpZi8xLjAvIj4KICAgICAgICAgPGV4aWY6UGl4ZWxYRGltZW5zaW9uPjEzPC9leGlmOlBpeGVsWERpbWVuc2lvbj4KICAgICAgICAgPGV4aWY6UGl4ZWxZRGltZW5zaW9uPjE0PC9leGlmOlBpeGVsWURpbWVuc2lvbj4KICAgICAgPC9yZGY6RGVzY3JpcHRpb24+CiAgIDwvcmRmOlJERj4KPC94OnhtcG1ldGE+CghrN1AAAABzSURBVCgVldKxEYAgDAXQD5VOpLuwgi4jlrTMqF00oOd5Aia/CcV/F4oYOgNlrLjvVyCEVJchBjEC25538PeaWTzRMBLxvIL7UZwFwL06qoA6aoAy+gFfJABvJAQPUoCMlICRRd8BzgHzJL4ok9aJ67l4AK9AxVKhHryUAAAAAElFTkSuQmCC");
}

.tui-colorpicker-palette-hex {
    font-family: monospace;
    display: inline-block;
    *display: inline;
    zoom: 1;
    width: 60px;
    vertical-align: middle;
}

.tui-colorpicker-palette-preview {
    display: inline-block;
    *display: inline;
    zoom: 1;
    width: 12px;
    height: 12px;
    border: 1px solid #ccc;
    border: 1px solid #ccc;
    vertical-align: middle;
    overflow: hidden;
}

.tui-colorpicker-palette-toggle-slider {
    display: inline-block;
    *display: inline;
    zoom: 1;
    vertical-align: middle;
    float: right;
}

.tui-colorpicker-slider-container {
    margin: 5px 0 0 0;
    height: 122px;
    zoom: 1;
}

.tui-colorpicker-slider-container:after {
    content: "";
    display: block;
    clear: both;
}

.tui-colorpicker-slider-left {
    float: left;
    width: 120px;
    height: 120px;
}

.tui-colorpicker-slider-right {
    float: right;
    width: 32px;
    height: 120px;
}

.tui-colorpicker-svg {
    display: block;
}

.tui-colorpicker-slider-handle {
    position: absolute;
    overflow: visible;
    top: 0;
    left: 0;
    width: 1px;
    height: 1px;
    z-index: 2;
    opacity: 0.9;
}

.tui-colorpicker-svg-slider {
    width: 120px;
    height: 120px;
    border: 1px solid #ccc;
    overflow: hidden;
}

.tui-colorpicker-vml-slider {
    position: relative;
    width: 120px;
    height: 120px;
    border: 1px solid #ccc;
    overflow: hidden;
}

.tui-colorpicker-vml-slider-bg {
    position: absolute;
    margin: -1px 0 0 -1px;
    top: 0;
    left: 0;
    width: 122px;
    height: 122px;
}

.tui-colorpicker-svg-huebar {
    float: right;
    width: 18px;
    height: 120px;
    border: 1px solid #ccc;
    overflow: visible;
}

.tui-colorpicker-vml-huebar {
    width: 32px;
    position: relative;
}

.tui-colorpicker-vml-huebar-bg {
    position: absolute;
    top: 0;
    right: 0;
    width: 18px;
    height: 121px;
}
```

* theme 主题设置文件

```javascript
/*
 * @Author: wqjiao
 * @Date: 2019-02-21 16:27:13
 * @Last Modified by:   wqjiao
 * @Last Modified time: 2019-02-21 16:27:13
 * @Description: blackTheme 主题配置
 */
let blackTheme = {
	"common.bi.image":
		"https://zzz.xom",
	"common.bisize.width": "251px",
	"common.bisize.height": "21px",
	"common.backgroundImage": "none",
	"common.backgroundColor": "#1e1e1e",
	"common.border": "0px",

	// header
	"header.backgroundImage": "none",
	"header.backgroundColor": "transparent",
	"header.border": "0px",

	// load button
	"loadButton.backgroundColor": "#fff",
	"loadButton.border": "1px solid #ddd",
	"loadButton.color": "#222",
	"loadButton.fontFamily": "'Noto Sans', sans-serif",
	"loadButton.fontSize": "12px",
	"loadButton.display": "none",

	// download button
	"downloadButton.backgroundColor": "#fdba3b",
	"downloadButton.border": "1px solid #fdba3b",
	"downloadButton.color": "#fff",
	"downloadButton.fontFamily": "'Noto Sans', sans-serif",
	"downloadButton.fontSize": "12px",
	"downloadButton.display": "none",

	// main icons
	"menu.normalIcon.path": "./svg/icon-d.svg",
	"menu.normalIcon.name": "icon-d",
	"menu.activeIcon.path": "./svg/icon-b.svg",
	"menu.activeIcon.name": "icon-b",
	"menu.disabledIcon.path": "./svg/icon-a.svg",
	"menu.disabledIcon.name": "icon-a",
	"menu.hoverIcon.path": "./svg/icon-c.svg",
	"menu.hoverIcon.name": "icon-c",
	"menu.iconSize.width": "24px",
	"menu.iconSize.height": "24px",

	// submenu primary color
	"submenu.backgroundColor": "#1e1e1e",
	"submenu.partition.color": "#3c3c3c",

	// submenu icons
	"submenu.normalIcon.path": "./svg/icon-d.svg",
	"submenu.normalIcon.name": "icon-d",
	"submenu.activeIcon.path": "./svg/icon-c.svg",
	"submenu.activeIcon.name": "icon-c",
	"submenu.iconSize.width": "32px",
	"submenu.iconSize.height": "32px",

	// submenu labels
	"submenu.normalLabel.color": "#8a8a8a",
	"submenu.normalLabel.fontWeight": "lighter",
	"submenu.activeLabel.color": "#fff",
	"submenu.activeLabel.fontWeight": "lighter",

	// checkbox style
	"checkbox.border": "0px",
	"checkbox.backgroundColor": "#fff",

	// range style
	"range.pointer.color": "#fff",
	"range.bar.color": "#666",
	"range.subbar.color": "#d1d1d1",

	"range.disabledPointer.color": "#414141",
	"range.disabledBar.color": "#282828",
	"range.disabledSubbar.color": "#414141",

	"range.value.color": "#fff",
	"range.value.fontWeight": "lighter",
	"range.value.fontSize": "11px",
	"range.value.border": "1px solid #353535",
	"range.value.backgroundColor": "#151515",
	"range.title.color": "#fff",
	"range.title.fontWeight": "lighter",

	// colorpicker style
	"colorpicker.button.border": "1px solid #1e1e1e",
	"colorpicker.title.color": "#fff"
};

export default {
	blackTheme: blackTheme
};
```

## toDataURL()

将编辑后的图片转换成 base64，并返回 url。

```
canvas.toDataURL({
   format: 'jpeg',
   quality: 0.8
});

canvas.toDataURL({
    format: 'png',
    left: 300,
    top: 250,
    width: 200,
    height: 150
});
```

## 目前存在的问题

* 再次安装 `tui-image-editor` 后，执行代码报错

  npm install --no-save --no-optional fabric@~1.6.7

* 图标不显示
