---
title: Practical Vim - Getting Around Faster
tags:
  - vim
  - practical vim
date: 2018-01-25 07:04:08
---

更多操作上的快速鍵
<!-- more -->

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

> The search command is an exclusive motion.

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

## Mark標記

`m{char}` 用選定的字元{char}標記目前游標的位置
``{char}` 跳到{char}標記的位置

tip: `mm` ``m`常用標記

` `` ` 上次跳轉之前的位置

``.` 上次修改的位置

實際上用`'`也可以，比起`來說要好按多了
`'m`

## `%`開關括號間跳轉

將{}替換成[]

```
cities = {London Berlin New York}
```

```
利用f{% r] `` r[
```

```
cities = [London Berlin New York]
```
因為改完]之後就無法再用%跳回了，所以可以用` `` `或者是`<C-o>`跳回

處理成對符號蠻麻煩的，這邊真的要考慮用plugin

## Plugin for pair parentheses, brackets, quotes

#### [Auto Pairs](https://github.com/jiangmiao/auto-pairs)
快速處理成對符號，包含新增、刪除
`<M-e>`系列的快速鍵在iterm2上沒辦法用

#### [surround.vim](https://github.com/tpope/vim-surround)
快速新增、編輯成對符號在已有的字串上面

同樣將{}替換成[]

```
cities = {London Berlin New York}
```

`f{` `cs{[`

```
cities = [ London Berlin New York ]
```

注意左右多了空白

## 快速跳轉

- `<C-o>` `<C-i>`
- `:jumps`
- `'m`或``m` Jump to mark
- ``.` 指向最後一次修改的地方
- `gi` 立刻跳到最後一次修改的地方並進入插入模式，實用！
- 瀏覽大量文件後要跳回，先`mM`，查完後``M`跳回

#### Change list

- `u<C-r>`
- `:changes`
- `g;` `g,`

#### `gf` `<C-]`

實際上因為plugin幫我們把path設定好了，所以才能夠在檔案或tag之間跳轉
如: `vim-ruby` `vim-bundler`
