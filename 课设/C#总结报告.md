-----------------------
title: C#总结报告
tags: C#
-----------------------

> # C#探索报告

# C#简介
请参见[http://www.cnblogs.com/Young-wind/p/5843488.html](http://www.cnblogs.com/Young-wind/p/5843488.html)
语法上和Java是差不多的，是微软为了干掉Java而出的一门语言。

# 与java的对比
请参见[en.wikipedia.org/wiki/Comparison_of_C_Sharp_and_Java](https://en.wikipedia.org/wiki/Comparison_of_C_Sharp_and_Java)
# C#的优点与缺点
请参见[http://www.360doc.com/content/13/1121/09/10504424_330951715.shtml](http://www.360doc.com/content/13/1121/09/10504424_330951715.shtml)

# 个人感受

个人对C#的使用不是很多，仅局限于用C#写了个俄罗斯方块，做一点自动化工具，而且上大学后就没用过，所以不好对C#妄加评论。

个人感觉用C#写桌面程序挺方便的，编辑器原生的所见即所得工具。比起java的swing不是一个档次。虽然写桌面程序也不是C#的强项，当然个人写个小东西还是可以的。

据说写web用的多一点。虽然没用过ASP.NET。。

顺便说一下，C#可以实现伪跨平台。

# 安装Visual Studio
请参见[http://jingyan.baidu.com/article/495ba84113bf4938b30edecf.html](http://jingyan.baidu.com/article/495ba84113bf4938b30edecf.html)

备注
* PS：话说我还是认为Visual Studio世界上最好用的编辑器，没有之一
* visual studio的安装很简单，不像Android Studio安装那么麻烦。建议不要改变安装目录。有些组件是必须装在C盘的，即时选择了其他盘，你会发现安装的大部分体积都在C盘。
* 大约要花费20个G
* 建议将可选项都选中，即使用不到，也可以留下个概念。另外，即时将可选功能全都去掉，也只不过节约了不到3G空间，完全没必要去掉。当然，你如果非要去掉的话，可以全都去掉，反正接下来的PC定位例子用不到。
* 另外，建议装在Win8、win10上（不会有高人想装在linux上吧。。当然据说是出了一个跨平台的C#组件库，但显然Visual Studio是不会移植过去的），
* 第一次启动时会让你选择默认开发环境。这里选择C#


# A Demo
## 说明
这个demo可以实现位置查询，当然不是那么精确，只能精确到市。精确到GPS的接口百度还没公布。

## 具体实现
具体就是拖一个按钮控件到界面上，然后双击此按钮，C#会为你自动生成OnClick代码框架，只需要将OnClick补充完整就好了。
在onclick里面加上这一段
```
try {

                WebClient MyWebClient = new WebClient();
                MyWebClient.Credentials = CredentialCache.DefaultCredentials;//获取或设置用于向Internet资源的请求进行身份验证的网络凭据

                Byte[] pageData = MyWebClient.DownloadData("http://restapi.amap.com/v3/ip?key=af37ce56cbd20a52aaaee19c07dd337a"); //从指定网站下载数据
                // 请去高德申请一个开发者账号，将此key填写成你自己的key，当然，用我的也行

                string pageHtml = Encoding.UTF8.GetString(pageData); //如果获取网站页面采用的是UTF-8，则使用这句
                MatchCollection matches = Regex.Matches(pageHtml, "\"(.+?)\":\"(.+?)\"");

                MessageBox.Show("您正处于 : " + matches[3].Groups[2].ToString() + matches[4].Groups[2].ToString());
               // pageHtml.
               // MessageBox.Show(pageHtml);

            } catch (WebException webEx) {

                Console.WriteLine(webEx.Message.ToString());

            }
```

点击`F5`
OK，搞定

结束，只用几分钟，我可以开发出一个具有GUI的桌面程序。
可见，C#确实开发效率很高。

源码在附件中。

想深入学习C#者，请自行百度
虽然我是不建议将C#当做主语言的，，，
但这并不代表不能学习一下，了解并深入学习一下其他语言的特性，有助于扩大视野。他山之石，可以攻玉。

PS：貌似即使是培训班都没有.NET的课了。。。。
