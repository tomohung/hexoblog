---
title: Practical Vim - 2
date: 2018-01-06 14:30:48
tags: [vim, practical vim]
---



## 區分實際行和營幕行

如果一行字數太長發生wrap時就需要

在`j` `k` `0` `$`之前加上`g`就是營幕操作，即使是wrap也能夠有效移動

## 字串WORD和單字word

字串WORD：不含空格的連續字元串
單字word：字母、數字、底線組成的連續字元串

`w` `b` `e` vs `W` `B` `E`

```
e.g. we're going too slow
```
同樣要移動到going的開頭
- `wwwwwww`
- `WW`

## 單行搜尋

```
Find the first occurrence of {char} and move to it.
```

`f{char}` Forward to the next occurrence of {char}
`F{char}` Backward to the previous occureence of {char}
`t{char}` Forward to the character before the next occurrence of {char}
`T{char}` Backward to the character after the previous occurrence of {char}
`;`       Repeat the last character-search command
`,`       Reverse the last character-search command

`T`用倒退搜尋但是停在符合字元前的位置來理解

## 活用`t`
```
I've been expecting you, Mister Bond.
```
刪除`, Mister Bond`

`f,` `dt.`
```
I've been expecting you.
```

## 活用搜尋`/`

```
This phrase takes time
but eventually gets to the point.
```

1. `ft` `v/ge<CR>` `hd`
2. `ft` `d/ge<CR>`

方法2乾淨多了，但是刪掉的是只到要搜尋的字串前，和`v`模式不同比較不直覺

## Text Object Selection

```
var tpl = [
  '<a href="{url}">{title}</a>'
]
```

超棒的範例，看完後覺得以前編輯這種字串時自己像個笨蛋

1. 游標停在url的r上面
2. `vi}` url被選起，`i`表示inner
3. 之後都是v模式，所以可以省略。
4. `a"` "{url}"被選起，`a`表示around
5. `i>` a href="{url}"被選起
6. `it` {title}被選起，`t`表示tag
7. `at` `<a href="{url}">{title}</a>`被選起
8. `a]` []內容被選起，包含[]

而且即使游標沒停在內容上，也會自動向後搜尋第一個符合條件的位置

配合動作命令
`d{motion}` `c{motion}` `y{motion}`

如：
`ci"`
`cit`

- `iw` Current word
- `aw` Current word plus one space
- `iW` Current WORD
- `aW` Current WORD plus one space
- `is` Current sentence
- `as` Current sentnece plus one space
- `ip` Current paragraph
- `ap` Current paragraph plus one blank line

編輯時用`ciw`較好保留空白
刪除時`daw`較好去除多餘的空白

