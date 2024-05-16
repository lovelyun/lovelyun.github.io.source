---
title: Markdown常用语法手册
date: 2018-10-12 11:09:37
categories: doc
---

## 常用语法
### 1. 斜体和粗体
这是 *斜体*，这是 **粗体**。

### 2. 分级标题
在行首加井号表示不同级别的标题 (H1-H6)，例如：## H2, ### H3

### 3. 外链
这是去往 [本人博客](https://lovelyun.github.io/) 的链接。

### 4. 无序列表
- 无序列表项 一
- 无序列表项 二

### 5. 有序列表
1. 有序列表项 一
2. 有序列表项 二

### 6. 文字引用
> - 整理知识，学习笔记
> - 发布日记，杂文，所见所想
---
> 野火烧不尽，春风吹又生。

<div class="tip">
野火烧不尽，春风吹又生。
</div>

### 7. 行内代码块
让我们聊聊 `html`。

### 8. 代码块
```javascript
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
```

### 9. 插入图像
![我的头像](https://avatars0.githubusercontent.com/u/11694024?s=460&v=4)


### 10. 删除线
~~这是一段错误的文本。~~

### 11. 绘制表格

| Syntax | Description |
| --- | ----------- |
| Header | Title |
| Paragraph | Text |

使用`:`对齐
| Syntax      | Description | Test Text     |
| :---        |    :----:   |          ---: |
| Header      | Title       | Here's this   |
| Paragraph   | Text        | And more      |

## 参考文档
[Markdown 语法速查表](https://markdown.com.cn/cheat-sheet.html#%E5%9F%BA%E6%9C%AC%E8%AF%AD%E6%B3%95)
