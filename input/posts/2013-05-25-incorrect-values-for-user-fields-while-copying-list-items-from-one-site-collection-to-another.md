---
id: 250
title: Incorrect values for User Fields while copying list items from one site collection to another
date: 2013-05-25T12:08:22+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=250
permalink: /2013/05/25/incorrect-values-for-user-fields-while-copying-list-items-from-one-site-collection-to-another/
disqus_identifier: https://www.sanjaybhagia.com/2013/05/25/incorrect-values-for-user-fields-while-copying-list-items-from-one-site-collection-to-another/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
categories:
  - 'C#'
  - SharePoint
  - Uncategorized
tags:
  - Copy list item
  - Cross Site Collection
  - List Items
  - SPFieldUser
  - SPFieldUserValue
  - User Fields
  - User Information List
---
<strong>Scenario</strong>
<p style="text-align:justify;">Recently, I was writting a piece of code to copy list item from a list in one site collection to the list in another site collection. I did the shallow copy (copying field by field).
Both the lists were created using same List definition so they had same fields and content types etc.
The item was successfully copied but then I noticed that the values of user fields were not correct.</p>
<p style="text-align:justify;">Here is the pice of code that i used for copying the item</p>

<pre><code class="csharp">
Public static void Main(string[] args)
{
    using (SPSite sourceSite = new SPSite(&quot;http://sp2010demo/sites/source&quot;))
    {
      using (SPWeb web = sourceSite .OpenWeb())
      {
          SPList sourceList= web.Lists.TryGetList(&quot;Source List&quot;);
          //fetch first item - for demonstration purpose
          SPListItem sourceItem = sourceList.Items[0];
          //instantiate destination site collection
          using (SPSite destinationSite = new SPSite(&quot;http://sp2010demo/sites/destination/&quot;))
          {
             var destinationList = destinationSite.RootWeb.Lists.TryGetList(&quot;DestinationList&quot;);
             if (destinationList != null)
             {
                //Add new item
                SPListItem destinationItem = destinationList.Items.Add();
                //copy item
                CopyListItem(sourceItem, destinationItem);
             }
          }
      }
   }
}

public static void CopyListItem(SPListItem sourceItem, SPListItem destinationItem)
{
        foreach (SPField field in sourceItem.Fields)
        { 
          //Filter out readonly and attachment fields
          if ((!field.ReadOnlyField) &amp;amp;&amp;amp; (field.InternalName != &quot;Attachments&quot;))
          {
                destinationItem[field.Title] = sourceItem[field.Title];
          }
        } 
        destinationItem.Update();
}
</code></pre>

<p style="text-align:justify;">Apparently, it looked just fine and i used the same piece of code to copy the list items from one list to another within the same site collection and it worked just perfect as it should !</p>
<p style="text-align:justify;"><strong>Problem</strong></p>
<p style="text-align:justify;">So, the problem was actually that when you move this item to a different site collection, users have to be present there in order for it to have the correct value.</p>
<p style="text-align:justify;">If you look at the internal representation of the user field it is basically a look up value and the value is represented something like this: <strong>1;#Sanjay Bhagia</strong></p>
<p style="text-align:left;">Where first number is the user id in User Information List in the site collection that can be accessed (only if you are admin) by navigating to the following url in your site <strong>_catalogs/users/simple.aspx</strong> (e.g., http://sp2010demo/sites/source/_catalogs/users/simple.aspx).</p>
<p style="text-align:justify;">So lets do some investigation.</p>
<p style="text-align:left;">I first open my source site collection i.e., http://sp2010demo/sites/source and navigate to my user information list by navigating to the following url http://sp2010demo/sites/source_catalogs/users/simple.aspx It shows all the users that have been added in my site</p>
<p style="text-align:justify;"><a href="/images/userinfosourcesite1.jpg"><img class="alignnone size-full wp-image-265" alt="userinfosourcesite" src="/images/userinfosourcesite1.jpg" width="600" height="105" /></a></p>
<p style="text-align:justify;">Now, if you put the cursor on the user link, you can see the entire url in the status bar of your browser. At the end of the url you can see the querystring <strong>ID </strong>that has value 1 in this example.</p>
<p style="text-align:justify;">Now lets navigate to the same user list in destination site collection by (http://sp2010demo/sites/destination/_catalog/users/simple.aspx).</p>
<p style="text-align:justify;">Note: In my environment, I already have my user added in both the site collections, that is why I'm able to see my user at both the places but it is possible that you don't see any user at both the places. User will be added automatically if you have logged in with that user on that site</p>
<p style="text-align:justify;"><a href="/images/userinfodestination1.jpg"><img class="alignnone size-full wp-image-266" alt="userinfodestination" src="/images/userinfodestination1.jpg" width="600" height="105" /></a></p>
<p style="text-align:justify;">By observing the url of the same user we can see that the user id for this user in destination site collection is <strong>10</strong>.</p>
<p style="text-align:justify;">So if I'm copying the list item that has user field and that field has my user added (i.e., Sanjay Bhagia) the representation of that user in my source site collection will be <strong>1;#Sanjay Bhagia</strong> but the representation of the same user in my destination site collection will be <strong>10;#Sanjay Bhagia</strong> !! Hence, the copied item will not display the proper user at the destination list item.</p>
<p style="text-align:justify;"><strong>Solution</strong>
It is quite easy to fix this issue, I basically fetched the value from user field and called SPWeb.EnsureUser() method to make sure the user is added in the User Information List in my destination site collection and updated the LookupId attribute of the SPUser object.</p>
<p style="text-align:justify;">Here is the modified code:</p>

<pre><code class="csharp">
public static void CopyListItem(SPListItem sourceItem, SPListItem destinationItem)
{
    foreach (SPField field in sourceItem.Fields)
    {
        //Filter out readonly and attachment fields
	if ((!field.ReadOnlyField) &amp;&amp; (field.InternalName != &quot;Attachments&quot;))
        {
            //only for user type fields
            if (field.GetType() == typeof(SPFieldUser))
            {
               SPWeb destinationParentWeb = destinationItem.ParentList.ParentWeb;
               if (sourceItem[field.Title] != null)
               {
                 //for field can have multiple selection enabled otherwise you can user SPFieldUserValue class
                 SPFieldUserValueCollection users = new SPFieldUserValueCollection(sourceItem.ParentList.ParentWeb, sourceItem[field.Title].ToString());
                 foreach (SPFieldUserValue user in users)
                 {
                    //this LookupId is the number of user object in User Information List
                    user.LookupId = destinationParentWeb.EnsureUser(user.User.LoginName).ID;
                 }
                destinationItem[field.Title] = users;
               }
            }
            else
              destinationItem[field.Title] = sourceItem[field.Title];
                    }
        }
    destinationItem.Update();
}
</code></pre>

So basically, I'm making sure that user object in destination site collection has the correct look up id, that's it!

Hope it helps.