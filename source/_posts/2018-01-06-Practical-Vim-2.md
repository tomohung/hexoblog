---
title: Practical Vim - 2
date: 2018-01-06 14:30:48
tags: [vim, practical vim]
---

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

## 管理緩沖區

- `:ls` buffer list

```
1 %a + "./source/posts/2018-01-05-Practical-Vim.md" line 427
3 #    "~/.vimrc"                     line 1
```

- `%`是當前的可見緩沖區
- `#`表示是上一次打開的緩沖區
- `:bn` `:bp` 上下切換
- `<C-^>` 快速切換目前和前次緩沖，實用!!不過這個快速鍵不順手
- `:bdelete` ex: `:bd 1 2 4`

## 用參數列表管理緩沖區

`:args **/*.md` 把當前目錄下所有副檔名為md的都讀入緩沖區，包含所有子資料夾

## 窗口管理

- `<C-w>v` `:vs` `:vsp`
- `<C-w>s` `:sp`

以前用`:vsp` `:sp`不過現在覺得`<C-w>v` `<C-w>s`更順手

- `<C-w>o` only，只保留當前視窗
- `<C-w>c` close，關閉當前視窗

## Tab管理

對Tab一直沒有愛，所以就跳過了

## `:edit`打開文件

`:e %<Tab>` 展開`%`變當前工作路徑
`:e %:h<Tab>` 展開`%:h`變當前工作資料夾路徑

tip，在vimrc加入
```
cnoremap <expr> $$ getcmdtype() == ':' ? expand('%:h').'/':'%%'
```
每次輸入`%%`直接展開工作目錄路徑

## 使用`:find`找文件

`:find`可以找文件而不需輸入完整路徑，設置path使其威力更強大

```
set path+=**
```

加上`*`可以有類似fuzzy serach的效果，如
`:find *practical*`

## 使用netrw管理文件

以前只看別人推薦就直接裝nerdtree來用，卻完全沒有用過內建的netrw，這次強迫自己習慣用這個，其實大部份的操作應該都能滿足
另一個好處是不會換台電腦就什麼都不能用，盡量減少對plugin的依賴

從shell打開
`vim .`

從編輯器內打開，打開後是在工作目錄
`:e .` 或 `:e.`

不過`E`比較靈活些
`:E` 打開後是目前檔案的目錄
`:E.` 打開後是工作目錄

打開後同樣可以用`<C-o>` `<C-i>`返回或進去
