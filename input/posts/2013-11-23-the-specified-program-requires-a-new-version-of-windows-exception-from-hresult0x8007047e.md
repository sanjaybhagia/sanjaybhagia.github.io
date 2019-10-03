---
id: 291
title: The specified program requires a new version of Windows. (Exception from HRESULT:0x8007047E)
date: 2013-11-23T16:08:11+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=291
permalink: /2013/11/23/the-specified-program-requires-a-new-version-of-windows-exception-from-hresult0x8007047e/
disqus_identifier: https://www.sanjaybhagia.com/2013/11/23/the-specified-program-requires-a-new-version-of-windows-exception-from-hresult0x8007047e/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
categories:
  - 'C#'
  - SharePoint
  - Uncategorized
tags:
  - Approval Workflow
  - BaseId
  - EnableModeration
  - LCID
  - ModeratedList
  - Workflow Association
  - Workflow Templates
  - Workflows
---
<strong>Scenario</strong>:
I have a custom list definition based on which I want to create some lists and associate OOB approval workflow with them for content approval.

In the schema.xml file for my custom list definition (<em>based documents library i.e., basetype=1</em>), I set ModeratedList=“True” attribute among others (see in the code snippet below)
<pre><code class="xml">&lt;List xmlns:ows="Microsoft SharePoint" Title="TestList" Direction="$Resources:Direction;" Url="TestList" BaseType="1"
xmlns="http://schemas.microsoft.com/sharepoint/" EnableContentTypes="TRUE" EnableMinorVersions="TRUE" VersioningEnabled="TRUE" DraftVersionVisibility="2" ModeratedList="TRUE”&gt;
</code></pre>
Now, I added a ListAdded event receiver that listens to my custom list definition,so whenever any list is created based on my custom list definition, the OOB approval workflow should be associated with the list.

Here is the code snippet that associates the OOB approval workflow with the list
<pre><code class="csharp">
///
/// Associate OOB Approval workflow with the list
///
///
public static void AssociateApprovalWorklowWithList(SPList list)
{
  //variables
  Guid listId = list.ID;
  Guid webId = list.ParentWeb.ID;
  Guid siteId = list.ParentWeb.Site.ID;
  SPSite site = null;
  SPWeb web = null;

  try
  {
    site = new SPSite(siteId);
    web = site.OpenWeb(webId);
    bool allowUnsafeCurrent = web.AllowUnsafeUpdates;
    web.AllowUnsafeUpdates = true;
    list = web.Lists[listId];

    //Get Approval Workflow Template Base Id
    Guid workflowBaseId = new Guid("8ad4d8f0-93a7-4941-9657-cf3706f00"+ web.Language.ToString("X"));

    //If workflow is already associated, don't re-associate
    if (list.WorkflowAssociations.GetAssociationByBaseID(workflowBaseId) != null)
       return;

    //check if workflows feature is activated, if not activate the feature
    SPFeature workflowsFeature = web.Site.Features[WorkflowId];

    if (workflowsFeature == null)
      web.Site.Features.Add(WorkflowId);

    // Get workflow history and task list
    SPList historyList = EnsureHistoryList(web);
    SPList taskList = EnsureTasksList(web);
    string workflowAssociationName = string.Format("{0} - Approval", list.Title);
    SPWorkflowTemplate workflowTemplate = web.WorkflowTemplates.GetTemplateByBaseID(workflowBaseId);
    if (workflowTemplate == null)
    {
     //Log exception
     return;
    }

    workflowTemplate.AllowManual = false;

    // Create workflow association
    SPWorkflowAssociation workflowAssociation = SPWorkflowAssociation.CreateListAssociation(workflowTemplate,
    workflowAssociationName, taskList, historyList);

    var associationDataXml = XElement.Parse(workflowAssociation.AssociationData);
    // Add workflow association to my list
    list.WorkflowAssociations.Add(workflowAssociation);

    //Set workflow association data
    workflowAssociation.AssociationData = Add_Association_Data(web, associationDataXml);

    // Enable workflow
    if (!workflowAssociation.Enabled)
        workflowAssociation.Enabled = true;

    if (list.DraftVersionVisibility != DraftVisibilityType.Approver)
       list.DraftVersionVisibility = DraftVisibilityType.Approver;

    list.WorkflowAssociations.Update(workflowAssociation);
    list.DefaultContentApprovalWorkflowId = workflowAssociation.Id;
    list.Update();
    web.AllowUnsafeUpdates = allowUnsafeCurrent;
  }
  catch (Exception ex)
  {
    //Log Exception
  }
  finally
  {
    //Dispose the objects
    if (web != null)
       web.Dispose();
    if (site != null)
       site.Dispose();
   }
}

</code></pre>
For the explanation of retrieving the workflow template id (line#23), please refer to my previous post here <a href="http://sanjaybhagia.com/2013/10/20/associating-oob-approval-workflow-with-custom-list-for-different-locale-lcids/">Associating OOB Approval workflow with custom list for different Locale (LCIDs)</a>

<strong>Problem:</strong>
Once I have deployed the solution and everything is hooked up, I create the list based on my custom list definition and the process ends with the following exception:

<img class="aligncenter" src="/images/pastedgraphic-1.png" alt="PastedGraphic-1" width="600" height="279" />

<span style="line-height: 1.5;">But when I go to the site, the list is created and workflow is associated properly. </span>

<strong>Solution:</strong>
<span style="line-height: 1.5;">After narrowing down the problem, I removed the <strong>ModeratedList</strong> attribute from List element in schema.xml file for custom list definition (shown in the first code snippet), enabled the content approval for the list in the code and redeployed the solution and everything worked !</span>

Here is the code for enabling content approval through code (add this in the function defined above after setting <em>DraftVersionVisibility</em> property)
<pre><code class="csharp">
  if(!list.EnableModeration)
    list.EnableModeration = true;
</code></pre>
Cheers