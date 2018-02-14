---
title: 'Practical Vim - Registers '
tags:
  - vim
  - practical vim
date: 2018-02-12 23:08:06
---


## 複制貼上

- 將somethingInTheWay換成collection
```
collection = getCollection();
process(somethingInTheWay, target);
```

`yiw` `jww` `diw` `P` 失敗！

`yiw` `jww` `diw` `"0P` OK!!

`"0`是複制專用的暫存器，只有`y`操作會改變其值

- 如果是在插入模式時，也可以用`<C-r>0`來貼上

- 如果是HTML
```
<table>
  <tr>
    <td>Keystrokes</td>
    <td>Buffer Contents</td>
  </tr>
</table>
```

游標停在tr
`yap` `gP`

```
<table>
  <tr>
    <td>Keystrokes</td>
    <td>Buffer Contents</td>
  </tr>
  <tr>
    <td>Keystrokes</td>
    <td>Buffer Contents</td>
  </tr>
</table>
```

`gp` `gP`和`p` `P`類似，差別在插入後游標的位置在新的複制內容上

## 系統剪貼板

- `"+p`將外部程式中複制的內容貼到游標後（而且此方法在貼入多行程式碼時，不會受到autoindent的影響）
- `"+yy`將游標所在行的內容複制到系統剪貼板

## Macro冷知識

- `""` The Unnamed Register
- `"0` The Yank Register
- `"_` The Black Hole Register
- `"+` The System Clipboard
- `"*` Selection Registers
- `"=` The Expression Register
- `"%` Name of the current file
- `"#` Name of the alternate file
- `".` Last inserted text
- `":` Last Ex command
- `"/` Last search pattern

## Swap Two Words

```
I like tomato and banana.
```

`ft` `de`
`mm` **

`ww` `ve` `p`
`'m` **
`P`


```
I like banana and tomato.
```

## Macro

```
foo = 1
bar = 'a'
foobar = foo + bar
```

`qa` `A;<ESC>` `Ivar <ESC>` `q`
完成對第一次加上var和行尾加上;

```
var foo = 1;
bar = 'a'
foobar = foo + bar
```

然後 `j` `@a` `j@@`
```
var foo = 1;
var bar = 'a';
var foobar = foo + bar;
```
`@@`表示執行前一次的Macro

> 錄制Macro時確保每一條指令都可以被重覆執行

`:reg a`
看register a的內容


## 連續本文行上修改

Origin
```
1. one
2. two
3. three
4. four
```

Goal
```
1) One
2) Two
3) Three
4) Four
```

- 串行的方式
`qa` `f.r)` `w~j`
`3@a`

- 並行的方式
遇到其中一行不滿足規則會失敗，改用並行方式
```
1. one
2. two
// breaking line
3. three
4. four
```
`qa` `f.r)` `w~`
選擇`V`後
`:normal @a<CR>`

```
1) One
2) Two
// breaking line
3) Three
4) Four
```

- 查內容`:reg {char}`
- 追加內容`q{capital char}` 執行完`q`結束

## 批次修改檔案

例：給每個class加上外部的module
```
# description
class Animal
  # todo
end
```

`:args {file patten}` 讀入符合樣板的檔案
`:args`顯示參數列表內容

#### 並行
`:first`確保是第一個檔案

`qa`
`gg/class<CR>`
`O` `module Rank<ESC>`
`j>G`
`Go` `end<ESC>`
`q`

還不要儲存，因為等會要對所有文件做同樣的動作
`:e!`撒消修改
`:argdo narmal @a`
`:wa`

#### 串行

增加指令
`qA`
`:next`
`q`
`100@a`

100只是要比所有檔案數還多就可以，反正只要失敗就停止

## 疊代求值給列表編號

```
one
two
three
four
```

利用`:let`
`:let i=1`
`qa`
`I<C-r>=i<CR><ESC`
`:let i+=1`
`q`
`jVG`
`:normal @a`

```
1) one
2) two
3) three
4) four
```

`:echo i`可印出變數值

利用複制貼上再用`<C-a>`也是一種解法

## 修改Macro

利用`:put {char}`印出內容，修改後
`0"{char}y$` `dd` 寫回暫存器

