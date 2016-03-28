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
NOTE: If you want to read about my implementation of this idea, check out part 2 of this post at (). I detail my thought process and decisions behind the same in this post.

## Introduction
Configuration is a natural evolution of my development cycle. Initially I make software to fit a purpose, and slowly find ways to reuse it outside its sandbox. Invariably as the software evolves, it becomes configurable and extensible.

Dotfiles are a way to keep track of tweaks that you make to your application, to enhance your workflow and productivity. And if you're like me, you're looking to maintain these in a way that seems intuitive and simple. We should learn from a field that has solved this problem so well, that it is almost second nature - Web Development

## The sane way to maintain configurations
So how do I go about storing these? Should I just copy all of them and dump them in Version Control? Should I add in a file that contains all the commands I want to run? Should I break them up into multiple files? What about installation, should I manually do it outside the dotfiles repo? Where do I start?

In web development, the only sane way to deal with server configuration is to keep a logical separation between two. The code that describes the server (usually in JSON files)is separate from server code itself. This is web development 101. So how do we use this theory to maintain our dotfiles?

## The drawbacks of current dotfile managers
Bash is a good scripting language, but maintaining configurations with it is unintuitive and messy.
{% codeblock Symlinking in code lang:bash %}
  ln -s ~/.vimrc ~/programs/dotfiles/vim/config
{% endcodeblock %}
is more complex to understand than
{% codeblock Symlinking in JSON lang:javascript %}
{
  "symlink": {
    "source": "~/.vimrc",
    "target": "~/programs/dotfiles/vim/config"
  }
}
{% endcodeblock %}

The closest dotfile tool that I came across that understands this is DotBot(https://github.com/anishathalye/dotbot). However, it fails in my other requirement -

## Dotfiles are not just about configuration
A Dotfile is the state of an application. Your application went through an evolution. It was installed, initialized, run and tweaked (massively). In order to get to the current state of the application, all of these actions must be done. So your dotfiles should describe how the app was installed, initialized and then configured.

I would like to think of my dotfiles as a set of modules. And each module has a lifecycle. It installs, initializes and configures itself, which is what I call bootstrapping. This way I get finegrained control over modules, and can bootstrap a specific module instead of all at once. Also, if I have many modules and configs, I can choose which ones to bootstrap and dependency management becomes a breeze.

## My ideal application specific dotfile
{% codeblock Vim dotfile conf lang:javscript%}
{
  "install": "sudo apt-get install vim", //inline bash script in string form, or name of file in the module directory which is a bash script
  "initialize": "mv ~/.vimrc ~/backup", //inline or filename of bash script which runs after installation and before config files are moved
  "configure": "configure.sh", //inline or bash filename. Run after config files are symlinked
  "config": {
    "global": {
      "path": "$HOME/.config" //sets the global path option for the config folder. This means all files in config will by symlinked at this new path
    },
    "filename/foldername": {
      "path": "$HOME/.config/vim" //file or folder level options within the config folder. These options will apply for filename/foldername and overrides the global
    }
  }
}
{% endcodeblock %}
