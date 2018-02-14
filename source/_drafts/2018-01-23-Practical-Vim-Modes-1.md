---
title: Practical Vim - Modes
tags:
  - vim
  - practical vim
date: 2018-01-23 00:10:01
---


Vim Practical Part I，主要是一些基本的編輯操作

<!-- more -->
## 縮排

```
Line one
Line two
Line three
Line four
```

`v`一次選2-4排後按`>`向右縮排，按`.`重覆向右縮排

```
Line one
    Line two
    Line three
    Line four
```

## 多行行尾加上;

```
var foo = 1
var bar = 'a'
var foobar = foo + bar
```

移至行尾`$`，再選取所有行，`VG` or `Vjj`。
`A;`加入;後`<ESC>`，即可見所有行尾都有加上;

```
var foo = 1;
var bar = 'a';
var foobar = foo + bar;
```

另一種
`A;`之後，按`j.`重覆在第二行，以此類推

```
var foo = 1;
var bar = 'a';
var foobar = foo + bar;
```

## 大寫熱鍵

大寫鍵實際上是兩鍵的組合
C -> c$ 刪除至行尾開始編輯
s -> cl
S -> ^C 整行刪除開始編輯
I -> ^i
A -> $a
o -> A<CR>
O -> ko

## 搜尋重覆

```
var foot = "method("+argument1+","+argument2+")";
```

`f+` `s + ` 重覆`;.`
```
var foot = "method(" + argument1 + "," + argument2 + ")";
```

or
`V` `:s/+/ + /g`
```
var foot = "method(" + argument1 + "," + argument2 + ")";
```

## 取代確認

```
We're waiting for content before the site can be published.
If you are content with us, let's go ahead.
```

`Vj` `:s/content/copy/gc`
可以在替換前一一尋問是否替換
```
We're waiting for copy before the site can be published.
If you are copy with us, let's go ahead.
```

## 活用`.`

新增一行時如果在插入模式可以直接`<Cr>`
但是如果`<ESC>o`
可以用`.`重覆之前的動作

連續刪除6個單字
`6dw` vs `dw.....`
後者重覆風格所見即所得比較直覺

## 比較刪除單字

如果游標在行尾
```
The end is night
```

1. `dbx`
2. `bdw`
3. `daw`

三種操作裡面只有`daw`是可以用`.`反覆繼續刪除最後單字

另外`daw`能把空白同時消除，這種情況下`diw`就不適用了

## 計數操作

`<C-a>`可以在游標所在數字加1，`<C-x>`則是減1。另外如果不是在數字上，後自動向後找第一個數字操作，並且跳到該數字，沒有數字的話就沒作用

以前用tmux把`<C-a>`拿去當呼叫鍵了，沒發現這其實蠻實用的

```
.blog { background-position: 0px 0px }
```

`180<C-x>`
```
.blog { background-position: -180px 0px }
```

## 大小寫轉換

`~` 對字元大小寫反轉
`gUaw` `guaw` 對單字轉大寫/小寫
或是 `g~aw`

選擇模式對區塊操作
`gU` `gu`
轉大小寫

shorthand
`gUgU` -> `gUU`

```
test paragraph for convert upper and lower.
```

`VgU` 整行轉大寫

```
TEST PARAGRAPH FOR CONVERT UPPER AND LOWER.
```

然後 `guip` or `guap` 當前段落轉回小寫
```
test paragraph for convert upper and lower.
```

## 插入模式

`<C-h>` delete back one char
`<C-w>` delete back one word
`<C-u>` delete back to start of line
測試後發現這指令只對這次插入模式輸入的字有用，原先就有的字是無效的

## Insert Normal Mode

`<C-o>`從插入模式進入一般模式，執行完後馬上又返回插入模式

情境
`<C-o>zz`
可以把當前編輯行移至螢幕中間

## Paste from Register

```
Practical Vim, by Drew Neil
Read Drew Neil's
```

`yt,`
`jA `
`<C-r>0`
`.<ESC>`

register 0是y的預設位址

```
Practical Vim, by Drew Neil
Read Drew Neil's Practical Vim.
```

以前習慣所見所得，所以比較常用`v`選取後再用`y`複制，不過`yt,`或`y/abc`可以用更少的操作鍵來得到同樣的效果，需要刻意練習。

另外`<C-r>`呼叫register內容，不止在插入模式可用，命令模式command mode也可以
以前都傻傻的回到Normal mode再用p貼上

## 利用Register做運算

`<C-r>=^6*35<CR>`
可產生210的結果

## 用編碼插入非常用字元

這應該很少機會用到

`<C-v>123` {
`<C-v>u1234` ሴ
`<C-v><CR>` 
`<C-k>35` ⅗

ga可以顯示字元的編碼，例如：
```
<碼> 30908, Hex 78bc, Octal 74274
```

## 替換模式`R`

```
Typing in Insert mode. But in Replace mode.
```

將`mode. But`換成`mode, but`
`f.` `R` `, b<ESC>`
```
Typing in Insert mode, but in Replace mode.
```

## `R` and `gR`

`R`取代的是按鍵，而`gR`取代的是字元間隔，最明顯的差別就是tab鍵

假設tapstop = 8，那麼
`tab` `a` `tab` `b`
在`Rxy<ESC>`之後是
`x` `y` `tab` `b`

而在`gRxy<ESC>`之後是
`x` `y` `tab` `a` `tab` `b`

這個通常也遇不到，因為寫程式會把tab鍵直接等價於n個空白鍵了

## Visual Mode

`v` 字元選擇
`v` 行選擇
`<c-v>` 區塊選擇

這些是基本的，不過下面技巧實用

`gv` 重新選擇上一次選擇結果
`o` 跳轉至選擇區另一端

## 盡量使用操作符號而不是可視命令

所見即所得比較直覺，但是對`.`支持度較低
```
<a>one</a>
<a>two</a>
<a>three</a>
```

`vit` `U`
再重覆 `j.j.`

```
<a>ONE</a>
<a>TWO</a>
<a>THRee</a>
```

注意最後一行結果是`THRee`

但如果是用`gUit` `j.j.`
```
<a>ONE</a>
<a>TWO</a>
<a>THREE</a>
```
這個結果才是預期的

## 畫表格

這個蠻酷的，雖然寫程式應該也少機會用到

```
Chapter       Page
Normal mode      1
Insert mode      2
Visual mode      3
```

移到中間的空白處`<C-v>jjj` `r|`
```
Chapter     | Page
Normal mode |    1
Insert mode |    2
Visual mode |    3
```

`yyp` `Vr-`
```
Chapter     | Page
------------------
Normal mode |    1
Insert mode |    2
Visual mode |    3
```
