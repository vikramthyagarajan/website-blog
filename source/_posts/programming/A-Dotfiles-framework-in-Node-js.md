---
title: A Dotfiles Framework in Node.js
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
Configuration is a natural evolution of my development cycle. Initially I make software to fit a purpose, and slowly find ways to reuse it outside its sandbox. Invariably as the software evolves, it becomes configurable and extensible.

Dotfiles are a way to keep track of tweaks that you make to your application, to enhance your workflow and productivity. And if you're like me, you're looking to maintain these in a way that seems intuitive and simple. We should learn from a field that has solved this problem so well that it is almost second nature - Web Development

## The sane way to maintain configurations
So how do I go about storing these? Should I just copy all of them and dump them in Version Control? Should I add in a file that contains all the commands I want to run? Should I break them up into multiple files? What about installation, should I manually do it outside the dotfiles repo? Where do I start?

In web development, the only sane way to deal with server configuration is to keep a logical separation between two components: the code that describes the server (usually in JSON files) and the code that runs the server. This is web development 101. So how do we use this theory to maintain our dotfiles?

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
A Dotfile is the state of an application. Your application went through an evolution. It was installed, initialized, run and tweaked (massively). In order to get to the current state of the application, all of these actions must be done. So your dotfiles should describe this state accurately.

I would like to think of my dotfiles as a set of modules. And each module has a lifecycle. It installs, initializes and configures itself, which is what I call bootstrapping. This way I get finegrained control over modules, and can bootstrap a specific module instead of all at once. Also, if I have many modules and configs, I can choose which ones to bootstrap. Dependency management becomes a breeze.

## Implementation
My dotfile framework treats applications as modules. Each module has a lifecycle associated with it, and describes the way it was installed, initialized and configured. All of this is stored in Version Control so that I can get my whole dev environment set up by just running one bootstrap script. The framework is written in Node as it has first class support for JSON, and makes it simple to manage lifecycle events, but can be written in any language. You can check out the code at https://github.com/vikramthyagarajan/dotfiles, and fork it for maintaining your dotfile configuration

## Conventions
The framework makes it easy to maintain your dotfiles without much configuration for basic use cases. As I've mentioned before, each application has its own folder. Within this folder, these conventions are followed if no config options are given (this means that you have not included a module.json file in the folder) -

- An install.sh file which has the code that runs the app installation
- An initialize.sh file which has the initialization code and runs after install.sh
- A config folder, whose contents will be symlinked to the $HOME directory
- A configure.sh file which runs after the symlinking, and can be used to run code post symlinking

* Note: None of these files are compulsory. You can include just the files you want based on your requirement *

## Configuration
All configuration is maintained in the module.json file within each application folder. An example configuration is -
``` javascript
{
  "install": "sudo apt-get install vim", //inline bash script in string form, or name of file in the module directory which is a bash script
  "initialize": "mv ~/.vimrc ~/backup", //inline or filename of bash script which runs after installation and before config files are moved
  "configure": "configure.sh", //inline or bash filename. Run after config files are symlinked
  "config": {
    "global": {
      "path": "$HOME/.config" //sets the global path option for the config folder. This means all files in config will by symlinked at this new path
    },
    "nvim": { //nvim here is the name of the folder within the config directory. Options here will only affect this folder. You could also pass a config for a file
      "path": "$HOME/.config/vim" //file or folder level options within the config folder. These options will apply for filename/foldername and overrides the global
    }
  }
}
```

## Usage
After setting up your dotfiles in your required format, your dotfiles can be kept in Version Control. In order to run them, you can run these commands to set it up -
- bootstrap: Will set up the whole application, including install, init and configure it 
- configure: Will only symlink your config folders and run the configure script
``` javascript
  npm start bootstrap   //will bootstrap all modules
  npm start bootstrap vim   //will bootstrap the vim module
  npm start configure   //will configure all modules
  npm start configure vim   //will configure the vim module
```

## The Way Ahead
At the moment, this framework seems to satisfy my requirements in a dotfile management system. However, on the roadmap I do intend to make it easier to use by making a tool that can automate the hardest part - the starting configuration. The tool would take all your required files, move them to the config folder, symlink them and take all the commands you require in one go. Still thinking about this though, would love your comments on whether this would be helpful to start your dotfile management process

Do let me know what you think about the framework. And of course, issues and pull requests are more than welcome.
