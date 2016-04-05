---
title: A Dotfiles Framework in Node.js (Part 2)
subtitle: Delving deep into the perfect Dotfile framework
cover: 'http://www.artsfon.com/pic/201502/1920x1080/artsfon.com-54011.jpg'
layout: post
category: programming
date: 2016-03-29 21:31:00
tags:
  - nodejs
  - dotfiles
  - json
  - development
  - config
---

## Introduction
In this post, I go further in detail on my ideal Dotfile framework, and how I went about creating it. To briefly sum up part 1 () of this post, my requirements were simple -
- I should be able to configure my apps by JSON
- I should be able to describe the state of the application in order to recreate it

My dotfile framework treats applications as modules. Each module has a lifecycle associated with it, and describes the way it was installed, initialized and configured. All of this is stored in Version Control so that I can get my whole dev environment set up by just running one bootstrap script. The framework is written in Node as it has first class support for JSON, and makes it simple to manage lifecycle events, but can be written in any language. You can check out the code at https://github.com/vikramthyagarajan/dotfiles, and fork it for maintaining your dotfile configuration

## Conventions
The framework makes it easy to maintain your dotfiles without much configuration for basic use cases. As I've mentioned before, each application has its own folder. Within this folder, these conventions are followed if no config options are given (this means that you have not included a module.json file in the folder) -

- An install.sh file which has the code that runs the app installation
- An initialize.sh file which has the initialization code and runs after install.sh
- A config folder, whose contents will be symlinked to the $HOME directory
- A configure.sh file which runs after the symlinking, and can be used to run code post symlinking

Note: None of these files are compulsory. You can include just the files you want based on your requirement

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
