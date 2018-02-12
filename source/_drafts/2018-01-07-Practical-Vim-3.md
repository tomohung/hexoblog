---
title: Practical Vim - 3
date: 2018-01-07 22:16:37
tags: [vim, practical vim]
---


## 搜尋匹配

#### `\V`
```
The N key searched backward...
the \v pattern switch (a.k.a. very magic search)
```

`.`是特殊字元可匹配任一字元，所以`/a.k.a`會匹配到`ackwa`
`\Va.k.a` very nomagic模式就沒這個問題了

#### `\v`

搭配`<` `>`
`\v<the>`可正確匹配到單字the

## 搜尋後游標移到匹配字尾

`/{word}/e`
