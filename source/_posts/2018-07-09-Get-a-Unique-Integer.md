---
title: Get a Unique Integer
date: 2018-07-09 06:33:16
tags: [postgres, rails]
---

# Get a Unique Integer in Database Level

## 需求

剛好在前後公司看到類似的需求，因為需要除了Pirmary Key之外的整數來當作給人閱讀用的ID，原先的作法是
<!-- more -->

```ruby
# 資料record，有一個欄位integer
validate :integer, uniqueness: true

while true
  record.integer = Random.rand(6)
  break if record.save
end
```

實際上這個作法有兩個問題

- 最直覺的是當6位數用掉大部份之後，為了找到還沒用過的數字會在while loop裡面重覆很多次，理論上用光之後會陷入無窮迴圈。

- 另一個是其實Rails uniqueness validation驗証資料是否在，其實是分成兩個動作

  1) Record.exist? 2) Record.save
  所以如果1的結果回傳false，但在production的環境下，有另一個instance也在做一樣的事情，這個時候就有可能會存兩筆一樣的資料。

  可以參考[ThoughtBots這篇文章](https://robots.thoughtbot.com/the-perils-of-uniqueness-validations)

## 作法

之前是用mysql，我唯一能想到的方法是在迴圈中，如果驗証失敗的話，就增加rand的位數，這樣可以避免上述第一個問題。不過並不算是完整的解決問題。

後來這家公司是用postgresql，而有提供[SEQUENCE](https://www.postgresql.org/docs/9.5/static/sql-createsequence.html)功能，利用這個功能在database level上我們就可以取得一個unique integer。

```ruby
# migration
class CreateUniqueInteger < ActiveRecord::Migration
  def up
    execute <<-SQL
      CREATE SEQUENCE integer START 100001; # 可以指定整數從100001開始
    SQL
  end

  def down
    execute <<-SQL
      DROP SEQUENCE integer;
    SQL
  end
end
```

會在postgresql中會建立一個叫做`ingeter`的sequence。因為這不是Rails原生的功能，必需用`up/down` 來自行控制migration/rollback

接著在model內要取得unique integer時，就可以

```ruby
def get_unique_integer
  uid = ActiveRecord::Base.connection.execute("SELECT nextval('integer');").get_value(0, 0)
end
```

每一次`SELECT nextval('integer');`都會拿到下一個數字，所以就能夠確保拿到的是unique integer。

利用`before_validation`來自動填入

```ruby
before_validation: :set_integer

def set_integer
  self.human_readable_id ||= get_unique_integer
end
```

然後`human_readable_id`這個欄位，記得再上index unique確保存入database時的確是unique的。

## 優缺點

這樣做上述的兩個問題都得到了解答，不過仍要注意的是，這個作法並不能保証取得的是`連續`的整數，因為只要呼叫`nextval`數字就會跳號，它並不會知道你有沒有使用這個數字。也就是說如果出現rollback數字還是會跳號。


