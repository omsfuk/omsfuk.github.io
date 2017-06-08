1、ng-model
这个大家都非常熟悉了，就是将表单控件和当前作用域的属性进行绑定。需要注意绑定的scope的范围（父scope与子scope）。
2、ng-init
用得比较少，该指令被调用时会初始化内部作用域。一般不建议使用此参数。
3、ng-app
这个是必需的。使用该指令自动启动一个AngularJS应用。ngapp指令指定的应用程序的根元素，通常放置在网页的根元素如body或html 标签。
只有一个AngularJS应用可以自动引导每个HTML文档。第一ngapp找到该文件将定义自动引导的根元素的应用。运行多个应用程序在一个HTML文件，您必须手动引导他们使用angular.bootstrap。AngularJS应用不能互相嵌套。 
你可以指定一个AngularJS模块被用于应用程序的根模块。该模块将被加载到应用程序时，引导到$injector对象中。它应该包含所需的应用程序代码，或依赖于将包含代码的其他模块的依赖关系。更多信息见angular.module。 
4、ng-controller
这个也是经常用到的，用来定义一个控制器。注意格式
5、ng-form用来定义一个from，通常是用来验证参数。通常可以和以下标签一起使用
ng-valid (有效的). 
ng-invalid （无效的）. 
ng-pristine （原始，简介）. 
ng-dirty （脏的）. 
ng-submitted （提交的）
6、ng-disabled
像这种只要出现则生效的属性，我们可以在AngularJS中通过表达式返回值true/false令其生效。禁用表单输入字段。
7、ng-readonly
通过表达式返回值true/false将表单输入字段设为只读。
8、ng-checked
设置是否选中复选框。其中 ng-true-value="''" ng-false-value="''"，可用来设置选中时或不选中时对应的值
9、ng-selected
给`<select>`里面的`<option>`用的
10、ng-show/ng-hide
根据表达式显示/隐藏HTML元素，注意是隐藏，不是从DOM移除（ng-if才是移除），对于大对象的DOM，可以用它，但如果是小对象的DOM，建议使用ng-if
11、ng-change
不是HTML那套onXXX之类的，而是ng-XXX。用来设置input/select等内容发生变化时的事件
12、{{}}
其实这个也是一个指令，也许觉得和ng-bind差不多，但页面渲染略慢时可能会被看到。另外，{{}}的performance远不如ng-bind，只是用起来很方便。
13、ng-bind
ng-bind的行为和{{}}差不多，只是我们可以用这个指令来避免FOUC(Flash Of Unrendered Content)，也就是未渲染导致的闪烁。
14、ng-cloak
ng-cloak也可以为我们解决FOUC。 ng-cloak会将内部元素隐藏，直到路由调用对应的页面。
15、ng-if
如果ng-if中的表达式为false，则对应的元素整个会从DOM中移除而非隐藏，但审查元素时你可以看到表达式变成注释了。如果相进行隐藏，可以使用ng-hide。
16、ng-switch
ngSwitch指令包含ng-switch on、ng-switch-when、ng-switch-default功能类似switch，ng-switch on指要判断的值，ng-switch-when指条件条件符合将显示这个dom元素， ng-switch-default指条件都不符合默认显示的元素。
17、ng-repeat
遍历集合(数组)，给每个元素生成模板实例，每个实例的作用域中可以用一些特殊属性，如下
$index
$first
$last
$middle
even
18、ng-href
起初我在一个文本域中弄了个ng-model，然后像这样`<a href="{{myUrl}}">`在`href`里面写了进去。
19、ng-src
大同小异，即表达式生效前不要加载该资源。
20、ng-class
用作用域中的对象动态改变类样式，
21、ng-click
点击事件
22、ngKeyup
键盘事件
23、ngKeydown
键盘事件
24、ngKeypress
键盘事件
25、ngMousedown、ngMouseenter、ngMouseleave、ngMousemove、ngMouseover、ngMouseup
鼠标事件
26、ngTrim
去除左右空格
