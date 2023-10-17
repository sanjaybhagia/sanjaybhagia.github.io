---
id: 165
title: Update list items to trigger event handler through PowerShell
date: 2013-02-25T09:50:51+02:00
author: sanjay.bhagia@gmail.com
layout: post
permalink: /2013/02/25/update-list-items-to-trigger-event-handler-through-powershell/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
disqus_identifier: https://sanjaybhagia.com/2013/02/25/update-list-items-to-trigger-event-handler-through-powershell/
categories:
  - PowerShell
  - SharePoint
tags:
  - Event Handler
  - PowerShell
  - Update List Items
---
<p style="text-align:justify;">Scenario:
A SharePoint list has different content types associated with it and every content type has a field of type taxonomy (connected to managed metadata termstore). And that list has an event handler attached to it that is triggered on item add and item update. In that event handler, you push the change to user profile property (for respective user profile) that is also of type managed metadata. So basically when you add or update an item in the list, that value is written back in a user profile property.</p>
<p style="text-align:justify;">Now, under normal circumstances everything should work fine as event handler will kick in and write back the values to user property. However, if you have to delete the user profile or due to different reasons, the values in user profile is wiped off etc, you need to ensure that the data already inserted in the list is also present in the user profile.</p>
<p style="text-align:justify;">Solution:</p>
<p style="text-align:justify;">For such scenarios, what we need to do is trigger the event handler on every list item so that the value is written back to user profile property. PowerShell comes to our rescue in such situation!</p>
<p style="text-align:justify;">Here is the powerShell script for that</p>

<pre><code class="powershell">
Function UpdateListItems($webAppUrl)
 {
  [Microsoft.SharePoint.SPSecurity]::RunWithElevatedPrivileges(
  {
     #Get Site Collections for respective web applications
     $sitecolls = Get-SPSite -WebApplication $webAppUrl

     Write-Host &quot;Total number of site collections: &quot; $sitecolls.Count

     #Iterate through every site collection to find the list at the root web
     foreach($siteCol in $sitecolls)
     {
        $site = Get-SPSite $siteCol.Url

        Write-Host &quot;Site Collection: &quot; $site.Url
        $rootSite = $site.RootWeb

        Write-Host $rootSite.Name

        #Retrieve the list
        $list = $rootSite.Lists[&quot;ListName&quot;]

        if($list -ne $null)
        {

         # Fetch list items only for required content type (If this needs to execute for every content type,
         # just remove the filter from following statement, just write $listItems = $list.Items)
         $listItems = $list.Items | ?{$_.ContentType.Name -eq &quot;ContentTypeName&quot;}

         foreach($item in $listItems)
         {
           Write-Host $item[&quot;Field's Dislay Name&quot;]

           #Update the item
           $item.Update()
         }
       }
     }
  })
 }
</code></pre>

Now once the function is defined, call it for execution.

<pre><code class="powershell">
UpdateListItems &quot;http://mysite.mydomain.com&quot;
</code></pre>
<p style="text-align:justify;">One thing to notice in the script above is that we are using RunWithElevatedPrivileges. My Colleague <a href="http://www.matthiaseinig.de/">Matthias Einig</a> has written an amazing <a href="http://www.matthiaseinig.de/2012/12/18/lost-in-impersonation/">post</a> on his blog regarding this, do check it out.</p>
<p style="text-align:justify;">Hope it helps.</p>