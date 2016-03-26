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
I've always been a fan of configuration. I've hated the "Convention over Configuration" philosophy of frameworks. 

And I love dotfiles. 

And though I've been messing about with my configurations for a long time, I'd never felt a need for version control with regard to it. 

- All the current methods of handling dotfiles were not to my liking
- While I appreciate bash and using it to keep track and run everything, its not perfect
- There should be a clear distinction between your configs and the code that keeps track of it
  - This doesn't mean that each dotfile should be in a separate repository, or be handled by complex directory structures.
  - This only means that having to keep track of configs through bash commands is unintuitive. Why write the command ln -s file1 ~ instead of setting it by config?
- And the best way to handle configurations is JSON. Its been used in web dev forever
- Moreover, dotfiles is not just about config files. It's about mainaining applications. Rarely is a config not tied to any application. 
- Dotfiles should be about backing up your development environment wrt applications.
- Bash is not the best for writing a framework to handle this
- Check out the second part regarding my implementation of this
