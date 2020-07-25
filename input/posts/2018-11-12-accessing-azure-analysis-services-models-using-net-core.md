---
id: 586
title: Accessing Azure Analysis Services Models using .NET Core
date: 2018-11-12T14:31:12+02:00
author: sanjaybhagia
layout: post
guid: https://www.sanjaybhagia.com/?p=586
permalink: /2018/11/12/accessing-azure-analysis-services-models-using-net-core/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:69:"https://cdn-images-1.medium.com/fit/c/400/400/0*ERb6Is_QqdMUQz6C.jpeg";s:10:"author_url";s:32:"https://medium.com/@bhagiasanjay";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"a80fa315af70";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:5:"draft";s:3:"url";s:45:"https://medium.com/@bhagiasanjay/a80fa315af70";}'
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
disqus_identifier: https://www.sanjaybhagia.com/2018/11/12/accessing-azure-analysis-services-models-using-net-core/
categories:
  - Azure
  - 'C#'
tags:
  - .net-core
  - adal.net
  - adomd.net
  - analysis-services
  - azure
---
***Update (2020-07-21):*** Microsoft recently [announced](https://azure.microsoft.com/en-us/updates/net-core-support-for-azure-analysis-services-client-libraries-is-in-preview/) the .NET Core support for Azure Analysis Services client libraries in preview ([AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.NetCore.retail.amd64) and [ADOMD.NET](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.NetCore.retail.amd64)). You can use these packages instead of the Unofficial packages which I have based this blogpost on. These packages are still in preview though, so you can't (or shoudn't) use these in your production environment. I have added another console application project in my project which you can checkout [here](https://github.com/sanjaybhagia/azure-analysis-services-netcore-sample/tree/master/ConsoleApp/ConsoleAppOfficialLibs). I just updated the reference to the nuget packge, nothing else. These libraries should work similar to the full .NET framework ones.

---

<!-- wp:quote {"className":"is-style-default"} -->
<blockquote class="wp-block-quote is-style-default"><p>Azure Analysis Services is a fully managed platform as a service (PaaS) that provides enterprise-grade data models in the cloud. Use advanced mashup and modeling features to combine data from multiple data sources, define metrics, and secure your data in a single, trusted tabular semantic data model. The data model provides an easier and faster way for users to browse massive amounts of data for ad-hoc data analysis.<br/></p><cite>Refer to <a href="https://docs.microsoft.com/en-us/azure/analysis-services/analysis-services-overview" target="_blank" rel="noreferrer noopener">Microsoft official documentation</a> to read more about Azure Analysis Services</cite></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>Programming against Analysis services is nothing new and we have been doing it for a long time with the full .NET framework, the most common approach is using ADOMD.Net. In this blog post, I will go through the process of getting the same task done with .NET Core.  For this sample, I'm using .NET Core 2.1.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><a href="https://github.com/sanjaybhagia/azure-analysis-services-netcore-sample" target="_blank">Look at my GitHub repository for the entire source code for this blogpost</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>The important thing to note here is that there is no official <g class="gr_ gr_4 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="4" data-gr-id="4">nuget</g> package from Microsoft for ADOMD.NET yet, but I found an unofficial package here (<a href="https://github.com/bdebaere/Unofficial.Microsoft.AnalysisServices.AdomdClientNetCore" target="_blank" rel="noopener">Unofficial.Microsoft.AnalysisServices.AdomdClientNetCore</a>) and it seems to work for my quick test (you have to make a call if you want to use it in production or not). I couldn't find any official word on this anywhere I looked for.  <em>Besides this </em><g class="gr_ gr_5 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="5" data-gr-id="5">nuget</g><em> package for .net core rest of the stuff should work same in full framework (with official </em><g class="gr_ gr_6 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="6" data-gr-id="6">nuget</g><em> for ADOMD.NET)</em></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>I have divided this into several steps so that it is easy to follow. So let's get started!</p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2>Step 1: Create Azure Analysis Service resource</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>The very first thing we need is the Analysis Server and model in Azure. Follow <a href="https://docs.microsoft.com/en-us/azure/analysis-services/analysis-services-create-server" target="_blank">this</a> quick starter to create the server.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Next is to create a model which we will use to query. You can create a model with sample data (adventure works) right from within your Analysis Server.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Click 'Manage' in the blade and click 'New Model'. Select 'Sample data' from the drop down and press 'Add'. It should add the model for you. </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":598} -->
<figure class="wp-block-image"><img src="/images/image-3.png" alt="" class="wp-image-598"/><figcaption>Create a model Analysis Services</figcaption></figure>
<!-- /wp:image -->

