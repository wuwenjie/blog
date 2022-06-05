---
id: 209
title: FusionCharts参数说明补充
date: '2010-04-11T12:56:24+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=209'
permalink: /2010/04/fusioncharts-api
categories:
    - 'net&amp;c#'
---

**功能特性** animation 是否动画显示数据，默认为1(True)  
showNames 是否显示横向坐标轴(x轴)标签名称  
rotateNames 是否旋转显示标签，默认为0(False):横向显示  
showValues 是否在图表显示对应的数据值，默认为1(True)  
yAxisMinValue 指定纵轴(y轴)最小值，数字  
yAxisMaxValue 指定纵轴(y轴)最小值，数字  
showLimits 是否显示图表限值(y轴最大、最小值)，默认为1(True)

**图表标题和轴名称**  
caption 图表主标题  
subCaption 图表副标题  
xAxisName 横向坐标轴(x轴)名称  
yAxisName 纵向坐标轴(y轴)名称

**图表和画布的样式**  
bgColor 图表背景色，6位16进制颜色值  
canvasBgColor 画布背景色，6位16进制颜色值  
canvasBgAlpha 画布透明度，\[0-100\]  
canvasBorderColor 画布边框颜色，6位16进制颜色值  
canvasBorderThickness 画布边框厚度，\[0-100\]  
shadowAlpha 投影透明度，\[0-100\]  
showLegend 是否显示系列名，默认为1(True)

**字体属性**  
baseFont 图表字体样式  
baseFontSize 图表字体大小  
baseFontColor 图表字体颜色，6位16进制颜色值  
outCnvBaseFont 图表画布以外的字体样式  
outCnvBaseFontSize 图表画布以外的字体大小  
outCnvBaseFontColor 图表画布以外的字体颜色，6位16进制颜色值

**分区线和网格**  
numDivLines 画布内部水平分区线条数，数字  
divLineColor 水平分区线颜色，6位16进制颜色值  
divLineThickness 水平分区线厚度，\[1-5\]  
divLineAlpha 水平分区线透明度，\[0-100\]  
showAlternateHGridColor 是否在横向网格带交替的颜色，默认为0(False)  
alternateHGridColor 横向网格带交替的颜色，6位16进制颜色值  
alternateHGridAlpha 横向网格带的透明度，\[0-100\]  
showDivLinues 是否显示Div行的值，默认？？  
numVDivLines 画布内部垂直分区线条数，数字  
vDivLineColor 垂直分区线颜色，6位16进制颜色值  
vDivLineThickness 垂直分区线厚度，\[1-5\]  
vDivLineAlpha 垂直分区线透明度，\[0-100\]  
showAlternateVGridColor 是否在纵向网格带交替的颜色，默认为0(False)  
alternateVGridColor 纵向网格带交替的颜色，6位16进制颜色值  
alternateVGridAlpha 纵向网格带的透明度，\[0-100\]

**数字格式**  
numberPrefix 增加数字前缀  
numberSuffix 增加数字后缀 % 为 ‘%25’  
formatNumberScale 是否格式化数字,默认为1(True),自动的给你的数字加上K（千）或M（百万）；若取0,则不加K或M  
decimalPrecision 指定小数位的位数，\[0-10\] 例如：=’0′ 取整  
divLineDecimalPrecision 指定水平分区线的值小数位的位数，\[0-10\]  
limitsDecimalPrecision 指定y轴最大、最小值的小数位的位数，\[0-10\]  
formatNumber 逗号来分隔数字(千位，百万位),默认为1(True)；若取0,则不加分隔符  
decimalSeparator 指定小数分隔符,默认为’.’  
thousandSeparator 指定千分位分隔符,默认为’,’

**Tool-tip/Hover标题**  
showhovercap 是否显示悬停说明框，默认为1(True)  
hoverCapBgColor 悬停说明框背景色，6位16进制颜色值  
hoverCapBorderColor 悬停说明框边框颜色，6位16进制颜色值  
hoverCapSepChar 指定悬停说明框内值与值之间分隔符,默认为’,’

**折线图的参数**  
lineThickness 折线的厚度  
anchorRadius 折线节点半径，数字  
anchorBgAlpha 折线节点透明度，\[0-100\]  
anchorBgColor 折线节点填充颜色，6位16进制颜色值  
anchorBorderColor 折线节点边框颜色，6位16进制颜色值

