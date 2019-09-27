---
id: 108
title: Remove webpart from a page on Personal site using delegate control
date: 2013-01-12T12:06:56+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=108
permalink: /2013/01/12/remove-webpart-from-a-page-on-personal-site-using-delegate-control/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
dsq_thread_id:
  - "2795716839"
disqus_identifier: https://sanjaybhagia.com/2013/01/remove-webpart-from-a-page-on-personal-site-using-delegate-control/
categories:
  - 'C#'
  - SharePoint
tags:
  - Delegate Controls
  - Feature Stappling
  - Feaures
  - Personal Site
  - WebParts
---
<p style="text-align:justify;">Customizing Personal Sites (site collections for users in my site) could be little challenging at times. One reason being that personal site is provisioned automatically by SharePoint and there could be more than one point from which user can provision the site and there is no support to create your own web template or site definition, as we can do with every other type of site (<em>as per my knowledge</em>). Creating your own web template provides great flexibility and easiness to provision sites  having your own settings and all but lacking this feature for personal sites provide us more challenges, even for small customizations at times. In a recent project, one of the customer's wish was to remove "Recent Blog Posts" web part from default page when user provisions the personal site.</p>
<p style="text-align:justify;"><strong>Solution</strong>: After trying out different approaches the final solution turned out to create a delegate control that removes the web part from the page when site is provisioned and having this delegate control added on the site collection through feature stappling.</p>
So, here is what I did to fulfill this requirement.
<ol>
	<li>Create a delegate control</li>

Here is the element file for delegate control

<pre><code class="xml">
&lt;!--?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot;?--&gt;

 &lt;Control
 ControlAssembly=&quot;MyAssembly, Version=1.0.0.0, Culture=neutral, PublicKeyToken=b848d3ec396f6039&quot;
 ControlClass=&quot;MyAssembly.RemoveWebPartsFromPersonalSiteDefaultPage&quot; Id=&quot;AdditionalPageHead&quot; Sequence=&quot;50&quot;

</code></pre>

	<li>Fetch the desired webpart and remove it</li>
</ol>
and in the code behind file for this delegate control add the functionality to fetch and remove the desired webpart (I've added this logic in Render method)

<pre><code class="csharp">

//... code removed

//Fetch Web Part title from SPCore resoruce file for current web language
 //It is important to fetch the localed based on current site's language not based on Browser locale or regional settings!
 var localizedRecentPostWebPartName = SPUtility.GetLocalizedString(&quot;$Resources:spscore,MySiteOnet_WebPart_Blog&quot;, &quot;spscore&quot;, SPContext.Current.Web.Language);

//remove the webpart
 RemoveWebPartsFromWebPartPage(SPContext.Current.Web, &quot;default.aspx&quot;, &quot;MiddleRightZone&quot;, localizedRecentPostWebPartName);

 //add page redirectino logic otherwise it will be an infinite loop
 // ... code removed

</code></pre>

Here is the definition of RemoveWebPartsFromWebPartPage method:

<pre><code class="csharp">

//... code removed

using (SP.SPLimitedWebPartManager webpartManager = web.GetLimitedWebPartManager(webPartPage.Url, Web.PersonalizationScope.Shared))
{
  var collection = webpartManager.WebParts;
  List webParts = new List();

  foreach (WebPart webPart in collection)
  {
    if (fromZoneId == webpartManager.GetZoneID(webPart))
    {
      if (webPart.DisplayTitle == webPartTitle)
      {
          webParts.Add(webPart);
      }
    }
  }

 // delete the web parts
 foreach (WebPart webPart in webParts)
 {
   webpartManager.DeleteWebPart(webPart);
 }
}

</code></pre>
<p style="text-align:justify;">That's it! that is the functionality that we need to remove the webpart. We created a delegate control that contains the code to remove the web part from desired page! Now comes the part where we need to execute this delegate. You can use different approaches depending on your requirement. For this demo, we create a simple site scoped feature and add this delegate as an item in that feature.</p>
<p style="text-align:justify;">Build your solution, create the package and deploy it. Activate the feature. Having this feature activated, as soon as user navigates to the default page, our delegate control will kick in and looks for the desired web part and removes it!</p>
<p style="text-align:justify;">Other approaches could be creating feature stappling mechanism that attaches our delegate control with desire web templates so that this control is attached to every site that is created.</p>
Hope it helps!