---
title: Practical Vim Patterns
tags: [vim, practical vim]
---

## 搜尋

"""
body   { color: #3c3c3c; }
a      { color: #0000EE; }
strong { color: #000; }
"""

Magic Search
`/#\([0-9a-fA-F]\{6}\|[0-9a-fA-F]\{3}\)'

Very Magic Search
`/\v#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})`

Hex Character Class
'/\v#(\x{6}|\x{3})'

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

## Lexical Illusion

```
I love Paris in the
the springtime.
```

兩個the在不同一行，但是很不容易發現這個錯誤
`/\v<(\w+)\_s+\1>`

`()`match後會給值，這裡是\1

`<>`是字串符合

`\_s`是空白或換行

## Swap Two Words

```
Andrew Neil
Drew Neil
```

`/\v(%(And|D)rew) (Neil)`
':%s//\2, \1/g'

```
Neil, Andrew
Neil, Drew
```

利用%()把字串存在\1，否則會變成And/D被存起來而已。

## 字的邊界

```
the probelm with these new recruits is that
they don't keep their boots clean.
```

`\v<the><CR>`

## 匹佩的邊界

```
Match "quoted words"
```

'/\v"[^"]+"<CR>' 匹佩包含"

`/\v"\zs[^"]+\ze"<CR>`

## 搜尋後游標移到匹配字尾

`/{word}/e`

## 呼叫搜尋歷史記錄

`/<C-p>`

## 自動補全

`<C-r><C-w>`

## 進階搜尋

```
This string contains a 'quotesd' word.
This string contains 'two' quoted 'words.'
This 'string doesn't make things easy.'
```

try 1. `/\v'.+'`

`.` match any char

try 2. `/\v'[^']+'`

但是 doesn't 柀match進來

try 3. `/\v'([^']|'\w')+'`

包含括號也被匹佩進來

try 4. `/\v'(([^']|'\w)+)'`

將匹佩的結果放進\1，接著

`:%s//"\1"/g`
單引號就變雙引號了

最後的結果也等同於
`:%s/\v'(([^']|'\w)+)'/"\1"/g`

## 計數匹佩

```
var buttons = viewport.buttons;
viewport.buttons.previous.show();
viewport.buttons.next.show();
viewport.buttons.index.hide();
```

#### :s

`/\<buttons\>`
`:%s///gn`

#### :vimgrep

`/\<buttons\>`
`:vimgrep //g %`

## * search in visual mode

書中提供如何實作 在visual mode搜尋所選擇的文字，但也可以用plugin
vim-visula-star-search