**Set标签使用的参数**  
value 数据值  
color 颜色  
link 链接（本窗口打开\[Url\]，新窗口打开\[n-Url\]，调用JS函数\[JavaScript:函数\]）  
name 横向坐标轴标签名称FusionCharts v3新增功能

新的3.1版本：   
使得出口能力为PDF格式的图表和图片在客户端以及服务器端   
旋转文字并不需要任何更多的嵌入字体。任何UTF – 8字符现在可以旋转和使用图表从而FusionCharts真正多语言。   
选项指定文本价值，可以代替数值是图表上显示的每个数据项   
无法加载自定义标识，图表上在预先确定的位置，然后连结相同   
…  
FusionCharts v3新增功能 FusionCharts v3的拥有大量的新功能，在以前的版本。下面是几个主要的。   
  
  
有什么新的3.1版本：   
使得出口能力为PDF格式的图表和图片在客户端以及服务器端   
旋转文字并不需要任何更多的嵌入字体。任何UTF – 8字符现在可以旋转和使用图表从而FusionCharts真正多语言。   
选项指定文本价值，可以代替数值是图表上显示的每个数据项   
无法加载自定义标识，图表上在预先确定的位置，然后连结相同   
选择添加自定义菜单项，以图表的上下文菜单，然后连结相同   
支持包装的标题，分标题和工具   
趋势线现在可以自定义工具文本   
用户定义调色板的数据项目   
更多的JavaScript事件，以帮助您更好的操纵图表从您的JavaScript代码   
出口能力的数据，图表的CSV使用上下文菜单或JavaScript的API   
能力得到了XML的任何使用JavaScript API的图表   
能力得到了一张图表属性使用JavaScript的API   
图表已重新在36fps解决内存问题在Firefox   
  
新3.0 有什么？   
新的图表类型   
FusionCharts v3的介绍了很多新的图表类型，如：   
  
滚动图-柱二维，二维和区系的二维，堆叠柱二维，二维结合，结合二维（双年）   
样图   
样条区域图   
对数坐标图   
二维多图单Ÿ组合图。   
二维多图双Ÿ组合图。此图被用来阴谋9类型的图表：   
柱（小年） +线（中学年）组合   
柱（小年） +地区（中学年）组合   
柱（小年） +柱（中学年）组合   
区（小年） +线（中学年）组合   
区（小年） +柱（中学年）组合   
区（小年） +地区（中学年）组合   
线（小年） +线（中学年）组合   
线（小年） +柱（中学年）组合   
线（小年） +地区（中学年）组合   
三维堆叠柱线双Ÿ组合图   
三维单杠   
三维堆叠式酒吧   
先进的蜡烛棒图支持线，酒吧和数量列   
拖放能够柱线图表先进的模拟情景   
瀑布图   
  
少数上述所列图表不标准FusionCharts v3的包。例如，花键，对数，瀑布及拖放能够图表的一部分， PowerCharts包，可单独购买..   
  
出口能力的图表图像   
从FusionCharts v3.0.5 ，现在您可以导出您的图表，图片相结合的客户端和服务器端操作。   
  
容易，但先进的整合与JavaScript   
FusionCharts v3的提供高级选项，将图表与AJAX应用程序或JavaScript模块。您可以随时更新海图在客户端，调用JavaScript函数的热点链接，或要 求作出动态XML数据不涉及任何页面刷新。您也可以指定一个DOMId的每个图表和有登记的JavaScript 。图表可以保持JavaScript函数公布了它的活动。   
  
可视化的XML生成工具   
FusionCharts v3的介绍了一个新的Visual XML和图表生成工具，帮助您轻松地建立您的XML数据的图表。您可以手动输入数据的网格，形成XML或转换您现有的数据从电子表格/ csv文件/表格到XML数据。   
  
时尚元素   
FusionCharts v3的介绍方式，帮助您适用的字体，效果和动画，各种物体的图表。一个简单的样式贷款机制，利用这些您就可以轻松地控制可视化布局图。   
  
地图支持   
FusionCharts v3的介绍地图PowerMaps包。该PowerMaps Pack是收集61基于Flash矢量地图用来显示不同类型的数据与地理分区。适用于网站和应用程序，每一个地图暴露了其性能使用一个XML的API 。建立一个地图使用几乎需要几分钟，不涉及任何修改源代码。所有您需要做的是饲料中的数据的XML文件，您已经准备好。   
  
