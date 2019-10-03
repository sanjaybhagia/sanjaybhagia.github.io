---
id: 181
title: Update Personal Site Property in User Profile
date: 2013-03-06T11:01:28+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=181
permalink: /2013/03/06/update-personal-site-property-in-user-profile/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
disqus_identifier: https://www.sanjaybhagia.com/2013/03/06/update-personal-site-property-in-user-profile/
categories:
  - PowerShell
  - SharePoint
tags:
  - My Site
  - Personal Site
  - PersonalSpace
  - User Profile
---
User Profile has one property called PersonalScope that stores the URL of the personal site of user (if mysite is enabled for users). When user creates personal site, this property is set. Recently we had some problem in our environment with user profile synchronization that removed some of the information for user profiles and Personal Site was one of them. The impact of this was that when affected users navigate to their personal site ("My Content") they get the message that personal site hasn't been created yet. On checking the site collections for my site web application in Central Administration, the site collections for users were present. So it was just the linkage that was broken.

It is as simple as updating the User Profile's Personal Site property with proper relative URL of the site collection, but if many users are affected then PowerShell is the way to go!

Here is the scrip that i wrote to fix this linkage. I basically, went through all the site collections (in My Site Web Application) and fetched the user profile for every site and checked if Personal Site is null, simply update it with relative URL.

<pre><code class="ps">Add-PSSnapin &quot;Microsoft.SharePoint.PowerShell&quot;

#This function resets the link to personal site for users who have personal site created but the link has been removed from user profile
Function UpdatePersonalSiteLinks($webApplicationUrl)
{
   #Instantiate Web Application Object
   $webApplication = Get-SPWebApplication -Identity $webApplicationUrl
   
   #Create Service Context for User Profile Manager
   $context = Get-SPServiceContext $webApplication.Sites[0]

   #Get User Profile Manager instance
   $profileManager = new-object Microsoft.Office.Server.UserProfiles.UserProfileManager($context)
   [Microsoft.SharePoint.SPSecurity]::RunWithElevatedPrivileges(
   {
     #Iterate through site collection in My Site Host web application
     foreach($siteCol in $webApplication.Sites)
     {
          Write-Host &quot;Site: &quot; $siteCol.Url

          #Get the user profile for owner of the personal site
          $uProfile = $profileManager.GetUserProfile($siteCol.Owner.LoginName)

          #Check if user profile has reference to personal site
          if($uProfile.PersonalSite -eq $null)
          {
             #If link has been removed, update the user profile
             Write-Host -ForegroundColor Red &quot;Personal Site is not linked&quot;
             Write-Host -ForegroundColor Green &quot;Updating the link&quot; $siteCol.RootWeb.ServerRelativeUrl

             #update the property (&quot;PersonalSpace&quot;)
             $uProfile[&quot;PersonalSpace&quot;].Value = $siteCol.RootWeb.ServerRelativeUrl
             $uProfile.Commit()
          }
     }
   })
}

UpdatePersonalSiteLinks &quot;http://mysite.local.domain/&quot;
</code></pre>

It is important to run this script under right context, otherwise it might fail (to fetch the personal sites or updating the user profiles). That is why i have used RunWithElevatedPrivileges, however this might not be sufficient in your environment. So be sure you execute this script with right user credentials. 