
---
title: 后向引用（Back-Reference）中的 $&
tags: [正则,书籍推荐]
categories: [其他]
toc: false
date: 2016-12-24 16:43:26
---

今天遇到一个从（shao）未（jian）遇（duo）到（guai）过的正则替换：
```javascript
'iixxxixx'.replace(/i+/g, '($&)') // complete match
```
虽然注释说明白了这是**complete match**的意思，但我还是不懂啊（以前只遇到过``$``加数字），还是搬砖找资料吧  ( ￣ー￣)

>送给你们一本讲解正则表达式的书，不谢！[《Regular Expressions: The Complete Tutorial》](http://www.regular-expressions.info)

翻到[replacematch](http://www.regular-expressions.info/replacematch.html)这一页：

>& all by itself represents the whole regex match, while $& is a literal dollar sign followed by the whole regex match, and \& is a literal ampersand.

看英文可能会明白一点，`&`表示 the whole regex match（or each one ?）

>$& is substituted with the whole regex match in the replacement text in the JGsoft applications, Delphi, .NET, JavaScript, and VBScript

这个符号（`$&`）只在 JGsoft 应用和 Delphi 等几种语言里可用。

>You can do this with $0 in the JGsoft applications, Delphi, .NET, Java, XRegExp, PCRE2, PHP, and XPath. \0 works with the JGsoft applications, Delphi, Ruby, PHP, and Tcl.

`$0`（JGsoft应用、PHP、Java、XPath等）、`\0`（Ruby等）也有相同效果，不同应用、语言里有不同的 `literal ampersand`。

完。

PS：上面提到的书并没有看完。
PS：今天还找到了[Speaking JavaScript: An In-Depth Guide for Programmers](http://speakingjs.com/)，我正在看这本书的中文翻译版，有英文版对照好棒 b(￣▽￣)d（HTML比PDF方便多了），开森  