请注意， PowerMaps是一项额外插入FusionCharts v3的，而不是在标准许可。您需要另行购买。   
  
内置的Adobe Flash 8使用ActionScript 2的   
FusionCharts v3是编码在Flash 8和ActionScript 2 ，充分利用新的和先进功能的Flash 8喜欢动态渐变，过滤器，更好的速度，先进的面向对象的结构等   
  
新的调试模式   
FusionCharts v3的介绍了调试模式为每个图表。在调试模式可以帮助您寻找到正在发生着什么图表幕后。你可以看到图表是如何初始化，获得的数据以及与 JavaScripts 。各种错误产生，也表明在这。所以，当你看到一个错误的图表现在，你需要做的是切换到调试模式，并确定了。   
  
梯度支持   
FusionCharts v3的支持梯度大多数图表物件如背景下，油画，数据阴谋等诸多新的图表支持单一属性use3DLighting ，让先进的灯光和梯度影响图更好的视觉效果。   
  
调色板支持   
FusionCharts v3的介绍调色板，以帮助您快速选择颜色主题，为您的图表。从v3的，你可以选择其中一个五年预先定义调色板改变的外观图。你不必指定任何十六进制颜色代码使用调色板。   
  
虚线支持   
从FusionCharts v3的，您可以使用虚线策划：   
  
数据（列，线，馅饼等）   
网格分区线   
趋势线   
垂直分离线   
您也可以指定破折号性能像破折号长度，差距等   
  
多种显示模式的数据标签   
在FusionCharts v3的，很多选择了介绍，以便更好地x轴Label控件。现在，您可以包装，错层或旋转X轴标签。   
  
旋转价值盒及动态位置选项   
的数据值的文本字段，现在可以旋转，以避免简洁。此外，在案件列图表，您可以选择是否将文本框的值列内或之外。如果没有空间， FusionCharts v3的会自动调整位置。   
  
一些规模支持   
FusionCharts v3的介绍了一些调整和更好地控制数字格式。   
  
自动分区线编码   
FusionCharts v3的介绍自动分区线编号和位置，以最佳的位置上的图表。   
  
  
更好的打印支持   
在上下文菜单中的图表现在包括一个新项目“打印图表” ，它提供标准的跨浏览器的打印支持。   
  
更多的控制权动态调整   
v3的推出两种模式的图表大小- exactFit和noScale 。 noScale使用基于像素的大小。在exactFit模式，您可以调整图的基础上的百分比。此外， exactFit模式允许动态的调整，当容器对象（浏览器，表，分区等）调整大小。   
  
先进的馅饼和甜甜圈图表   
馅饼和甜甜圈图表FusionCharts v3的提供先进的最终用户的交互选择像动态切片，旋转，链接等此外，智能标签已被引入馅饼/甜甜圈图表。   
  
更好的传说   
FusionCharts v3的介绍了一个更好的期待和互动传说的multi-series/combination图表。您可以自定义了很多的属性的传说每个图表。   
  
高级钻取功能   
图表项目现在可以链接到新窗口，弹出式，框架或自我窗口。   
  
  
垂直分工之间的界线任何两个数据点。   
在轴的图表，现在你可以选择垂直分工之间的界线任何两套数据。这特别有助于当你策划的数据说， 2岁，你想一个明显的分隔符之间两年的数据图表。   
  
整个图表作为一个热点   
v3的开始，整个图表现在可以作为一个单一的热点。   
  
自定义工具提示为每个数据阴谋项目   
现在您可以设定您自己的工具提示文字为每个数据阴谋项目。   
  
多语言支持的应用信息   
现在，您可以轻松定制的图表显示应用消息（载入中图表，装载数据，绘制图表等。 ）在您自己的语言。要做到这一点，你需要指定的邮件图表来源和重新编译。

一个Y轴中文例：

**（注意：chart.setDataURL中若包含中文，charset应为GBK）**  
chart.setDataXML(“&lt;chart rotateYAxisName=’0′ baseFont=’宋体’ baseFontSize=’14’ palette=’10’ caption=’标题’ xAxisName=’X轴’ yAxisName=’Y轴’ showValues=’0′ decimals=’0′ formatNumberScale=’0′ useRoundEdges=’1′&gt;&lt;set label=’1月’ value=’462′ /&gt;&lt;set label=’Feb’ value=’857′ /&gt;&lt;set label=’Mar’ value=’671′ /&gt;&lt;set label=’Apr’ value=’494′ /&gt;&lt;set label=’May’ value=’761′ link=’javascript:myJS()’/&gt;&lt;set label=’Jun’ value=’960′ link=’n-DemoLinkPages/DemoLink1.html’/&gt;&lt;/chart&gt;”)