<!-- wp:image {"id":597} -->
<figure class="wp-block-image"><img src="/images/image-2.png" alt="" class="wp-image-597"/><figcaption>Model successfully created</figcaption></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 id="mce_26">Step 2: Create App Service Principal</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>There are many ways to access analysis services. Simplest is <g class="gr_ gr_5 gr-alert gr_spell gr_inline_cards gr_disable_anim_appear ContextualSpelling ins-del multiReplace" id="5" data-gr-id="5">using</g> a connection string that has <g class="gr_ gr_12 gr-alert gr_spell gr_inline_cards gr_disable_anim_appear ContextualSpelling ins-del multiReplace" id="12" data-gr-id="12">usrename</g> and password. But this is not recommended approach and works only with <g class="gr_ gr_209 gr-alert gr_gramm gr_inline_cards gr_disable_anim_appear Style multiReplace" id="209" data-gr-id="209">full .</g>Net framework but <g class="gr_ gr_14 gr-alert gr_gramm gr_inline_cards gr_run_anim Style multiReplace" id="14" data-gr-id="14">not .</g>Net Core (I was pointed by <a href="https://github.com/bdebaere/Unofficial.Microsoft.AnalysisServices.AdomdClientNetCore/issues/1" target="_blank">bdebaere in his GitHub respo</a> regarding this), so we want to authenticate with other OAuth flows. For this post, we will use token-based authentication. For <g class="gr_ gr_832 gr-alert gr_gramm gr_inline_cards gr_run_anim Punctuation only-ins replaceWithoutSep" id="832" data-gr-id="832">this</g> we will need an app principal (or Azure AD App)</p>
<!-- /wp:paragraph -->

<!-- wp:list {"ordered":true} -->
<ol><li>Sign in to <a href="https://portal.azure.com/" target="_blank" rel="noreferrer noopener">Azure Portal</a>.</li><li>Navigate to Azure Active Directory -> App Registrations and Click New application registration.</li><li>Register an app with the following settings:<ul><li>Name: any name</li><li>Application type: Web app/API,</li><li>Sign-on URL:  https://westeurope.asazure.windows.net (<em>Not really important here, you can provide any valid <g class="gr_ gr_14 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="14" data-gr-id="14">url</g></em>).<br/></li></ul></li><li>Once the app is created, navigate to 'Keys' and add a new key<ul><li>provide the description and select duration and press Save button</li><li>after that you will be able to see the key <strong>it will appear only once</strong> so take note of this key and we will use this later on</li></ul></li><li><g class="gr_ gr_127 gr-alert gr_gramm gr_inline_cards gr_run_anim Punctuation only-ins replaceWithoutSep" id="127" data-gr-id="127">Also</g> take note of the Application Id from the main page of the application</li></ol>
<!-- /wp:list -->

<!-- wp:image {"id":600} -->
<figure class="wp-block-image"><img src="/images/image-5.png" alt="" class="wp-image-600"/><figcaption>Setting access key for Azure AD App</figcaption></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 id="mce_12">Step 3: Assign your user as Service Admin in order to connect from SSMS</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Registering an app is not enough. We need to assign access to this app on  Analysis Service Model (<em>adventureworks model that we created in previous step)</em>. In order to give this access, we will need SQL Server Management Studio. </p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Before we could use that, we need a way to connect to this analysis services instance via SSMS. For this, we need to set up our account as Service Admin. Navigate to the Analysis Services resource that we created in the first step. Click 'Analysis Services Admin'. Normally your subscription account is set as the admin (this is what I will be using) but you are free to set up <g class="gr_ gr_270 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del" id="270" data-gr-id="270">any</g> account you wish appropriate. </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":601} -->
<figure class="wp-block-image"><img src="/images/image-6.png" alt="" class="wp-image-601"/><figcaption>Setting Sevice Admin for Analysis Service</figcaption></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 id="mce_28">Step 4: Grant permissions to app principal on the model</h2>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol><li>Connect to Analysis Service using SSMS 2017 with your account that you assigned as Service Admin in the previous step<ul><li>You will need the Server name (from Step 1)</li></ul></li><li>Select the database model and click on Roles or add a new Role<ul><li>Choose any name </li><li>Select 'Read' database permission for the role </li></ul></li><li>Add the Service principal to any role in below format (search for the app name)</li></ol>
<!-- /wp:list -->

