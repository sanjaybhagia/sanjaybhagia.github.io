---
Title: 'Mirating from wordpress to wyam'
date: 2019-10-05
author: sanjaybhagia
layout: post
categories:
  - Blog
  - Hosting
tags:
  - static-blog
  - wyam
  - migration
  - wordpress
---

I had been hosting my blog on SiteGround for a number of years now. It's been good so far, no complaints but I had been wanting to try out the static content generation engines for a while. One major reason for this is that my content is mostly static and don't really require a server side for anything as such (ok, few things but nothing that can't be achieved on client-side). 

So, this was the tipping point for me. I had been following static site generation solutions so I was aware of this space and few options (like jeklyll, wyam etc.). I decided to go with wyam for now (it's built on top of .NET Core) but any other engine will serve similar purpose. 

#### New tech stack:
- [Wyam](https://wyam.io/) - Static Content Generator
- [GitHub](https://www.github.com/) - hosting the content
- [Netlify](https://www.netlify.com/) - hosting the blog
- [Cloudflare](https://www.cloudflare.com/) - Caching and DNS
- [Azure DevOps](https://dev.azure.com/) - CI/CD
- [Google Analytics](https://analytics.google.com) - Traffic Analysis


###This is how my process looked like: 
- Installed Jekyll Export plugin on my wordpress site and exported the content
- Pretty much followed this blog to do heavy lifting (), so won't repeat myself again as it has already been written quite a lot. 
- Some decisions on my end though: 
  - Went with CleanBlog theme but made some changes (css, fonts etc.) to suit the design I like.
  - The exported content is not really proper markdown but I am OK with it (it would have taken sinifican effort to clean it up otherwise)
  - I just made changes to the image paths
  - Even though I had disqus comment system enabled on my wordpress, I had bit of a trouble getting it right (mostly, the out-of-the-box wyam URLs were different than my previous and during testing I ended up creating different disqus threads for my posts and it messed it up. I have to remove those threads from disqus to be able to re-use with my previous URLs)
  - configured the URL to be same as my previous URLs. I found this way simpler rather than doing the redirections etc.
  - Added support for Google Analytics

###Deployment: 
- I have hosted the site content on github
- Setup [Netlify](https://www.netlify.com/) account to host the site (I could have hosted it directly on github but wanted to try out Netlify as I was hearing so many good things about it lately)
- I setup Azure DevOps pipeline (via yml) to push the content to Netlify
- I found this a lot easier and simpler without going to any other approach (Cake or PS). I'm tempted to use Github actions for this part however.
- So, whenever I push anything in my 'master' branch, the pipeline is triggered and content is pushed over to Netlify. 
- One nice thing about Netlify is that, it allows you to preview your changes before you want to Publish it, which is nice (there are other options to create proper staging environment etc. but for me this was sufficient)


#### Things I like:
- The entire blog is served as static resource (html, images, css etc.), nothing is running server side (as of now)
- Wyam is pretty awesome and seem to work really well for now
- I'm running this practically for free
- I am able to write my posts totally in markdown on my local machine (without any internet connectivity with amazing VSCode)

#### Things I am not really satisfied with: 
- Wordpress took care of a lot of things out-of-the-box for me such as search, image compression, contact form and various other plugins (pretty for any need you can think of) and I will have to add a number of these features to my site which I find important
- I don't understand 100% yet as there is some learning curve and wyam's own tooling (no intellisense etc.)
- I need to think of image compression or use other solution
- Had some issues with setting up Images compression with wyam (aparently there is some issue)

#### Things I will add/improve going forward: 
- Add Search capability on my site as I think it's very important feature (and one that I use quite often no matter where I'm as a user)
- Better support for serving smaller images (or even replace the ones that I already have with smaller size)
- Contact form

Another thing I found interesting is site performance. Up until now, I didn't pay much attention to this aspect since it's not a heavy site but as I recently moved to Australia (pun intended), I started noticing how slow internet speed affects your mood :p, I decided to improve this aspect of my site. By following the approach for my new blog (without doing much optimization, I already gained a huge improvement), see it yourself: 

This is my site on wordpress earlier (see both mobile and desktop speeds): 
<p>
  <img src="/images/old-mobile.png" width="300px">
  <img src="/images/old-desktop.png" width="300px">
</p>

And here is my new blog (again, mobile vs desktop):
<p>
  <img src="/images/new-mobile.png" width="300px">
  <img src="/images/new-desktop.png" width="300px">
</p>

That's quite impressive already. I have yet to optimize images (which should push me even further). Another thing I might work with is getting rid of disqus and using Github commenting system (since this is a dev focus blog, so it shouldn't be big of a deal to use Github issues system to serve as a comments engine).

  Website performance
  https://developers.google.com/speed/pagespeed/insights/?url=sanjaybhagia.com

  Never realized, it's so bad !!

  Deploying the site as it was (after fixing few thins) to netlify

  Improvement I: Fixing images (I moved the highest resolution images from my wordpress export - so reduced the image size) and here are the results. 

That's it for now. I will keep posting my learnings and finding as I go along. 

Feel free to drop any suggestions if you have been the same path already, I would very much appreciate that. 

Cheers


wyam build -p -w (rebuild everytime you make any change and auto refreshes the page) - great while you are building the site or making any changes to preview quickly