<figure class="wp-block-image">![](http://hiphotos.baidu.com/handboy/pic/item/7cf42f3d367bd646bba16745.jpg)</figure>在FCF里显示“￥”，你需要用“%A5”来替换它。如下：  
&lt;graph decimalPrecision=’0′ numberPrefix=’%A5′ &gt;   
&lt;set name=’John’ value=’420′ color=’AFD8F8′ /&gt;   
&lt;set name=’Mary’ value=’295′ color=’F6BD0F’ /&gt;   
&lt;set name=’Tom’ value=’523′ color=’8BBA00′ /&gt;   
&lt;/graph&gt;

<figure class="wp-block-image">![](http://hiphotos.baidu.com/handboy/pic/item/fd927d11a02c4056b8127b45.jpg)</figure>用FusionCharts v3可以保存图片和PDF

FusionCharts V3图表导出图片和PDF属性说明  
<http://www.cnblogs.com/ATree/archive/2010/07/21/FusionCharts-Export-Image-Pdf.html>属性名 类型 描述 [FusionCharts](http://www.cnblogs.com/ATree/archive/2010/04/20/FusionCharts3v-flash.html) V3核心导出功能相关的属性 exportEnabled Boolean (0/1) 图表是否允许导出images/PDFs? exportShowMenuItem Boolean (0/1) 是否将导出图片等按钮出现在图表右键菜单中 exportFormats String

格式的列表图表将显示在上下文菜单中，同时为每一个标签。  
  
该属性的值应该分开键值对。分隔符字符将要采用的’|’（分字符）。该属性值的语法如下：  
KEY=Value\[|KEY=Value\]\*  
  
例如：自定义上下文菜单PNG和PDF格式。  
  
exportType=”PNG=Export as High Quality Image;JPG;PDF=Export as PDF File”exportAtClient Boolean (0/1) 导出到客户端还是服务器端 exportHandler String

在服务器导出方面而言，这指的是服务器端输出处理程序（已经可以使用的脚本，我们提供的路径）。  
  
在客户导出方面而言，这指的是DOM的组成部分的FusionCharts的导出是在你的网页嵌入，随着图表同上。exportAction ‘save’ or ‘download’ 在服务器端的情况下导出，行动指定是否导出的图像将被发送回客户端的下载，或者是否会在服务器上保存。 exportTargetWindow \_self or \_blank 在服务器端的情况下使用时，导出作为行动的下载，这个左派配置是否返回图片/ PDF格式将在同一窗口中打开作为附件下载（），或是否会打开一个新窗口。 exportCallback String

名称的JavaScript函数将被调用时返回进程的情况下导出成品：  
  
客户端的导出  
批量导出  
服务器端导出使用’保存’的行动exportFileName String 利用输出（导出）您可以指定此属性的名称（不包括扩展名）文件。 导出对话框配置相关的属性： showExportDialog Boolean (0/1) 是否要显示在捕获阶段的出口对话框。如果没有，开始捕获过程，但没有图表对话框可见。 exportDialogMessage String 该消息被显示在对话框中。默认为“捕捉数据：” exportDialogColor Hex Color 对话框背景颜色。 exportDialogBorderColor Hex Color 对话框前景颜色。 exportDialogFontColor Hex Color 对话框文本的字体颜色。 exportDialogPBColor Hex Color 对话框进度条的颜色。

简单XML案例：

&lt;chart yAxisName=’Sales Figure’ caption=’Top 5 Sales Person’ numberPrefix=’$’ useRoundEdges=’1′ bgColor=’FFFFFF,FFFFFF’ showBorder=’0′ **exportEnabled=’1′ exportAtClient=’1′ exportHandler=’fcExporter1′**&gt;  
&lt;set label=’Alex’ value=’25000′ /&gt;   
&lt;set label=’Mark’ value=’35000′ /&gt;   
&lt;set label=’David’ value=’42300′ /&gt;   
&lt;set label=’Graham’ value=’35300′ /&gt;   
&lt;set label=’John’ value=’31300′ /&gt;  
&lt;/chart&gt;