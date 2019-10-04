---
id: 211
title: Consuming Content Type Hub in custom web templates
date: 2013-03-21T13:40:44+02:00
author: sanjaybhagia
layout: post
permalink: /2013/03/21/consuming-content-type-hub-in-custom-web-templates/
disqus_identifier: https://www.sanjaybhagia.com/2013/03/21/consuming-content-type-hub-in-custom-web-templates/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
categories:
  - PowerShell
  - SharePoint
tags:
  - Consuming Content Type
  - Content Type Hub
  - Cotnent type publishing
  - Enable Feature
  - Managed Matadata Service
  - onet
  - TaxonomyFieldAdded
  - Term store management
  - Web Templates
---
<p style="text-align: justify;">Setting up content type hub in SharePoint 2010 is quite straightforward process. There are simple steps to follow and if followed in correct order you can get it working in no time. Content Type Hub relies on managed metadata service. Unless all web applications are sharing the same instance of managed metadata service application, they should be able to consume content types from the content type hub (a site collection marked as a hub and other web application are marked to consume from this location).</p>
<p style="text-align: justify;">However, if you are doing the customization and you have some custom Web Templates that you use to create sites and subsites. And in those sites, you want to consume content types from content type hub, you might get a little bump in the road during the setup.</p>
<p style="text-align: justify;">As I mentioned, Content Type Hub relies on Managed Matadata service, one feature TaxonomyFieldAdded (a hidden) feature is enabled on every site collection by default. This feature is not activated by default when you create web templates (please refer to this blog for further explanation of this: <a href="http://blogs.msdn.com/b/chaks/archive/2011/09/04/web-templates-and-content-type-publishing.aspx">Web Templates and Content Type Publishing</a>). So if you navigate to the Site Settings for any site that is created from custom web template, you will not see any link for Term store management or Content Type Publishing</p>
<p style="text-align: center;"><a href="/images/sitesettings-featuredisabled.png"><img class=" wp-image-212 aligncenter" src="/images/sitesettings-featuredisabled.png" alt="sitesettings-featuredisabled" width="540" height="320" /></a></p>
So for custom web templates, you are required to activate this feature.  Here is the PowerShell code for enabling this feature on a specific site collection.

<pre><code class="ps">$snapin = Get-PSSnapin | Where-Object {$_.Name -eq 'Microsoft.SharePoint.Powershell'}

if ($snapin -eq $null)
{
Write-Host "Loading SharePoint Powershell Snapin"
Add-PSSnapin "Microsoft.SharePoint.Powershell" -EA SilentlyContinue
}

#Enable &lt;strong&gt;TaxonomyFieldAdded &lt;/strong&gt;feature on site collection
Enable-SPFeature –Identity 73EF14B1-13A9-416b-A9B5-ECECA2B0604C –url "http://portal.local.domain/sites/subsite1/"</code></pre>

Once feature is activated, you will see the links in Site Settings appear !
<a href="/images/sitesettings-featureenabled.png"><img class="wp-image-213 aligncenter" src="/images/sitesettings-featureenabled.png" alt="sitesettings-featureenabled" width="540" height="303" /></a>
<p style="text-align: justify;">Now, you should be able to consume content types from content type hub as expected. You can activate this feature directly from your web template, add an entry for this feature in onet.xml file for your web template, in this way you don't have to enable this feature manually.</p>
<pre><code class="xml">&lt;/pre&gt;
&lt;SiteFeatures&gt;
&lt;!-- OOTB: Taxonomy --&gt;
&lt;Feature ID="73EF14B1-13A9-416b-A9B5-ECECA2B0604C" /&gt;
&lt;/SiteFeatures&gt;
&lt;pre&gt;</code></pre>