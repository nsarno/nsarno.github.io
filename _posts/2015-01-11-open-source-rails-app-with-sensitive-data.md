---
title: Open source a rails app with sensitive data
layout: post
---

## Some context first

I recently open-sourced one of my personal projects on my github account.
I wanted to showcase some of my work and also a lot of awesome tools like [Codeship](https://codeship.com)
or [Code Climate](https://codeclimate.com) are free for open source projects which is pretty neat, given they're usually
pretty expensive otherwise.

One may think open-sourcing a rails app is as simple as pushing the "make it public" button, and you couldn't be more wrong.

![one does not]({{ site.url }}/assets/onedoesnot.jpg)

## Why you can't do it

Database password, third party services license key, password salt, etc. The sources of your rails app is full of sensitive
data you just don't want to disclose to anyone, let alone the entire internet.

## How to do it

I'll explain below the solution I used, it's not the only one but it's simple and it gets the job done.

