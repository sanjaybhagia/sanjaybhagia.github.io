---
id: 316
title: Using HttpHandler to deal with cross-domain requests from AngularJS app for SharePoint on-premises
date: 2014-08-16T14:03:48+02:00
author: sanjay.bhagia@gmail.com
layout: post
permalink: /2014/08/16/using-httphandler-to-deal-with-cross-domain-requests-from-angularjs-app-for-sharepoint-on-premises/
geo_public:
  - "0"
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
disqus_identifier: http://sanjaybhagia.com/using-httphandler-go-around-cross-domain-requests-angularjs-app-sharepoint/
categories:
  - 'C#'
  - SharePoint
tags:
  - AngularJS
  - 'C#'
  - cross-domain requests
  - JavaScript
  - newsfeed
  - REST
  - SharePoint
  - social
---
<strong>Scenario:</strong>

When you go to the newsfeed in your personal site (mysite newsfeed), you can write post and target it to different sites you are following or on your personal feed.

Recently, I had to built the same functionality for one of the projects. The ideas was to build the UI using SharePoint’s REST APIs and AngularJS. I am not using Office365 so i still have SharePoint on-prem.

<img class="alignnone size-medium wp-image-342" src="/images/Screen-Shot-2014-08-14-at-10.37.17.png" alt="" width="300" height="158" />

<strong>Problem:</strong>

Its quite simple as you would expect to retrieve the list of sites a user is following. You simply call this REST endpoint “../_api/social.following/my/followed(types=4)” and you have the result, no brainer. But only when you dig into the results, you get to know that even though the list is correct but not every site returned has newsfeed enabled. As a result, you also get those sites that have no newsfeed and these are the sites you don’t want to display. So, how do you eliminate those?

The problem is that there is no information in the JSON response that REST endpoint returns. The only option you have is to actually check for the SiteFeed feature on that web. So you get the site, open that web and see if this feature in enabled. Now this is all fine when you are on server side but if you are implementing your functionality using client side, you will end up dealing with cross-domain issues and you will get exception as it is treated as a cross-domain request by the browser.

Now if you were dealing with Office365, this could have been resolved using Microsoft Azure’s ACS to authenticate your app with SharePoint and all would have worked just fine. But that’s not the case when you are building your client app using jQuery or Angular and using REST APIs in SharePoint on-prem.

<strong>Solution:</strong>

So, after struggling quite a bit to somehow deal with this, I ended up creating the HttpHandler in which I retrieved the list of sites the current user is following. Then I iterated through that collection to check for the Site Feed feature and return the collection of sites only with site feed feature enabled and continue to building my AngularJS app as I was before hitting this issue.

I am not putting up the entire code or even complete code for my app here. I am adding some snippets demonstrating the solution. Here is the markup in my view. Its simply a drop down that lists the sites I’m following just like the one you get in SharePoint by default.
<pre><code class="html">&lt;!-- Here is the markup in the view (html file) of my angularjs app. I am putting just the markup for rendering the dropdown with required results and eliminating rest of the code for demonstration purpose --&gt;
&lt;!-- here, vm.followedSites is the model that im setting in my controller and select element is bound to that model --&gt;
 &lt;div&gt;
      &lt;select ng-model="vm.selectedValue" ng-options="value.Name for value in vm.followedSites"&gt;
          &lt;option value=""&gt;everyone&lt;/option&gt;
      &lt;/select&gt;
&lt;/div&gt;</code></pre>
Here is my controller that calls the handler to retrieve that data and set the model that my view is bound to.
<pre><code class="csharp">/* to simplify things, i am demonstrating the underlying method that is calling in the httphandler to get the desired result 
 this method is inside my controller 
 */
 
 function loadFollowedSites(currentUser) {
            //construct the URL for httphandler to get the sites with newsfeed activated
            var url = "/_layouts/15/myapp/Handlers/ClientHandler.ashx?operation=getFollowedSitesWithNewsFeed&amp;username="+ currentUser;
            
            //make request to the url and return the result back
            return $http.get(url).then(function (response) {
                return response.data;
            });
        }</code></pre>
And finally, here is the code that retrieves the list of sites i’m following in my HttpHandler
<pre><code class="c#">/**
Here is the code snippet from my HttpHandler
 
**/  
  public void ProcessRequest(HttpContext context)
  {
      context.Response.ContentType = "application/json";
      context.Response.ContentEncoding = Encoding.UTF8;
      
      string operation = context.Request.QueryString["operation"];
 
      if (!String.IsNullOrEmpty(operation))
      {
        /* Code removed */ 
        if (operation == "getFollowedSitesWithNewsFeed")
        {
            string userName = (context.Request.QueryString["username"] != null) ? context.Request.QueryString["username"] : String.Empty;
            List&lt;SPSocialActor&gt; newsFeedFollowedSites = GetFollowedSitesWithNewsFeed(context, userName);
            context.Response.Write(JsonSerializer.Serialize(newsFeedFollowedSites));
        }
        
        /* code remoed */
      }
 
  }
  
  //Retrieves the list of all the sites that specific user is following that has SiteFeed web feature enabled
  private List&lt;SPSocialActor&gt; GetFollowedSitesWithNewsFeed(HttpContext cxt, string username)
  {
      List&lt;SPSocialActor&gt; followedSitesWithNewsFeed = new List&lt;SPSocialActor&gt;();            
      SPServiceContext context = SPServiceContext.GetContext(cxt);
      UserProfileManager profileManager = new UserProfileManager(context);
 
      if (profileManager.UserExists(username))
      {
          UserProfile userProfile = profileManager.GetUserProfile(username);
          SPSocialFollowingManager followingManager = new SPSocialFollowingManager(userProfile, context);
 
          SPSocialActor[] followedSites = followingManager.GetFollowed(SPSocialActorTypes.Sites);
 
          foreach (var actor in followedSites)
          {
              try
              {
                  using (SPSite site = new SPSite(actor.Uri.AbsoluteUri))
                  {
                      SPWeb rootWeb = site.RootWeb;
                      
                      //Check for SiteFeed feature (web)
                      Guid featureGuid = new Guid("15a572c6-e545-4d32-897a-bab6f5846e18");
                      if (rootWeb.Features[featureGuid] != null)
                      {
                          followedSitesWithNewsFeed.Add(actor);
                      }
                  }
              }
              catch 
              { 
                //Handle exception
              }
          }
      }
      else
      {
        //Log - user doesn't exist
      }            
      
      
      return followedSitesWithNewsFeed;
  }</code></pre>
Please let me know if there’s something I have missed or if there is any better way of doing this. I will really appreciate it.

Thanks Cheers