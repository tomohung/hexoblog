---
title: Mastering Git
date: 2018-03-26 10:11:40
tags: [git]
---

# Git Restart
算是在upcase看完mastering git 的心得，再參雜一些其它自己的經驗。

<!-- more -->

## Git Context in Prompt

整天和終端機為伍，所以有一個好的狀態顯式就非常重要了。
zsh theme可以選擇那些有顯示git的狀態，在寫程式的過程就會有所幫助。

之前我是用[ys](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes#ys)，最近改用[pure](https://github.com/sindresorhus/pure)更簡潔

選一個theme來用吧

## Always Be Committing

任何小的變動都可以commit，因為這些commit在之後都能夠重新整理，重點是只要有commit的變化，就會留下記錄，不用害怕救不回來。

## Git Stash for Safety

`git stash`
可以把目前的檔案變化先儲存到倉庫中，所以遇到緊急情況需要先把目前程式碼保存起來，或著是要切換到另一個分支時就會有用處。
預設是只會記錄有變化檔案，所以這時候參數`-u` include untracked 就有用處了

```
$ git stash -u
```

## The Reflog

因為任何的操作在git上都是不可變的，所以即使是force push在本機端都會留下記錄，這時侯`git reflog`就派上用場了。

也可以針對特定的分支

```
$ git reflog feature/branch
```

找到想救回的分支hash，然後

```
$ git reset --hard 09827382
```

## Git Log

裝完oh-my-zsh的[git plugin](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh)其實就已經有一些便利的指令可以用

`glog`只顯示commit log一行，顯示remote and local branch，顯示分支圖
```
glog: aliased to git log --oneline --decorate --graph
```

列一些我自已常用的

```
alias ga='git add'
alias gaa='git add --all'
alias gapa='git add --patch'

alias gb='git branch'
alias gba='git branch -a'
alias gbd='git branch -d'

alias gc='git commit -v'
alias gc!='git commit -v --amend'
alias gcn!='git commit -v --no-edit --amend'

alias gcb='git checkout -b'

alias gclean='git clean -fd'

alias gcm='git checkout master'
alias gcd='git checkout develop'
alias gco='git checkout'

alias gd='git diff'
alias gdca='git diff --cached'

alias glol="git log --graph --pretty='%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
alias glog='git log --oneline --decorate --graph'

alias gf='git fetch'
alias gl='git pull'
alias gm='git merge'
alias gp='git push'

alias grb='git rebase'
alias grbi='git rebase -i'

alias gstc='git stash clear'
alias gstd='git stash drop'
alias gstl='git stash list'
alias gstp='git stash pop'

alias gst='git status'
```
## Amending Commits
如果忘了加檔案，可以用

```
$ git add Gemfile.lock
$ git commit --amend --no-edit
```
也就是上述的`gcn!`

如果是想修改commit message，可以用

```
$ git commit --amend
```

也就是`gc!`

## Undo Changes

```
$ git checkout .
```
把所有修改都復元

## Undo a Commit

```
$ git reset --soft HEAD^
```

`HEAD`為當前commit，所以`HEAD^`為HEAD前一個commit。

## Canceling an Edit

不輸入任何的文字就跳出可以取消這次的commit，但也可以利用`:cq`來跳出。
因為git會辨識exit code為`0`來確認這個commit是有效的。

## Add Patch

```
$ git add --patch
```

利用這個指令可以選擇哪些hunk需要commit，進入互動介面後，`h`可叫出help，重要的是`s`可以把hunk再細分。

## Cherry Pick & Reset Hard

如果發現commit在錯誤的分支上，運用cherry-pick加上reset --hard的組合技

```
$ git checkout sanitize-search-query
$ git cherry-pick origin/master..master
```
把`origin/master..master`差別的commits放到`sanitize-search-query`分支上。

```
$ git checkout master
$ git reset --hard origin/master
```
將master分支reset成origin/master。

## Interactive Rebase & Squash

Rebase之後是個好機會重新整理commits，反正等會也是要force push。

假設rebase後有三個commits

```
$ git rebase -i master
```
進入互動介面後

```
pick be891be WIP add forum answer to FAQ
s c0a3941 Remove line in README
s 009ac91 Add other file
```

把後面兩個檔案從`pick`改成`s`，就可以只留下第一個commit的記錄。

## Git Objects

在git中，總共有三種objects

* Blobs
包含了檔案內容的變化

* Trees
包含連結的blobs, permission, subdirectories

* Commits
包含commit hash, parent commit, metadata

commit <- parent(s)
|
|->tree <- subtree, mode, name
|
|->blob

## Git Branches/Tags

#### Local branches
`.git/refs/heads`

#### Tags
`.git/refs/tags`

#### Remote branches
`.git/refs/remote`

可能包含不同來源

```
$ tree .git/refs
.git/refs
├── heads/
|   ├── deck-last-attempt
|   ├── master
|   ├── ... (truncated)
|   └── welcome-trail
├── remotes/
│   ├── origin
│   │   ├── HEAD
│   │   ├── cjt-north-star-metric
│   │   ├── master
│   │   ├── mg-button-colors
│   │   └── ... (truncated)
│   ├── production
│   │   └── master
│   └── staging
│       ├── dashboard-staging
│       ├── ... (truncated)
│       └── master
└── tags/
    └── v0.1
  ```

#### HEAD Object
永遠只有一個HEAD，因為這就是指到當前分支的commit

```
.git/HEAD
ref: refs/heads/master
```

## Verbose Checkout
一般建分支

`git checkout -b new-branch`

但其實可以指定base branch

`git checkout -b new-branch master`
用master分支來建立新的分支new-branch

## Checking Out a File

`$ git checkout master -- app/Gemfile`

double dashes -- 是為了避免模糊定義，參考[這裡](https://stackoverflow.com/questions/13321458/meaning-of-git-checkout-double-dashes)

##  幾種分支何併方法

- Fast Forward Merge
預設可行的話merge就會以Fast Forward的方式進行，保持分支的乾淨。
- Non Fast Forward Merge
保留feature branch的commit，經驗上合併到stable分支時會用到，因為revert時可以整個feature一併作用。
- Rebasing
開發後，原先base分支也有變化，利用rebase讓目前開發的分支更新。
- Interactive Rebase
其中Squash讓多餘的commits合併一起，是個重新整理的好機會。

## Hub
[hub](https://hub.github.com/)
> hub is a command-line wrapper for git that makes you better at GitHub.

小技巧，在github頁面上按`y`，可以得到perminent link。
https://help.github.com/articles/getting-permanent-links-to-files/

## Vim - Fugitive
[fugitive](https://github.com/tpope/vim-fugitive)讓vim也能直接操作git。

`:Gblame`看到爛code想知道要罵誰用這就對了

`:Gdiff`

`:Gread`等同於`git checkout <file>`

`:Gremove`包含幾個操作

- 移除buffer
- 從working directory刪除檔案
- 執行`git rm`

`:Gmove`

`:Gmove full/path/to/new-filename`

`:Gstatus`

常用指令

- `D` open diff to review
- `-` stage or unstage file
- `<C-n>` `<C-p>` Jump to next/previous file
- `cc` commit

## Github

協作是不可避免的，所以得有些基本技巧先熟知

- Open PR (Pull Request)
  - 提供上下脈絡
  - Why?
  - Other solution?
  - Any assumptions?
- 截圖或gif ([licecap](https://www.cockos.com/licecap/))
- code review ([thoughtbot guide to code review](https://github.com/thoughtbot/guides/tree/master/code-review))

## 合併分支

```
$ git checkout master
$ git merge -
```

`-`表示前一次切換的分支名

## 刪除分支

刪除local

```
$ git branch -d <branch>
```

刪除遠端

```
$ git push origin --delete <branch>
```

不過遠端上可以在github PR page上操作會比較簡單。

## 其它資源

- [git ready](http://gitready.com/)
- [pro git](https://progit.org/)
- [git internal pdf](https://github.com/pluralsight/git-internals-pdf)
