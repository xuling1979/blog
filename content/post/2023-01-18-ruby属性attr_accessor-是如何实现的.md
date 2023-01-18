---
title: Ruby属性attr_accessor是如何实现的
date: 2023-01-18T15:18:39.093Z
draft: false
tags:
  - Ruby
categories:
  - Ruby
---

最初看到`attr_accessor` 觉得很神奇。其实实现并没有很复杂，一言概之就是用了 `define_method`方法来定义一个方法。元编程的一种实现。
```ruby
class User
  def self.setup_accessor var
    define_method var do
      instance_varialble_get "@#{var}"
    end

    define_method "#{var}=" do |value|
      instance_variable_set "@#{var}", value
    end
  end

  setup_accessor :name
  setup_accessor :age

  def initialize name, age
    @name = name
    @age = age
  end

end

```
Ruby 中关于类的操作基本都是方法，几乎不会直接操作实例变量。
元编程+插值字符串 在运行时生成各种方法。
