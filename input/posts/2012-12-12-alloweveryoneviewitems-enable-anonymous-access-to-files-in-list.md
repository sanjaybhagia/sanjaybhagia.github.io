---
id: 90
title: 'AllowEveryoneViewItems - Enable anonymous access to files in list'
date: 2012-12-12T19:13:34+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=90
permalink: /2012/12/12/alloweveryoneviewitems-enable-anonymous-access-to-files-in-list/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
dsq_thread_id:
  - "2931252379"
disqus_identifier: https://sanjaybhagia.com/2012/12/alloweveryoneviewitems-enable-anonymous-access-to-files-in-list
categories:
  - 'C#'
  - SharePoint
tags:
  - Anonymous Access
  - Lists
  - SharePoint
---
<p style="text-align:justify;">SharePoint provides anonymous access to sites and document libraries etc but for that you have to set up the policy at web application level and then for specific site or document library as per your needs. But at times you don't want to allow anonymous access for entire web application or site collection but simply want to provide access to files within a specific document library without going through the trouble of configuring and managing permissions.</p>
<p style="text-align:justify;">If you want to access documents within a document library or files in a list (as an attachment) there is one quick and easy way to do that.</p>
<p style="text-align:justify;">There is a list property called <em>AllowEveryoneViewItems</em> that makes all documents within a document library anonymously accessible without dealing with permissions. There is no option in UI to set this up so you have to either do it through code, declaratively (list definition element.xml) or Powershell (depending on your situation and need).</p>
Here is the PowerShell code for setting this property on any list or library

<pre><code class="ps">
$web = Get-SPWeb -Identity &quot;[site url]&quot;
$list = $web.Lists.TryGetList(&quot;[list title]&quot;);
$list.AllowEveryoneViewItems = $true
$list.Update()
</code></pre>

This is how you define in list definition xml:

<pre><code class="xml">
&lt;!--?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot;?--&gt;

	&lt;ListTemplate
		Name=&quot;[list name]&quot;
		DisplayName=&quot;[list display name]&quot;
		Description=&quot;[list description]&quot;
		Type=&quot;10000&quot;
		BaseType=&quot;0&quot;
		Default=&quot;True&quot;
		VersioningEnabled=&quot;TRUE&quot;
		Hidden=&quot;FALSE&quot;
                AllowEveryoneViewItems=&quot;TRUE&quot;
		HiddenList=&quot;FALSE&quot; /&gt;

</code></pre>

And this is how you can do it using code

<pre><code class="csharp">
using (SPSite site = new SPSite(&quot;[site collection url]&quot;))
{
   using (SPWeb web = site.OpenWeb())
   {
       SPList list = rootWeb.Lists.TryGetList(&quot;[list title]&quot;);
       if (list != null)
       {
          if (!list.AllowEveryoneViewItems)
          {
             list.AllowEveryoneViewItems = true;
             list.Update();
          }
       }
    }
}
</code></pre>

It is important to note here that this property does not apply to all list items, but only to documents in document libraries or to attachments in list items. So you have to access this file directly for it to work ! (reference: <a href="http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.splist.alloweveryoneviewitems.aspx" target="_blank">SPList.AllowEveryoneViewItems property</a>)

Here is the code snippet for accessing the file directly using HttpWebRequest

<pre><code class="csharp">
var fileContent = String.Empty;
//construct path to the navigation file
string fileUrl = &quot;[full url of the file]&quot;;

//create webrequest to fetch this file!
var httpRequest = (HttpWebRequest)WebRequest.Create(fileUrl);
using (var webResponse = (HttpWebResponse)httpRequest.GetResponse())
{
   if (webResponse.StatusCode == HttpStatusCode.OK)
   {
      using (var responseStream = new StreamReader(webResponse.GetResponseStream()))
      {
          //if we get the response, read the content from file as string
          fileContent = responseStream.ReadToEnd();
      }
   }
}
</code></pre>