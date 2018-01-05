---
title: Practical Vim
date: 2018-01-05 16:03:58
tags: [vim, practical vim]
---

記錄閱讀其它人的Practical Vim筆記自己需要補強的地方
<!-- more -->

### 縮排

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

### 多行行尾加上;

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

### 搜尋重覆

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

### 取代確認

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

### 活用`.`

新增一行時如果在插入模式可以直接`<Cr>`
但是如果`<ESC>o`
可以用`.`重覆之前的動作

連續刪除6個單字
`6dw` vs `dw.....`
後者重覆風格所見即所得比較直覺

### 比較刪除單字

如果游標在行尾
```
The end is night
```

1. `dbx`
2. `bdw`
3. `daw`

三種操作裡面只有`daw`是可以用`.`反覆繼續刪除最後單字

另外`daw`能把空白同時消除，這種情況下`diw`就不適用了

### 計數操作

`<C-a>`可以在游標所在數字加1，`<C-x>`則是減1。另外如果不是在數字上，後自動向後找第一個數字操作，並且跳到該數字，沒有數字的話就沒作用

以前用tmux把`<C-a>`拿去當呼叫鍵了，沒發現這其實蠻實用的

```
.blog { background-position: 0px 0px }
```

`180<C-x>`
```
.blog { background-position: -180px 0px }
```

### 大小寫轉換

`~` 對字元大小寫反轉
`gUaw` `guaw` 對單字轉大寫/小寫
或是 `g~aw`

選擇模式對區塊操作
`gU` `gu`
轉大小寫

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

### 插入模式

`<C-h>` delete back one char
`<C-w>` delete back one word
`<C-u>` delete back to start of line
測試後發現這指令只對這次插入模式輸入的字有用，原先就有的字是無效的

### Insert Normal Mode

`<C-o>`從插入模式進入一般模式，執行完後馬上又返回插入模式

情境
`<C-o>zz`
可以把當前編輯行移至螢幕中間

### Register

```
Practical Vim, by Drew Neil
Read Drew Neil's 
```

`yt,`
`jA `
`<C-r>0`
`.<ESC>`

```
Practical Vim, by Drew Neil
Read Drew Neil's Practical Vim.
```

以前習慣所見所得，所以比較常用`v`選取後再用`y`複制，不過`yt,`或`y/abc`可以用更少的操作鍵來得到同樣的效果，需要刻意練習。

另外`<C-r>`呼叫register內容，不止在插入模式可用，命令模式command mode也可以

