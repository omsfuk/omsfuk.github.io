-----------------------
title: Bootstrap布局
tags: bootstrap
-----------------------

## Bootstrap是什么

### Bootstrap简述
* Bootstrap是一款来自Twitter的前段框架。Bootstrap严格上说是一个CSS框架。它由动态CSS语言Less写成。
* Bootstrap包含了丰富的Web组件，根据这些组件，可以快速的搭建一个漂亮、功能完备的网站和管理系统。
Bootstrap包含的组件如下：字体图标、下拉菜单、按钮组、按钮式下拉菜单、输入框组、导航、导航条、分页、标签、徽章、巨幕、页头、略缩图、提示框、进度条、媒体对象、列表组、面板、对话框等。同事Bootstrap也提供较为丰富的jQuery插件，比如过度效果、对话框、下拉菜单、滚动监听、标签页和提示框等一系列插件。

### 使用
* 在使用Bootstrap时，需要在页面中引用`Bootstrap.css`样式。如果要使用到相应的组件，还要引入`jQuery.js`以及`Bootstrap.js`文件。`jQuery`必须在`Bootstrap.js`之前引入。如果要在IE9一下版本中使用Bootstrap，则需要引入html5shiv.js和respond.js两个js。html5shiv.js主要是让不支持HTML5特性的IE浏览器版本识别HTML5中的元素，respond.js是让不支持CSS3媒体查询（MediaQuery）的浏览器（主要是IE9以下版本）支持媒体查询。

```html
<head>
<meta charset="utf-8"/>
<meta http-equiv="X-UA-Compatible" content="IE=edge"/>
<meta name="viewport" content="width=device-width, initial-scale=1"/>
<link href="dist/css/bootstrap.min.css" rel="stylesheet">
<link href="dist/css/bootstrap-theme.min.css" rel="stylesheet"/>
<title></title>
</head>
<body>
<h1>你好，Bootstrap从此刻开始</h1>
<script src="dist/js/jquery.min.js"></script>
<script src="dist/js/bootstrap.min.js"></script>
</body>
```

`<meta name="viewport" content="width=device-width, initial-scale=1"/>`可以实现对不同手机分辨率的支持。
`<link href="dist/css/bootstrap-theme.min.css" rel="stylesheet"/>`一般情况下不用引用到页面中。
html5shiv.min.js和respond.js在页面顶部引入是为了避免在渲染过程中出现闪动问题。
 jquery.min.js和bootstrap.min.js在页面底部加载是为了避免js的阻断加载导致页面渲染缓慢的问题。
