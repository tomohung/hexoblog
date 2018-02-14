---
title: Practical Vim - Modes (Command-Line Mode)
tags:
  - vim
  - practical vim
date: 2018-01-23 00:09:09
---


篇幅有點長，獨立一個章節。
<!-- more -->

## 各種command line可用來編輯的指令

- :[range]delete [x]
- :[range]yank [x]
- :[line]put [x]
- :[range]copy {address}
- :[range]move {address}
- :[range]join
- :[range]normal {commands}     Execute Normal mode {commands} on each specified line
- :[range]substitute/{partten}/{string}/{flags}
- :[range]global/{patten}/[cmd]     Execute the Ex command [cmd] on all specified lines where the {patten} matches

## Command mode

命令模式的特點在於`能在多行上同時執行的能力`，不需要`j.j.`重覆執行
```
<a>one</a>
<a>two</a>
<a>three</a>
```

`Vjj` `:normal gUit` 對每一行都執行`gUit`
```
<a>ONE</a>
<a>TWO</a>
<a>THREE</a>
```

關鍵指令
`:normal`

- `:%normal A;` 在每一行結尾插入;
- `:%normal i//` 在每一行最前面插入//

dot `.` 也是可以用在normal mode
- :'&lt;,'&gt; normal .

Ex命令的地址和範圍
```
Symbol |  Address
--------------------------------
1      |  first line of the file
$      |  last line of the file
0      |  Virtual line above first line of file
.      |  line where the cursor is placed
'm     |  line containing mark m
'<     |  start of visual selection
'>     |  End of visual selection
%      |  the entire file
```

利用pattern

`:/<html>/,/<\/html>/p`

Offset
`:/<html>/+1,/<\/html>/-1p`

## 活用`:t`複制

```
Shopping list
  Hardware
    Buy new hammer
  Beauty
    Buy polish
    Buy nails
```

`:6t.`
回顧一下`.`表示line where the cursor is placed
所以上述指令是拷貝第6行到游標所在行的下面

```
Shopping list
  Hardware
    Buy nails
    Buy new hammer
  Beauty
    Buy polish
    Buy nails
```

其它組合技
`:t6`  Copy current line to just below line 6
`:t$`  Copy current line to the end of the file
'&lt;,'&gt;t0 Copy visual selected lines to the start of the file

實用技
'&lt;,'&gt;t$ Copy visual selected lines to the end of the file
現在寫本篇文章常要複制上面的程式碼到最下面

## 活用`:m`移動
```
Shopping list
  Hardware
    Buy nails
    Buy new hammer
  Beauty
    Buy polish
    Buy nails
```

把2-4行移到最下面
after visual selected `Vjj`
'&lt;,'&gt;m$

```
Shopping list
  Beauty
    Buy polish
    Buy nails
  Hardware
    Buy nails
    Buy new hammer
```

## 跳回上/前一次瀏覽的位址`<C-o>` `<C-i>`

即使是不同buffer也有效

## 把游標停在的字插入到命令列

這在取代時特別有用

例如游標目前停在tomohung這個字上面

`:s%/<C-r><C-w>/TomoHung/g`

可以把所有tomohung置換成TomoHung
按組合鍵`<C-r><C-w>`可以取代原來要打字的麻煩

或者是要把游標的字取代
`:%s//TomoHung/g`

## 叫出命令行窗口

`q/` open command-line window with history of searchs
`q:` open command-line window with Ex commands
`<C-f>` switch from command line mode to command line window

另外command line window是可以編輯的

## Shell

`:!ruby %` 呼叫ruby執行當前檔案，應該可以應用在寫測試時的呼叫

以csv為例
```
first name, last name, email
john, smith, john@example.com
drew, neil, drew@vimcast.com
jane, doe, jane@example.com
```

`:2,$!sort -t',' -k2` 從第二行到最後一行照column 2排序

```
first name, last name, email
jane, doe, jane@example.com
drew, neil, drew@vimcast.com
john, smith, john@example.com
```

## Run Multiple Ex Commands as Batch

Origin
```
<ol> <li>
  <a href="/episodes/show-invisibles/"> 
    Show invisibles
  </a> </li>
<li>
  <a href="/episodes/tabs-and-spaces/">
     Tabs and Spaces
  </a> </li>
</ol>
```

Modified to this
```
Show invisibles: http://vimcasts.org/episodes/show-invisibles/
Tabs and Spaces: http://vimcasts.org/episodes/tabs-and-spaces/
```

`:g/href/j`
`:v/href/d`
`:norm A: http://vimcasts.org`
`:%norm yi"$p`
`:%s/\v^[^\>]+\>\s//g`

## 存成script並且執行

filename: batch.vim
``` batch.vim
global/href/join 
vglobal/href/delete
%normal A: http://vimcasts.org 
%normal yi"$p 
%substitute/\v^[^\>]+\>\s//g
```

`:source batch.vim`

## 批次執行

```
$ vim *.html
:args
:argdo source batch.vim
```
