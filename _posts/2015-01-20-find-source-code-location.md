---
title: Find source code location in Ruby
layout: post
---

### Do I really need to?

Whether it's lack of documentation or pure [curiosity](http://programmer.97things.oreilly.com/wiki/index.php/Curiosity_Killed_the_Cat,_but_It_Will_Make_You_Stronger), sometimes you need to dig into the source code of some methods to understand what it does or how it works.

![curiosity]({{ site.url }}/assets/curiosity.jpg)

As curious as I am, I hate sifting through thousands of lines of code to find the method I'm after. Thankfully a nifty little method called `#source_location` exists (Ruby 1.9+).

### Method#source_location

As you know, in Ruby everything is an object. Even methods!

Let's have a look at the example class `Cat`:

*./cat.rb*

```ruby
class Cat
	def meow
		puts "Miaou" # It's a french cat.
	end
end
```

```bash
$ irb
irb(main):001:0> require './cat'
=> true
```

- As you know, `#meow` is a method that you can call like this:

```bash
irb(main):002:0> Cat.new.meow
Miaou
=> nil
```

- But it's also an instance of the `Method` class that you can manipulate like this:

```bash
irb(main):003:0> Cat.new.method(:meow)
=> #<Method: Cat#meow>
```

So all we have to do now is to call `#source_location` on the Method object!

```bash
irb(main):004:0> Cat.new.method(:meow).source_location
=> ["/private/tmp/cat.rb", 4]
```

And it tells us the `#meow` method is defined at line 4 of the file `/private/tmp/cat.rb`

You can find documentation about `:source_location` [here](http://ruby-doc.org/core-1.9.3/
Method.html#method-i-source_location).

**source_location → [String, Fixnum]**

```
Returns the Ruby source filename and line number containing this method or nil if this method was not defined in Ruby (i.e. native)
```

*NB: `#method` needs to be called on the instance of a class if the argument is an instance method and on the class itself if the argument is a class method*

### Real life example

Let's say we want to know more about the `has_secure_password` method from Rails ActiveModel.

```bash
$ rails c
Loading development environment (Rails 4.1.5)
irb(main):001:0> ActiveRecord::Base.method(:has_secure_password).source_location
=> ["/Users/arno/.rbenv/versions/2.1.2/lib/ruby/gems/2.1.0/gems/activemodel-4.1.5/lib/active_model/secure_password.rb", 44]
```

There you go, the method is defined at line 44 of the file `[...]/.rbenv/versions/2.1.2/lib/ruby/gems/2.1.0/gems/activemodel-4.1.5/lib/active_model/secure_password.rb`

### Limitations

Native Ruby methods are written in C and thus `#source_location` does not work with those methods and will return `nil` as stated in the documentation.

```
Returns [...] nil if this method was not defined in Ruby (i.e. native)
```

example:

```bash
$ irb
irb(main):001:0> "Miaou".method(:size).source_location
=> nil
```

Have fun & ...

![read all the source code]({{ site.url }}/assets/read_all_the_source_code.jpg)