<!-- wp:image {"id":604} -->
<figure class="wp-block-image"><img src="/images/image-9.png" alt="" class="wp-image-604"/><figcaption>Connect to Azure Analysis Server with Service Admin account<br/></figcaption></figure>
<!-- /wp:image -->

<!-- wp:image {"id":612} -->
<figure class="wp-block-image"><img src="/images/image-13.png" alt="" class="wp-image-612"/><figcaption>Adding App principal as a member in newly defined role</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>This will add user with following convention:  app:&lt;appid>@&lt;tenantid></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><em>appid</em>: is the application id for your app you created in <g class="gr_ gr_108 gr-alert gr_gramm gr_inline_cards gr_disable_anim_appear Grammar only-del replaceWithoutSep" id="108" data-gr-id="108">the Step</g> 2.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><em>tenantid</em> - is the id of your subscription (you can find this in Properties of your Azure Active Directory)</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong><em>This didn't work for me when I <g class="gr_ gr_32 gr-alert gr_gramm gr_inline_cards gr_run_anim Style multiReplace" id="32" data-gr-id="32">tried </g></em></strong><strong><em><g class="gr_ gr_32 gr-alert gr_gramm gr_inline_cards gr_disable_anim_appear Style multiReplace" id="32" data-gr-id="32"> to</g> use <g class="gr_ gr_29 gr-alert gr_gramm gr_inline_cards gr_run_anim Grammar only-ins replaceWithoutSep" id="29" data-gr-id="29">Azure</g> subscription with my personal account (hotmail</em></strong><strong><em>) so I had to use my company account subscription to make this work. </em></strong></p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2 id="mce_30">Step 5: Write the code to access data</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Now we are all set up write our code that reads from the Model. Please refer to the entire source code in my <a href="https://github.com/sanjaybhagia/azure-analysis-services-netcore-sample" target="_blank">GitHub <g class="gr_ gr_4 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="4" data-gr-id="4">respository</g></a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Important method here <em>GetAccessToken.</em> I'm using ADAL.Net (<g class="gr_ gr_11 gr-alert gr_spell gr_inline_cards gr_disable_anim_appear ContextualSpelling ins-del multiReplace" id="11" data-gr-id="11">nuget</g>: <em>Microsoft.IdentityModel.Clients.ActiveDirectory</em>) to grab the token for the service principal from Azure AD<em>.</em> </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":607,"linkDestination":"custom"} -->
<figure class="wp-block-image"><a href="https://github.com/sanjaybhagia/azure-analysis-services-netcore-sample/blob/master/ConsoleApp/ConsoleApp/Program.cs"><img src="/images/image-12.webp" alt="" class="wp-image-607"/></a><figcaption>Method to acquire token from Azure AD to access analysis services</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Once we have the token, we are good to access data from the model. Here I'm using the unofficial NuGet package for ADOMD.NET that I mentioned previously.  The correct  Connection String format is: </p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>“<strong>Provider=MSOLAP;Data Source=&lt;url of the Azure Analysis Server>;Initial Catalog=&lt;modelname>;User ID=;Password=&lt;access token here>;Persist Security Info=True;Impersonation Level=Impersonate</strong>“</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>User ID is left empty and Password is the access token which we get from Azure AD. </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":606,"linkDestination":"custom"} -->
<figure class="wp-block-image"><a href="https://github.com/sanjaybhagia/azure-analysis-services-netcore-sample/blob/master/ConsoleApp/ConsoleApp/Program.cs"><img src="/images/image-11.png" alt="" class="wp-image-606"/></a><figcaption>Method to read data from Analysis Services Model <em>adventureworks</em></figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>If you run this, you will see the output in <g class="gr_ gr_35 gr-alert sel gr_gramm gr_replaced gr_inline_cards gr_disable_anim_appear Grammar only-ins doubleReplace replaceWithoutSep" id="35" data-gr-id="35">the </g>console</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":605} -->
<figure class="wp-block-image"><img src="/images/image-10.png" alt="" class="wp-image-605"/><figcaption>Final output of the program</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Have you tried to work with Azure Analysis services in .NET Core? How was your experience? I would be very interested in listening to your experience and challenges.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Cheers</p>
<!-- /wp:paragraph -->