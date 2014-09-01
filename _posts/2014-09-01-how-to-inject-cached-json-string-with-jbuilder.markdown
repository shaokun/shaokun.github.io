---
layout: post
title:  "如何在Jbuilder中注入缓存的JSON文本"
cover:  cover.jpg
date:   2014-05-18 16:25:00
categories: rails json jbuilder
---

Jbuilder提供了方便、强大的DSL来生成JSON。在最新版本的Jbuilder(v2.x)中也提供了cache的API来提高性能。

在我的一个项目中，要求非常高的JSON生成效率。于是我尝试使用Jbuilder的cache API来实现。但是结果是性能反而下降了。仔细研究后，才发现Jbuilder的原理是各个节点要么是Array，要么是Hash，而它的cache也只是简单的缓存生成好的Array或者Hash，然后在merge到当前的JSON数据中。在我的项目中，需要序列化成JSON的数据都已经在内存里面了，所以缓存Array或者Hash，反而是增加了损耗。我需要找到的是直接在Jbuilder的DSL中注入JSON文本的功能。

我找到的方法原理是通过创建一个CompiledJson的Class，覆盖to_json的方法来直接返回JSON文本。

```ruby
require 'oj'

class CompiledJson
  def initialize(s); @s = s; end
  def to_json(*args); @s; end
  def to_s; @s; end

  undef_method :as_json
end

Oj.default_options = {:mode => :compat, use_to_json: true}

result = Jbuilder.new do |json|
  json.a CompiledJson.new('{x: "y"}')
end

puts result.target!
# => {"a": {x: "y"}}
```

Jbuilder的主要贡献者@rwz给出了更贴切的方法：

```ruby
# config/initializers/lol_json.rb
 
module ActiveSupport
  module JSON
    module Encoding
      class JSONGemEncoder
        BYPASS_JSONIFY = Set.new
        
        alias_method :original_jsonify, :jsonify
        
        def jsonify(value)
          BYPASS_JSONIFY.include?(value.class) ? value : original_jsonify(value)
        end
      end
    end
  end
end
 
# lib/compiled_json.rb
 
class CompiledJson
  def initialize(s); @s = s; end
  def to_json(*); @s; end
  def as_json(*); self; end
end
 
ActiveSupport::JSON::Encoding::JSONGemEncoder::BYPASS_JSONIFY << CompiledJson
 
# seems to be working
 
bar = CompiledJson.new("bar")
 
MultiJson.dump(foo: bar)               # => { "foo": bar }
{ foo: bar }.to_json                   # => { "foo": bar }
Jbuilder.encode{ |json| json.foo bar } # => { "foo": bar }
```

需要注意的是因为在Rails4.1之后，ActiveSupport改变了JSON的生成机制，所以两个方法都涉及到覆盖其默认的方式。

[https://gist.github.com/rwz/14c0d8a5187b69922bb4](https://gist.github.com/rwz/14c0d8a5187b69922bb4)
[https://github.com/rails/jbuilder/issues/204#issuecomment-54004919](https://github.com/rails/jbuilder/issues/204#issuecomment-54004919)
[https://github.com/ohler55/oj/issues/140](https://github.com/ohler55/oj/issues/140)