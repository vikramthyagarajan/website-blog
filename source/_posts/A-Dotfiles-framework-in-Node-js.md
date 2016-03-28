---
title: A Dotfiles Framework in Node.js (Part 1)
subtitle: A modern developers guide to maintaining configs in a sane way
permalink: dotfiles-framework-nodejs
cover: http://www.artsfon.com/pic/201502/1920x1080/artsfon.com-54011.jpg
date: 2016-03-25 23:59:01
category: programming
tags: 
  - nodejs
  - dotfiles
  - json
  - development
  - config
---
## Introduction
Configuration is a natural evolution of my development cycle. Initially I make software to fit a purpose. Slowly, I find ways to reuse the software outside this small sandbox. But invariably as the software evolves, so does its ability to be configured. And slowly my coding style and the way I write software has changed to think of configuration from the ground up. I've started writing code with the thought of its extensibility, far more that I used to before. And this is why I adhere more to the "Configuration over Convention" philosophy of frameworks.

Also I am a total productivity freak. I love tweaking my workflow to make my development process more efficient. Which is probably why I love dotfiles.

But a natural part of maintaining dotfiles, or any configuration for that matter, is its storage. Anyone who has had to work on a different computer knows the pain of not having your sweet tools and customisations with you. And manually copy pasting these configs are a pain. It's almost harder than starting from scratch. Almost. So version control is necessary in the maintainence of dotfiles. 

## The sane way to maintain configurations

I wrestled with the way I'd maintain this in version control. I think my experience in Node.js server development helped me in this regard. The only sane way to deal with server configuration has always been through JSON files. Given the first class support for JSON, maintaining configuration through JSON is trivial. 

The main point being that there should be a logical separation between the configuration and the code that runs it. The only thing a webserver should be concerned with is running the server, not worrying about what port it runs on. So the code running the server should be separated from the part where the configuration of the server is described. This is web development 101. Now how do we use this theory to maintain our dotfiles?

## The drawbacks of current dotfile managers

Bash is a good scripting languages, but maintaining configurations is unintuitive and hard. Why write
{% codeblock Symlinking in code lang:bash %}
  ln -s ~/.vimrc ~/programs/dotfiles/vim/config
{% endcodeblock %}
when you would rather write
{% codeblock Symlinking by configuration lang:javascript %}
{
  "symlink": {
    "source": "~/.vimrc",
    "target": "~/programs/dotfiles/vim/config"
  }
}
{% endcodeblock %}

The closest dotfile tool that I came across that helps with this is DotBot (https://github.com/anishathalye/dotbot). However, it fails in my other requirement of a dotfile manager. Which is -

## Dotfiles are not just about configuration

Your dotfiles are not configurations floating in the wind. Your dotfiles are the states of applications that you store. Your applications have gone through an evolution. They were installed, initialized, run and then tweaked (massively). In order to get to the state of the application as it is now, all of these actions must be. So your dotfiles should describe how the app was installed, initialized and then configured.

I would like to think of my dotfiles as a set of modules. And each module has a lifecycle. It installs, initializes and configures itself, which is what I call bootstrapping. This way I get finegrained control over modules, and can bootstrap a specific module instead of all at once. This way if I have many modules and configs, I can choose which ones to bootstrap and dependency management becomes a breeze.

## Why Node.js

Node is my preffered tool when it the requirement has anything to do with lifecycles or events. It is also my guilty pleasure runtime and I look for any excuse to use it
