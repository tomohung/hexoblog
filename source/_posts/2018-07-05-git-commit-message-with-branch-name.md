---
title: 在git commit message加上branch name
date: 2018-07-05 00:00:12
tags: [git]
---

# Git commit with branch name

## 起因

來到新公司發現，在git commit的message最前面要加上branch name，一開始覺得幹這種事有點蠢，而且message的長度一下就暴增。不過後來發現其實rebase interactive的時侯蠻好用的，一眼就可以看到哪些commit是我想要rebase。

<!-- more -->
commit message格式看起來會像這樣

```
feat/todo_add_item: create new table item
```

雖然git commit後，vim有auto complete可以簡單打出branch name，不過久了之後人還是會想發懶，就上網查了一下，發現原來蠻多人有同樣的需求，而git也有對應的hook，也就是prepare-commit-msg可以來解決這個問題。

## 情境

需要考慮以下幾種情況

- git commit
  最普通的使用，直接前綴上branch name
- git commit --amend
  已經有前綴branch name了，所以不需要再加一次
- git rebase
  也是已經有branch name了，但是apply commit時無法取得branch name，而是 `* (no branch, rebasing master)` 的字眼

## 作法

打開git的專案，新增檔案在 `.git/hooks/prepare-commit-msg`

```bash
# .git/hooks/prepare-commit-msg

if [ -z "$BRANCHES_TO_SKIP" ]; then
  BRANCHES_TO_SKIP=(master develop test)
fi
BRANCH_NAME=`git branch | grep '^\*' | cut -b3-`
BRANCH_IN_COMMIT=$(grep -c "$BRANCH_NAME:" $1)
BRANCH_EXCLUDED=$(printf "%s\n" "${BRANCHES_TO_SKIP[@]}" | grep -c "^$BRANCH_NAME$")
REBASING=$(echo $BRANCH_NAME | grep 'rebasing')
FILE=`cat "$1"`

if [ -n "$BRANCH_NAME" ] && ! [[ $BRANCH_EXCLUDED -eq 1 ]] && ! [[ $BRANCH_IN_COMMIT -ge 1 ]] && [ -z "$REBASING" ]; then
  echo "$BRANCH_NAME: $FILE" > "$1"
fi
```

首先略過 `master develop test`這幾個。

```bash
if [ -z "$BRANCHES_TO_SKIP" ]; then
  BRANCHES_TO_SKIP=(master develop test)
fi
BRANCH_EXCLUDED=$(printf "%s\n" "${BRANCHES_TO_SKIP[@]}" | grep -c "^$BRANCH_NAME$")
```

取得branch name，`git branch` 列出所有branch並grep前綴*(表示為當前brach)，cut後得到乾淨的branch name

```bash
BRANCH_NAME=`git branch | grep '^\*' | cut -b3-`
```

BRACN_IN_COMMIT，檢查是否已經有branch name包含在message裡面，也就是`$1`。這會發生在`git commit --amend`時候需要檢查。

```bash
BRANCH_IN_COMMIT=$(grep -c "$BRANCH_NAME:" $1)
```

檢查rebasing

```bash
REBASING=$(echo $BRANCH_NAME | grep 'rebasing')
```

最後滿足所以條件下，把branch name前綴到message

```bash
FILE=`cat "$1"`
if [ -n "$BRANCH_NAME" ] && ! [[ $BRANCH_EXCLUDED -eq 1 ]] && ! [[ $BRANCH_IN_COMMIT -ge 1 ]] && [ -z "$REBASING" ]; then
  echo "$BRANCH_NAME: $FILE" > "$1"
fi
```

網路上有些解法是用

```bash
sed -i.bak -e "1s/^/[$BRANCH_NAME] /" $1
```

但這在mac os上跑`sed`會有問題，所以我改用

```bash
echo "$BRANCH_NAME: $FILE" > "$1"
```



## 結語

懶人沒有極限，從此以後不用怕打錯branch name了
