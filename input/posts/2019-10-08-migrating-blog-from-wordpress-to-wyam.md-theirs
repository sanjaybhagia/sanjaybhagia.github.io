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

I had looked at few options in the past but never really set out to do this. What pushed me to make this move was that, during last month, I got an email that some malicious code has been detected on my website! SiteGround did a really good job to make my website *unavailable* and notified me so that I could investigate, clean it up and put it back. It was a fairly smooth process but it got me thinking, this shouldn't have happened at first place. I don't really need the overhead of dealing with server side code at all. I could have drastically reduced the attack surface just by serving the static content. So, this was the tipping point for me.

I have been following static content generation solutions and was aware of this space and few options like Jekyll & Wyam etc. I decided to go with Wyam for now (*it's built on top of .NET Core*) but any other engine will do. My motivation to go with Wyam was influenced by a number of people I follow in the tech community and the fact it's running on .NET which, for me, is good opportunity to understand and learn how things are working behind the scenes to make such application work.

### This is how my migration process looked like: 
- Installed [Jekyll Exporter](https://wordpress.org/plugins/jekyll-exporter/) plugin on my wordpress site and exported the content in markdown format
- Pretty much followed [this blog](https://erikonarheim.com/posts/using-wyam-blog) to do the heavy lifting, so won't repeat myself again as it has already been written quite a lot. Here is [another good blogpost](https://salvoz.com/posts/2018-01-01-static-content.html)
- Some decisions that I made though: 
  - Went with CleanBlog theme but made some changes (css, fonts etc.) to suit the design I liked.
  - The exported content is not really proper markdown but I am OK with it. It would have taken significant effort to clean it up otherwise.
  - Decided to put images along with the content for now. I will look further if there is any better approach like hosting images into Azure Blob storage.
  - Even though I had disqus comment system enabled on my wordpress, I encountered a bit of a trouble getting it right because the out-of-the-box wyam URLs were different than my previous and during testing I ended up creating different disqus threads for my posts and it messed it up. I needed to remove those threads from disqus to be able to re-use with my previous URLs. You can read more about this [here](https://mycyberuniverse.com/how-delete-discussion-threads-incorrect-url-disqus.html)
  - Configured the URLs to be same as my previous URLs (includes day in the post path besides the default year and month). I found this way simpler rather than doing the redirections etc.
  - Added support for Google Analytics (*might get rid of this going forward with some other solutions but good for now*)


### Release Management: 
- I have hosted the source code (aka, markdown pages and assets) on GitHub
- Setup [Netlify](https://www.netlify.com/) account to host the site (I could have hosted it directly on GitHub but wanted to try out Netlify as I was hearing so many good things about it lately,rightfully so. It was a delightful experience with Netlify.)
- I setup Azure DevOps pipeline (via yml) to push the content to Netlify
- I found this a lot easier and simpler without going to any other approach (Cake or PS). I'm tempted to use [Github Actions](https://github.com/features/actions) for this part however.
- Whenever I push anything into my '*master*' branch, the pipeline is triggered and content is pushed over to Netlify. 
  - One nice thing about Netlify is that, it allows you to preview your changes before you want to Publish it, which is nice (there are other options to create proper staging environment etc. but for me this was sufficient)

#### Here is my new tech stack:
- [Wyam](https://wyam.io/) - Static Content Generator
- [GitHub](https://www.github.com/) - hosting the content
- [Netlify](https://www.netlify.com/) - hosting the blog
- [Cloudflare](https://www.cloudflare.com/) - Caching, DNS and Certificate management (via Lets Encrypt)
- [Azure DevOps](https://dev.azure.com/) - CI/CD
- [Google Analytics](https://analytics.google.com) - Traffic Analysis

That's about it. With this setup, After pushing everything I redirected my domain from SiteGround to Netlify site (from cloudflare) and bam new blog is alive!

Now, I want to list some things that I like, did not like and what I will add / improve going forward. 

#### Things I like:
- The entire blog is served as static resource (html, images, css etc.), nothing is running server side (as of now at least)
- Wyam is pretty awesome and seem to work really well. I do look forward to learning more about this.
- I am able to write my posts totally in markdown on my local machine (without any internet connectivity with amazing VSCode)
- Site is much faster!

#### Things I am *less* satisfied with: 
- Wordpress took care of a lot of things out-of-the-box for me such as search, image compression, contact form and various other plugins (pretty for any need you can think of) and I will have to add a number of these features to my site which I find important. But I was fully aware of these trade-offs.
- I don't understand Wyam 100% yet as there is some learning curve and Wyam's own tooling (no intellisense for example) makes it a bit difficult to compose config file.
- I need to think of image compression or use better solution to reduce the size of served content.
- Had some issues with setting up Images compression with Wyam (apparently there are some issues around this)

#### Things I will add/improve going forward: 
- Add Search capability on my site as I think it's very important feature (and one that I use quite often no matter where I'm as a user)
- Better support for serving smaller images (or even replace the ones that I already have with smaller size)
- *Contact form*
- Might replace Disqus with GitHub issues (found out this [Utterance](https://utteranc.es/))

Another thing I found interesting is site performance. Up until now, I didn't pay much attention to this aspect since it's not a heavy site but as I recently moved to Australia, I started noticing how slow internet speed affects your mood :wink:, I decided to improve this aspect of my site. By following the approach for my new blog (without doing much optimization, I already gained a huge improvement), see it yourself: 

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

That's quite impressive already!!. I have yet to optimize images (which should push me even further). I can make some optimizations in time to improve it even further. I used [Google's PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/) to measure this.
  
That's it for now. I will keep posting my learnings and finding as I go along. 

Feel free to drop any suggestions if you have been the same path already, I would very much appreciate that. 

Cheers
