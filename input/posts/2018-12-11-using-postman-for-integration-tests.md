---
id: 624
title: Using Postman for Integration Tests
date: 2018-12-11T10:55:51+02:00
author: sanjaybhagia
layout: post
guid: http://www.sanjaybhagia.com/?p=624
permalink: /2018/12/11/using-postman-for-integration-tests/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";N;s:10:"author_url";N;s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";N;s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:5:"draft";s:3:"url";N;}'
disqus_identifier: https://www.sanjaybhagia.com/2018/12/11/using-postman-for-integration-tests/
categories:
  - APIs
tags:
  - apis
  - integration-tests
  - postman
  - testing
---
<!-- wp:paragraph {"align":"left"} -->
In my recent project, I'm providing some integration services (APIs) that are used by the web application to fetch data from source systems. This is a legacy project that I inherited. To be honest, this was a PoC and we all know how PoCs are developed and very often continue to be used in production. That is why I have been rewriting/refactoring this codebase ([I even tweeted about this a while ago](https://twitter.com/bhagiasanjay/status/1054740627978641410)) in every sprint to improve the code quality and cleaning up a lot of code. Since I don't have a proper code coverage for all the source code, I'm always concerned when I'm continuously developing and refactoring this codebase. I'm often concerned (rightfully so) that I'll break my API and will eventually bring down the system due to some stupid mistake.  But until I can have a good enough code coverage I want to be confident that I'm not breaking my APIs whenever I'm pushing a new release to production. Now I've taken quite a lot of steps over past few months (from having proper CI/CD pipeline to having slots support to have the availability for new releases etc) to clean up the codebase and continue to have my services up-and-running, one thing I'm particularly excited about is Postman.
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>If you are involved in RESTful API development, chances are you are already aware of Postman or <g class="gr_ gr_5 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="5" data-gr-id="5">atleast</g> have heard of it. But in case this is the first time you heard about the tool, I'll give you a very quick intro about it.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:pullquote {"align":"center","className":"aligncenter"} -->
> "Postman mkes API Development Simple"
> [*Postman website*]('https://www.getpostman.com')
<!-- <figure class="wp-block-pullquote aligncenter"><blockquote><p>"Postman Makes API Development Simple"</p><cite><a href="https://www.getpostman.com/" target="_blank">Postman website</a></cite></blockquote></figure> -->
<!-- /wp:pullquote -->

<!-- wp:paragraph -->
Postman is a powerful HTTP client for testing web services. It makes API development faster, easier and better. You can read more about it on their official [website](https://www.getpostman.com)
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>I've been using postman during my development (testing APIs etc) but for last few weeks, I've started to write integration tests for my APIs so that whenever I'm making any changes or creating a new release (for any environment), I can quickly run the collection and make sure everything is still functioning as expected.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>This is not a replacement <g class="gr_ gr_63 gr-alert sel gr_gramm gr_replaced gr_inline_cards gr_disable_anim_appear Grammar multiReplace" id="63" data-gr-id="63">for</g> having a proper unit test (with good enough code coverage) but I find it very useful to quickly verify that I&nbsp;haven't broken the core functionality of my solution.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Here are some features I'm using with Postman for my integration tests</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul><li>Test automation<ul><li>Collections<ul><li>One big collection to run tests for all services in one go</li></ul></li><li>Separate collections for each service (API) with calls of each operation that API exposes/supports</li><li>Extended tests to cover different scenarios</li></ul></li><li>Debugging<ul><li>&nbsp;Pre-script and tests for individual calls</li></ul></li><li>Environment variables - to have the possibility to test the APIs towards&nbsp;any environment that I want to test&nbsp;</li></ul>
<!-- /wp:list -->

<!-- wp:paragraph -->
<p>Even though I've used quite a few features so far but it doesn't end here. Here is what I have planned to take advantage of other features as I go along.&nbsp;<br></p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul><li>Integrating postman in my CI/CD (<g class="gr_ gr_3 gr-alert gr_spell gr_inline_cards gr_disable_anim_appear ContextualSpelling ins-del multiReplace" id="3" data-gr-id="3">newman</g>) so that I can&nbsp;auto swap&nbsp;my slots for the web application if everything is green</li><li>Mocking</li><li>Continue to extend my tests cases to increase coverage<br></li></ul>
<!-- /wp:list -->

<!-- wp:paragraph -->
<p>How are you using Postman in your projects? Have you found anything intersting/useful that you would like to share?</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Cheers</p>
<!-- /wp:paragraph -->