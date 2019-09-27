---
Title: 'Microsoft OpenHack DevOps - Stockholm'
date: 2019-10-05
author: sanjaybhagia
layout: post
classic-editor-remember:
  - block-editor
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
image: /images/main.png
categories:
  - Blog
  - Hosting
tags:
  - static-blog
  - wyam
  - migration
  - hosting
---

This is my attempt to migrate my site from 

Followed this blog post - which is quite nice

I decided to go with CleanBlog theme but made some changes in css to get the fonts and design as I was usin in my previous blog. I haven't got everything right yet so I'll be making smaller changes as I go forward. 

First thing I want to add in this blog is search capabilities. I had search in my previous blog and since I use search for almost everything I use in my daily life on the internet I believe this is critical for any website to have search capabilities. I have some pointers here that I will explore and it looks quite good.

Secondly, I wanted to host this site on github. Going forward I will be setting up proper CI/CD flow. I have bunch of options. I'll start with Github actions to setup my CI/CD flow. But for now, I've simply uploaded the static files manually. For the time being, I'll be adding contents manually whenever I want to publish any new post. 


So here is my setup. 
I have this repository that hosts the content of the site. 
Everything goes to 'master' branch. I have another branch 'gh-pages' that hosts the final output for the site which I'm serving. 
I have Github Action setup which does the following: 
  - gets triggered whenever anything is pushed into 'master' branch
  - Fetch the required tools 'cake' and 'wyam'
  - run 'wyam build' to generate the build artefacts into 'buildartefacts' directory
  - copy files 
  - commit changes
  - push changes
  - send out an email of the build result.
  - wife off cloudflare cache