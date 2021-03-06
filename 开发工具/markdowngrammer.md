1.Markdown 标题
=

第一种方式
-

一级标题标记的语法为  换行 + “=”

例如：

>一级标题内容为：“我是一级标题”

使用效果如下

我是一级标题
=

二级标题标记语法为 换行 + “-”

例如：

>一级标题内容为：“我是二级标题”

使用效果如下

我是二级标题
-


第二种方式
-

`#`                 一级标题

`##`                二级标题

`###`

`####`

`#####`

`######`            六级标题

使用效果如下

# 一级标题

## 二级标题

### 三级标题

#### 四级标题

##### 五级标题

###### 六级标题

2.Markdown 段落
=

换行
-  
换行的方式  两个空格[SP][SP] + 回车（直接回车需要空一行，也有类似效果，不够紧凑，比如前面的内容布局就是这样）

使用效果如下：

这是段落一的内容  
这是段落二的内容  

字体相关
-  
`*文字*`为斜体  
`_文字_`为斜体  
`**文字**`为粗体  
`__文字__`为粗体  
`**文字**`为粗斜体  
`__文字__`为粗斜体  

效果如下：  
*文字* 为斜体  
_文字_ 斜体  
**文字** 为粗体  
__文字__ 为粗体  
***文字*** 为粗斜体  
___文字___ 为粗斜体  

分割线  
-  
使用三个以上的*、_或者-实现（中间可以加入空格，但是不可以有其他的）  
效果如下：  
第一行
***    
第二行
___  
第三行  

-------  

删除线  
-  

文字两端使用两个波浪号~~ 

使用效果如下：  

~~删除的文字~~

脚注（与编辑器有关，不一定支持）
-

这是一个[^jiaozhu]的例子  

[^jiaozhu]：  补充说明文档，脚注  

3.列表
=  
markdown支持有序和无序列表
-  
无序列表使用*、+或者-作为标记，后面加空格以起效  
数字1，2，3...后面加.和空格表示有序列表  

使用效果如下：
* 第一项
* 第二项
* 第三项

1. 第一项
2. 第二项
3. 第三项


列表的嵌套
-  
在四列表的选项前面添加四个空格即可。

使用效果如下：  
1. 第一项
    - 第一项的子项1
    - 第一项的子项2

4.区块引用  
=
使用符号>  

使用效果如下
>引用文本内容part1  
>引用文本内容part2  
>引用文本内容part3  

嵌套使用（增加>数量）：  

>引用文本1
>>引用文本1中的嵌套内容1
>>>引用文本1中的嵌套内容1中的嵌套内容1


列表中使用区块引用（>前面需要加入四个空格）：  
1. 列表第一项
    >列表中的嵌套区块内容  
2. 列表第二项  

5.代码与代码块
=
对于单行的代码，直接在代码块的始末加符号`(esc下的第一个键)

`hello world`  

代码区块（使用```）  
```
public static void main(String args){
    system.out.print("hello world");
}
```


6.链接
=
使用格式  

`这是一个链接 [名称](地址)`

使用效果：  
这是一个链接 [菜鸟教程](https://www.runoob.com)  

高级使用方法
```  
这个链接用 1 作为网址变量 [Google][1]
这个链接用 runoob 作为网址变量 [Runoob][runoob]
然后在文档的结尾为变量赋值（网址）

[1]: http://www.google.com/
[runoob]: http://www.runoob.com/
```
对应效果：

这个链接用 1 作为网址变量 [Google][1]
这个链接用 runoob 作为网址变量 [Runoob][runoob]
然后在文档的结尾为变量赋值（网址）

[1]: http://www.google.com/
[runoob]: http://www.runoob.com/  

7.图片
=  
```  
![alt 属性文本](图片地址)
![alt 属性文本](图片地址 "可选标题")  
```  
使用效果：

![美图](../image/markdown/beauty.jpg)

![美图带说明的那种](../image/markdown/beauty.jpg "美图说明")  

也可以像使用链接一样，用更高级的方法来实现。

利用`<img>`可以实现对图片大小的控制
`<img src="./image/markdown/beauty.jpg" width="50%">`
使用效果如下：  
<img src="../image/markdown/beauty.jpg" width="50%">

8.表格  
=
表格的使用格式如下
```  
基本使用：
|  表头   | 表头  |
|  ----  | ----  |
| 单元格  | 单元格 |
| 单元格  | 单元格 |

对齐方式：
-: 设置内容和标题栏居右对齐。
:- 设置内容和标题栏居左对齐。
:-: 设置内容和标题栏居中对齐。

```  
使用 | 来分隔不同的单元格，使用 - 来分隔表头和其他行  
使用效果：
|  表头   | 表头  |
|  ----  | ----  |
| 单元格  | 单元格 |
| 单元格  | 单元格 |

| 左对齐 | 右对齐 | 居中对齐 |
| :-----| ----: | :----: |
| 单元格1 | 单元格2 | 单元格3 |
| a单元格2 | b单元格2 | c单元格2 |

9.html标签、转义字符与公式
=
```
目前支持的 HTML 元素有：<kbd> <b> <i> <em> <sup> <sub> <br>等   
Markdown 使用了很多特殊符号来表示特定的意义，如果需要显示特定的符号则需要使用转义字符，Markdown 使用反斜杠转义特殊字符：
\   反斜线
`   反引号
*   星号
_   下划线
{}  花括号
[]  方括号
()  小括号
#   井字号
+   加号
-   减号
.   英文句点
!   感叹号
当你需要在编辑器中插入数学公式时，可以使用两个美元符 $$ 包裹 TeX 或 LaTeX 格式的数学公式来实现。提交后，问答和文章页会根据需要加载 Mathjax 对数学公式进行渲染