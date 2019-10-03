---
id: 282
title: Associating OOB Approval workflow with custom list for different Locale (LCIDs)
date: 2013-10-20T16:55:50+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=282
permalink: /2013/10/20/associating-oob-approval-workflow-with-custom-list-for-different-locale-lcids/
disqus_identifier: https://www.sanjaybhagia.com/2013/10/20/associating-oob-approval-workflow-with-custom-list-for-different-locale-lcids/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
categories:
  - 'C#'
  - SharePoint
  - Uncategorized
tags:
  - Approval Workflow
  - BaseId
  - LCID
  - Locale
  - Workflow Association
  - Workflow Templates
  - Workflows
---
<strong>Scenario:</strong> I have a custom web template that is used to provision the site. Among other things (branding etc), a custom documents library is provisioned whenever a new site is created (also based on my custom documents library template). Besides creating the custom library, I need to associate the OOB Approval workflow with the library that will be used as a publishing workflow. So if any user publishes the major version of the document it goes through the approval process.

<strong>Problem/Limitation:</strong> Associating the workflow with any list or library is fairly straight forward. Here are the steps:
<ol>
 	<li>Fetch the workflow template from parent web where list exist</li>
 	<li>Get the target list</li>
 	<li>Create the workflow association</li>
 	<li>Check if workflow is already associated with the list</li>
 	<li>If workflow is not associated, add the workflow association to the list</li>
</ol>
Here is the code snippet for performing this task.
<pre><code class="csharp">private void AssociateApprovalWorklowWithList(SPList list)
{
SPWeb web = list.ParentWeb;
SPSite site = web.Site;
try
{
bool allowUnsafeCurrent = web.AllowUnsafeUpdates;
web.AllowUnsafeUpdates = true;

//Associate the approval workflow with libraries
//based on Custom Documents template only
if (list != null &amp;&amp; list.BaseTemplate.ToString() == "10050")
{
//Get Approval Workflow Template Base Id
Guid workflowBaseId = new Guid("8ad4d8f0-93a7-4941-9657-cf3706f00409");

//If workflow is already associated, don't re-associate
if (list.WorkflowAssociations.GetAssociationByBaseID(workflowBaseId) != null)
return;

// Get workflow history and task list
SPList historyList = EnsureHistoryList(web);
SPList taskList = EnsureTasksList(web);

//set the name of workflow association
string workflowAssociationName = string.Format("{0} - Approval", list.Title);

//Get workflow template by Base Id
SPWorkflowTemplate workflowTemplate = web.WorkflowTemplates.GetTemplateByBaseID(workflowBaseId);
if (workflowTemplate == null)
{
//Log - no template found and return
return;
}

// Create workflow association
SPWorkflowAssociation workflowAssociation =
SPWorkflowAssociation.CreateListAssociation(workflowTemplate, workflowAssociationName,
taskList, historyList);

// Add workflow association to custom list
list.WorkflowAssociations.Add(workflowAssociation);

workflowAssociation.AssociationData = String.Empty;

//.. details omitted
list.WorkflowAssociations.Update(workflowAssociation);
list.DefaultContentApprovalWorkflowId = workflowAssociation.Id;
list.Update();

web.AllowUnsafeUpdates = allowUnsafeCurrent;
}
}
catch (Exception ex)
{
//Log exception
}
finally
{
//Dispose objects
}
}</code></pre>
The above code snippet will work just fine. But there is a little problem.

What if the site is created on language other than English? One case ask, why would there be any problem as we are fetching the workflow template by Base Id. Well, it turned out that the Base Id is not the same across different languages, it varies. So it failed if site has different locale.

There is also another method GetWorkflowTemplateByName but it didn't work out for me as well, even though I set the right culture.. So that means, we can not use both the methods.

<strong>Solution:</strong> Trying both the methods to get the template didn't work and I didn't get anything by googling it either. So the only way out of this problem was the following:

Looking at the BaseIds of different sites, a pattern emerged. Let's examine the BaseIds from English and Swedish sites, as we saw previously
<pre>8ad4d8f0-93a7-4941-9657-cf3706f00409 (English, LCID: 1033)
8ad4d8f0-93a7-4941-9657-cf3706f0041D (Swedish, LCID: 1053)</pre>
If you look closely, almost entire Id is same except last three digits Let's take some more examples:
<pre>8ad4d8f0-93a7-4941-9657-cf3706f00415 (Polish, LCID: 1045)
8ad4d8f0-93a7-4941-9657-cf3706f00406 (Danish, LCID: 1030)</pre>
as you can see, only the last three digits vary from language to language.

Now take the LCID and convert it to hexadecimal representation (thanks to my colleague <a title="Matthias Einig" href="https://twitter.com/mattein">Matthias Einig</a> for breaking this code ;) )

Lets say,  for:

LCID = 1033 =&gt; hex = 409

LCID = 1053 =&gt; hex = 41d

LCID = 1045 =&gt; hex = 415

So, it became clear that the last three digits that differ are basically the hexadecimal representation of LCID. Having identified this pattern, I simply converted the LCID to hex value and there we have it, the BaseId for approval workflow template for current language

So,  if we change the line #15 in our code snippet above to the following:
<pre><code class="csharp">Guid workflowBaseId = new Guid("8ad4d8f0-93a7-4941-9657-cf3706f00"+ web.Language.ToString("X"));</code></pre>
With this change, now no matter which locale the site has, it will construct the right workflow template id and will fetch the workflow template for association.

Hope it helps some folks out there :)