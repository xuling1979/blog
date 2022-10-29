---
title: 'Ruby on Rails enums 的使用 '
slug: ruby-on-rails-enums-的使用-
description: how to use ruby on rails enum
author: xulin
date: '2022-02-27T01:11:42.231Z'
lastmod: 2019-08-22T15:20:28.000Z
draft: false
tags:
  - rails
categories:
  - rails
---

### 应用场景
  有些表的字段内容不会经常变更，而且只有几项内容，没有必要新建一个表，用枚举 enum 就挺好。
  > Enum 就是 Rails 用来消灭魔鬼数字的工具。

### 如何使用
#### 数据库字段准备
  枚举类型是 integer，不是 string；使用 integer 来索引查找一个状态。
  举个例子：
`  rails g scaffold Post title content:text status:integer `
在 rails db:migrate 前，设置一个缺省值：
`t.integer :status, default: 0`

#### 声明
``` ruby
  class Post < ApplicationRecord
    enum :status, [:published, :draft, :archived]
    enum :status, { published: 0, draft: 1, archived: 2 }
    enum :status, { published: 0, draft: 1, archived: 2 }, scopes: false #有时候你不想用默认的缺省值，就可以加上这个
    enum :status, { published: 0, draft: 1, archived: 2}, default: :published
  end
```
注意：如果用数组，不要随意更换值的顺序，有新值加在后面；所以用 hash 更好，更容易控制且不出问题。

#### enums 内建的方法
按照上面的方式声明后，会获得一系列的 helper method.

``` ruby
  post.published! # => sets post status to 0
  post.published? # => true
  post.status # => "published"

  post.draft! # => sets post status to 1
  post.draft? # => true
  post.status # => "draft"

  post.archived! # => sets post status to 2
  post.archived? # => true
  post.status # => "archived"
```
#### 用在 ActiveRecord 就很 nice 了
```ruby
  # examples of a published ActiveRecord query
  Post.where(status: :published)
  Post.where.not(status: :published)
```
#### Prefix/Suffix 前缀和后缀
这个为了解决不同 enum 的值不能同名这个问题。
```ruby
  enum status: [:temporary, :active, :deleted]
  enum admin_status: [:active, :super_admin]

```  
这个就会报错，加上前缀后缀就不会了。

```ruby
  class Post < ApplicationRecord
    enum :status, { published: 0, draft: 1, archived: 2}, suffix: true
  end
  post.published_status!
  post.published_status?

  class Post < ApplicationRecord
    enum :status, { published: 0, draft: 1, archived: 2}, prefix: :posts
  end
  post.posts_published!
  post.posts_published?
```
#### i18n 翻译
使用这个：[enum_help](https://github.com/zmbacker/enum_help)
``` ruby 
  class Order < ActiveRecord::Base
    enum status: { "nopayment" => 0, "finished" => 1, "failed" => 2, "destroyed" => 3 }

    def self.restricted_statuses
      statuses.except :failed, :destroyed
    end
  end
  order = Order.first
  order.update_attribute :status, 0
  order.status
  # > nopayment
  order.status_i18n # if you have an i18n file defined as following, it will return "未支付".
  # > 未支付
```
i18n 文件
```yml
# config/locales/model/order.zh-cn.yml
zh-cn:
  enums:
    order:
      status:
        finished: 完成
        nopayment: 未支付
        failed: 失败
        destroyed: 已删除
```

#### 参考
这篇写得更好：
[Rails 关于在 Rails Model 中使用 Enum (枚举) 的若干总结](https://ruby-china.org/topics/28654)
