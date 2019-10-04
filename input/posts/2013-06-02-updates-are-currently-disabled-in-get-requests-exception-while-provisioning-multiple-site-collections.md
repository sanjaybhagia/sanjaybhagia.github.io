---
id: 269
title: Updates are currently disabled in GET requests exception while provisioning multiple site collections
date: 2013-06-02T18:44:45+02:00
author: sanjaybhagia
layout: post
permalink: /2013/06/02/updates-are-currently-disabled-in-get-requests-exception-while-provisioning-multiple-site-collections/
disqus_identifier: https://www.sanjaybhagia.com/2013/06/02/updates-are-currently-disabled-in-get-requests-exception-while-provisioning-multiple-site-collections/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
categories:
  - SharePoint
  - Uncategorized
tags:
  - Add SiteCollections
  - AllowUnsafeUpdates
  - GET
  - HttpContext.Current
  - Multiple site collections
  - Site Provisioning
---
<strong>Scenario:</strong>
<p style="text-align:justify;">Recently i was working with creating site provisioning in SharePoint 2010. For that I have a list where user submits the request for the site that needs to be created (along with some required metadata). Then I have scheduled the timer job that picks up items from that list (based on the status), iterates through the items and provisions the site.</p>
<p style="text-align:justify;">Here is the method in Timer Job that iterates through the items and creates site collections.</p>

<pre><code class="csharp">public static void ProvisionSite(String siteCollectionUrl)
{
   using (SPSite siteCollection = new SPSite(siteCollectionUrl))
   {
      SPList requestList = siteCollection.RootWeb.Lists.TryGetList(&quot;SiteRequests&quot;);
      if (requestList != null)
      {
        var query = new SPQuery();

        //Fetch the items that have Status=Requested
        query.Query = String.Format(&quot;{0}&quot;, &quot;Requested&quot;);

        SPListItemCollection items = list.GetItems(query);

        if (items != null &amp;&amp; items.Count &gt; 0)
            ProcessRequest(items);
      }
   }
}

//Generates random 7-digit number
public static String GenerateSiteNumber()
{
  Random random = new Random();
  String siteNumber = random.Next(0000000, 9999999).ToString();
  return siteNumber;
}

public static void ProcessRequest(SPListItemCollection items)
{
   try
   {
      foreach (SPListItem item in items)
      {
        try
        {
           SPSite site = null;
           SPSecurity.RunWithElevatedPrivileges(delegate
           {
               SPWebApplication webApp = item.Web.Site.WebApplication;
               SPSiteCollection siteCollections = webApp.Sites;

              //Generate random number to use as site collection url
              String siteNumber = GenerateSiteNumber();
              String relativeUrl = &quot;/sites/&quot; + siteNumber;

              //Fail if site collection already exists
              if (SPSite.Exists(new Uri(&quot;http://sp2010demo/&quot; + relativeUrl)))
                   throw new Exception(&quot;Site already exists&quot;);

               using (SPSite newSite = siteCollections.Add(relativeUrl, &quot;My Site&quot; + siteNumber, &quot;Test Site collection&quot;, 1033, null, &quot;dc\spadmin&quot;, &quot;&quot;,       &quot;&quot;))
               {
                  site = newSite;
                  //.. code removed for clarity .....
               }
           });
        }
      }
   }
   catch
   { }
}
</code></pre>

<strong>Problem:</strong>
<p style="text-align:justify;">As provisioning is done by the timer job Context is null (i.e., HttpContext.Current will be null). In my solution, I am simply iterating through the list items and creating the site collections. First request always works fine, site is created successfully but I am getting an exception as soon as the site for the next item is being provisioned. The exception that I get is the following:</p>
<em>"Updates are currently disabled in GET requests. To enable updates in GET requests, change the property AllowUnsafeUpdates on SPWeb."</em>
<p style="text-align:justify;">Now apparently, this exception is thrown when you perform any GET operation on Web and you haven't set AllowUnsafeUpdates to "True" !! But I'm just creating a site collection, so I don't have any SPWeb object where I should set this property ??</p>
<p style="text-align:justify;">After investigating a little further (and with the help of ILSpy) it turned out that after first site collection is created, HttpContext.Current is set to the context of this newly created site collection and for the next item, Context is already set to the previous site collection and exception is thrown at SiteCollection.Add() method. The Add() method basically checks if HttpContext is already set, if yes then it uses the context and in my case this is wrong!</p>
<strong>Solution</strong>
<p style="text-align:justify;">The solution is quite easy to fix.</p>
<p style="text-align:justify;">After the completion of first iteration, set the HttpContext to null so that during the next request there is no context set.</p>
<p style="text-align:justify;">Here is the line of code (finally block) that I added to my previous method.</p>

<pre><code class="csharp">
public static void ProcessRequest(SPListItemCollection items)
{
 try
 {
   foreach (SPListItem item in items)
   {
     try
     {
         //... code removed for clarity --
     }
     finally
     {
        //Reset the Context
        System.Web.HttpContext.Current = null;
     }
   }
 }
 catch
 { }
}
</code></pre>

Hope it helps.