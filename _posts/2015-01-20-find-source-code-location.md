---
title: Find source code location in Ruby
layout: post
---

### Why should I read source code?

Sometimes it's useful to dig into the source code of some methods. Wether it's lack of documentation or pure [curiosity](http://programmer.97things.oreilly.com/wiki/index.php/Curiosity_Killed_the_Cat,_but_It_Will_Make_You_Stronger). 

![curiosity]({{ site.url }}/assets/curiosity.jpg)

As curious as I am, I hate groping around thousands of lines of code to find the method I'm after. Thankfully since Ruby 1.9, an awesome little method called `#source_location` exists.

### Methods are objects!

As you know, in Ruby everything is an object. Even methods!

Let's have a look at the example class `Cat`:

*./cat.rb*

```ruby
class Cat
	def meow
		puts "Miaou" # This is a french cat.
	end
end
```

`:meow` is a method (instance of the `Method` class).

- `Cat.new.meow` calls the method and prints "Miaou"
- `Cat.new.method(:meow)` returns the `#meow` method

```bash
$ irb
irb(main):001:0> require './cat'
=> true
irb(main):002:0> Cat.new.meow
Miaou
=> nil
irb(main):003:0> Cat.new.method(:meow)
=> #<Method: Cat#meow>
```

### Method#source_location

You can find documentation about `:source_location` [here](http://ruby-doc.org/core-1.9.3/
Method.html#method-i-source_location).

**source_location → [String, Fixnum]**

```
Returns the Ruby source filename and line number containing this method or nil if this method was not defined in Ruby (i.e. native)
```


So all we have to do is call `#source_location` on the method we're interested in.

```bash
irb(main):004:0> Cat.new.method(:meow).source_location
=> ["/private/tmp/cat.rb", 4]
```

Easy! The `#meow` method is at the line 4 of the file `/private/tmp/cat.rb`

### A Rails example

I'm going to take a more useful example. Let's say we want to know more about the `has_secure_password` method from ActiveRecord.

```bash
$ rails c
Loading development environment (Rails 4.1.5)
irb(main):001:0> ActiveRecord::Base.method(:has_secure_password).source_location
=> ["/Users/arno/.rbenv/versions/2.1.2/lib/ruby/gems/2.1.0/gems/activemodel-4.1.5/lib/active_model/secure_password.rb", 44]
```

Happy reading ;)

### Limitations

Native Ruby methods are written in C and thus `#source_location` does not work with those methods and will return nil as stated in the documentation.

```
Returns [...] nil if this method was not defined in Ruby (i.e. native)
```

example:

```bash
$ irb
irb(main):001:0> "Miaou".method(:size).source_location
=> nil
```