---
id: 354
title: Identifying the owner of personal sites in SharePoint
date: 2015-01-22T16:20:20+02:00
author: sanjay.bhagia@gmail.com (Sanjay Bhagia)
layout: post
guid: https://www.sanjaybhagia.com/?p=354
permalink: /2015/01/22/identifying-the-owner-of-personal-sites-in-sharepoint/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
disqus_identifier: https://www.sanjaybhagia.com/2015/01/22/identifying-the-owner-of-personal-sites-in-sharepoint/
categories:
  - PowerShell
  - SharePoint
tags:
  - KeywordQuery
  - Personal Site
  - PersonalSpace
  - User Profile
---
<strong>Scenario/Problem:</strong>

Recently I was faced with a situation where I wanted to know the owner of the Personal Site in SharePoint. While it’s fairly straightforward to identify the personal site by the WebTemplate property in the corresponding SPWeb object but there was no property that could simply pin-point the owner of the site itself. If you take a look at the User Profile (from User Profile Service in Central Administration), there is a property called “Personal Site” (internal name is PersonalSpace) that tells us the relative url of the personal site for the respective profile. It is set automatically by SharePoint after the personal site is successfully provisioned. Wouldn’t it be nice in if we simply have a property or attribute on SPWeb object that tells us just this.

<strong>Solution:</strong>

However, it is not that difficult to achieve this. There could be many possible implementations but here I’m going to discuss one approach that I recently implemented.

So based on the information I could get out of SharePoint, I first set the Personal Site property to be indexed so that it can be crawled by the search service. Then, I created a managed property called PersonalSite and map it to PersonalSpace property. This gives me the possibility to query the search engine like “PersonalSite:/person/adamb”. If this is targeted to People scope, you will get the user profile whose personal site is set to /personal/adamb. And its safe to assume that the value would be unique.

Having set up the basics, now we can simply write a search query to give us the result we want.

Moving ahead, we can store this value in the property bag of the SPWeb object of the personal site. And whenever we need this property, we can first check if the value is present in the property bag, if we already have it, we don’t need to make a query again to the search, otherwise we can make a search query and store the value in the property bag for the next time.

Here is the code snippets for the above mentioned functionality. This code snippet doesn’t contain the creation of managed property but rest of the implementation.

<?# Gist 7b6de6ad79d0383566ee /?>

With this fairly simple implementation, we can grab the owner of the personal site and fetch his user profile to perform other required operations.

Please feel free to share if you have any other alternative(s) to get this done.

P.S: You will require to run the Full Crawl after creating the managed property with the correct mapping. Before this, you will not get any result back from the search query.

Cheers !