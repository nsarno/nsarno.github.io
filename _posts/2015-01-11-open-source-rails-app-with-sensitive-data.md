---
title: Open source a rails app with sensitive data
layout: post
---

## Some context

I recently open-sourced one of my personal projects on my Github account. A few reasons for that:

- I wanted to showcase some of my work.
- a lot of awesome tools like [Codeship](https://codeship.com) or [Code Climate](https://codeclimate.com) are free for open source projects which is pretty neat, given they're usually pretty expensive.

I'm in a very classic situation where the code of my app is in a private repo on [Github](https://github.com/), it is hosted on [Heroku](https://www.heroku.com/) and I monitor it on [NewRelic](http://newrelic.com/).

Until then, I never cared too much about protecting any data as I used a private repo (You should still be careful of what you push on the internet even if it's supposed to be private, but at that point of my project there was not much that needed to be protected anyway).

One may think open-sourcing a rails app is as simple as pushing the "Make public" button, and you couldn't be more wrong.

## Why you need to care about this

Database password, third party services license key, password salt, etc. The sources of your rails app is full of sensitive data you don't want to disclose to anyone, let alone the entire internet.

As an example, I'll show how not to disclose my NewRelic license key.

## How to do it right

I'll explain below the solution I used, it's not the only one but it's simple and it works well.

### 1. Pull that sensitive data out of your repo

You need to store it somewhere that won't get pushed with the rest of your files.

Thankfully, the [figaro](https://github.com/laserlemon/figaro) gem exists to do just that.

Add it to your Gemfile:

```ruby
gem 'figaro'
```

&

```bash
$ bundle install
$ figaro install
```

(Don't hesitate to look at the gem documentation to learn how it works.)

And voilà! You now have a new file called `config/application.yml` and it's been added to the `.gitignore` file.
You can put all your keys and (dirty) secrets in this file and no one will ever know about it!

Here I created a key/value for my New Relic license key:

```yaml
new_relic_license_key: mysupersecretnewrelickey
```

The great thing about Figaro, it's that all the yaml key/values you declare in the `application.yml` are loaded in the environment by default and I can now replace the key in my `newrelic.yml` with this:

```yaml
license_key: <%= ENV['new_relic_license_key'] %>
```
Great! Are we safe now? Not just yet...

### 2. Clean up your git history

If you've already been pushing code on your Github repository, anyone could still access your sensitive data by looking at old versions of your files. We need to make sure this is not possible before making the repo public.

[Here](https://help.github.com/articles/remove-sensitive-data/) is a piece of Github documentation that explains very well how to do it.

**NB: I strongly advise that you actually read the documentation from the link above as it is a non trivial git operation.**

Here's how I did it for my newrelic.yml file:

```bash
$ git filter-branch --force --index-filter \
'git rm --cached --ignore-unmatch config/newrelic.yml' \
--prune-empty --tag-name-filter cat -- --all
$ git push heroku --force --all
$ git push origin --force --all
```

Now let's check if we can still access the history for this file on Github by going config/newrelic.yml and clicking
on the `history` button...

![click on history]({{ site.url }}/assets/history-click.png)

And checking that there is only one revision of the file, without the license key...

![file history]({{ site.url }}/assets/config-history.png)

Looks good!

One last step...

### 3. Set your production environment

Now I have one problem, I'm using Heroku and I need those environements variable to be loaded there in production as well. But thanks to Figaro, this is once again super easy.

You can then set the values from the `application.yml` in your Heroku production environment all at once. Just run the following:

```bash
$ figaro heroku:set -e production
```

### 4. Make it public!

Time to show your beautiful code to the world!

Double check you don't have any secrets left in your code, then go in the settings of your repo and click on "Make public" in the "Danger Zone" at the bottom.

![make it public]({{ site.url }}/assets/make-public.png)

And voilà!
