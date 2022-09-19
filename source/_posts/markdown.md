---
title: MarkDown 基础语法手册
category:
  - MarkDown
date: 2017-08-03 17:36:40
---

Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。Markdown的语法简洁明了、学习容易，而且功能比纯文本更强，因此有很多人用它写博客。<!-- more -->
![科技感](http://bpic.588ku.com//back_pic/03/68/74/1657b3cea5bb265.jpg!/fh/170/quality/90/unsharp/true/compress/true)

### 1. 斜体和粗体

使用 `*` 和 `**` 表示斜体和粗体。
>示例：
这是 *斜体*，这是 **粗体**。

### 2. 分级标题

>使用 `===` 表示一级标题，使用 `---` 表示二级标题。
你也可以选择在`行首加井号`表示不同级别的标题 (H1-H6)，例如：# H1, ## H2, ### H3，#### H4。

### 3. 外链接

使用 `[描述](链接地址)` 为文字增加外链接。
>示例：
hexo是一个快速、简洁且高效的博客框架。这是去往 [hexo](https://hexo.io/) 的链接。

### 4. 无序列表

可以使用 ` *，+，- ` 这三个中的任何一个表示无序列表。
>示例：
>- 无序列表项 一
- 无序列表项 二
- 无序列表项 三

### 5. 有序列表
使用 `数字和点` 如 X. 表示有序列表。
>示例：
1. 有序列表项 一
2. 有序列表项 二
3. 有序列表项 三

### 6. 文字引用
使用 `>` 表示文字引用
示例：
> 野火烧不尽，春风吹又生。

### 7. 行内代码块
使用 \`代码` 表示行内代码块。
>示例：
让我们聊聊 `HTML和CSS`。

### 8. 代码块
使用 `四个缩进空格` 表示代码块。
>示例：

    这是一个代码块，此行左侧有四个不可见的空格。

### 9. 插入图像
使用 **\!\[描述](图片链接地址)** 插入图像。
>示例：

![言叶之庭](http://www.bz55.com/uploads/allimg/140413/1-140413102017.jpg)

### 10.内容目录
在段落中填写 `[TOC]` 以显示全文内容的目录结构。

### 11. 删除线
使用 `~~` 表示删除线。

>示例：
~~这是一段错误的文本。~~

### 12. 加强的代码块
支持四十一种编程语言的语法高亮的显示，行号显示。
非代码示例：
```
$ sudo apt-get install vim-gnome
```
**Python** 示例：
```python
@requires_authorization
def somefunc(param1='', param2=0):
    '''A docstring'''
    if param1 > param2: # interesting
        print 'Greater'
    return (param2 - param1 + 1) or None

class SomeClass:
    pass

>>> message = '''interpreter
... prompt'''
```

**JavaScript** 示例：
``` javascript
/**
* nth element in the fibonacci series.
* @param n >= 0
* @return the nth element, >= 0.
*/
function fib(n) {
  var a = 1, b = 1;
  var tmp;
  while (--n >= 0) {
    tmp = a;
    a += b;
    b = tmp;
  }
  return a;
}

document.write(fib(10));
```

### 13. 表格支持

**示例** ：

|  项目  |  价格  | 数量  |
| :----: | :----: | :---: |
| 计算机 | \$1600 |   5   |
|  手机  |  \$12  |  12   |
|  管线  |  \$1   |  234  |

### 14. 待办事项 todo 列表
使用带有 `[ ] 或 [x] （未完成或已完成）`项的列表语法撰写一个待办事宜列表，并且支持子列表 **嵌套** 以及 **混用** Markdown语法，例如：

    - [ ] **Cmd Markdown 开发**
        - [ ] 改进 Cmd 渲染算法，使用局部渲染技术提高渲染效率
        - [ ] 支持以 PDF 格式导出文稿
        - [x] 新增Todo列表功能 [语法参考](https://github.com/blog/1375-task-lists-in-gfm-issues-pulls-comments)
        - [x] 改进 LaTex 功能
            - [x] 修复 LaTex 公式渲染问题
            - [x] 新增 LaTex 公式编号功能 [语法参考](http://docs.mathjax.org/en/latest/tex.html#tex-eq-numbers)
    - [ ] **七月旅行准备**
        - [ ] 准备邮轮上需要携带的物品
        - [ ] 浏览日本免税店的物品
        - [x] 购买蓝宝石公主号七月一日的船票

对应显示如下待办事宜 Todo 列表：

- [ ] **Cmd Markdown 开发**
    - [ ] 改进 Cmd 渲染算法，使用局部渲染技术提高渲染效率
    - [ ] 支持以 PDF 格式导出文稿
    - [x] 新增Todo列表功能 [语法参考](https://github.com/blog/1375-task-lists-in-gfm-issues-pulls-comments)
    - [x] 改进 LaTex 功能
        - [x] 修复 LaTex 公式渲染问题
        - [x] 新增 LaTex 公式编号功能 [语法参考](http://docs.mathjax.org/en/latest/tex.html#tex-eq-numbers)
- [ ] **七月旅行准备**
    - [ ] 准备邮轮上需要携带的物品
    - [ ] 浏览日本免税店的物品
    - [x] 购买蓝宝石公主号七月一日的船票
