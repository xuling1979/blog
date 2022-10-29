---
title: Scope 用法
draft: false
keywords:
  - scope
categories:
  - rails
tags:
  - rails
date: '2022-02-27T12:56:33.600Z'
---
### Proc.new 约等于 lambda
  1. 控制权順序不同：lambda 會將控制权丟回呼叫 method 本身，继续往下执行，而 Proc 的 return 则不会，是立即跳出。
  2. 检查参数： lambda 会对参数进行检查，如果是 nil 則會直接拋出 error，而 Proc 則是以 nil 当参数帶入。
  3. Scope 运用在 Rails 的 ActiveRecord Model，要获取资料时，都会使用 lambda 來進行，原因就是 lambda 更谨慎。
### 基本用法
  
  ``` ruby
  class Book < ApplicationRecord
    scope :with_author, -> { where(author: "CY") }
    scope :with_page_count, -> { where("page_count > 100") }
    scope :with_author, ->(Name) { where(author: name) }
    scope :with_page_count, ->(count) { where("page_count > ?", count) }
  end
  
  Book.with_author.with_page_count.last(3)
  寻找作者为 CY 且页数大于 100 的书，并取最后 3 本。
  ```
### Scope 和 Class method 的比较
  1. scope 的代码更简洁
  2. scope 返回值一定是 ActiveRecord::Relation，但 Class method 不一定。
### Default Scopes
  default_scope { where(published: true) }
  只要获取 book 数据时，都只会获取到 published 为 true 的 book.
  unscoped_book = Book.unscoped.find(book_id)
  想取消 default scope 时。
