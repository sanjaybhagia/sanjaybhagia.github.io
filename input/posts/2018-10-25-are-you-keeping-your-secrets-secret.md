---
id: 538
title: Are you keeping your secrets secret?
date: 2018-10-25T21:42:58+02:00
author: sanjay.bhagia@gmail.com
layout: post
guid: https://www.sanjaybhagia.com/?p=538
permalink: /2018/10/25/are-you-keeping-your-secrets-secret/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:69:"https://cdn-images-1.medium.com/fit/c/400/400/0*ERb6Is_QqdMUQz6C.jpeg";s:10:"author_url";s:32:"https://medium.com/@bhagiasanjay";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"9a03e1b8a428";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:5:"draft";s:3:"url";s:45:"https://medium.com/@bhagiasanjay/9a03e1b8a428";}'
disqus_identifier: https://www.sanjaybhagia.com/2018/10/25/are-you-keeping-your-secrets-secret/
categories:
  - Azure
tags:
  - azure
  - keyvault
  - secrets
  - storage
  - webapp
---
<!-- wp:paragraph -->
<p><strong>As developers</strong>, we are all guilty of leaking sensitive information of our applications and systems more than we would perhaps like to admit. Don't get me wrong, I'm not talking about breaking the <g class="gr_ gr_18 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="18" data-gr-id="18">NDAs</g> with our clients but about those little connection strings to our databases and keys to our storage accounts where we hold all our information that we want to protect from outside world. As developers, we understand that this information is sensitive and still we check-in those along with the rest of our code base which eventually ends up in our version control systems that are hosted in the cloud somewhere. And we firmly believe that this is going to be fine. This is akin to watching a dreadful accident on NEWS and saying this can't happen to us.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>"The Cloud" is all beautiful and powerful but with power comes responsibility. Among all people, we developers should be well aware and feel responsible &amp; accountable for handling the sensitive information of our applications. We must ensure that it doesn't leak under any circumstances which can jeopardize our systems (and also our positions). And believe it or not cloud has made these things easier and we have all the tools we need to make it happen. It is not complicated anymore, you just need to be a bit thoughtful and set up the habit to do this when you are starting your project (or even better put these things if you end up in a project where this is lacking).</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Ok, enough talking let's get to the meat of this post. In this blog post, I will create a Web Application (hosted in Microsoft Azure) which lists URIs of blobs from a container in Storage Account (Microsoft Azure). For the sake of simplicity, I'm creating the container and a dummy blob at the runtime. The Web app is a standard ASP.Net Core web app. I will present two implementations of this scenario.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>The first implementation would be with the connection string of a Storage account stored in Application Settings of the web application directly. In the second part, I will move that connection string from the web application and keep it somewhere safe and will fetch information from the Storage account as earlier. Both the applications are deployed using ARM Templates and completely automated (<em>You can create these resources manually if you so</em> <em>wish</em>).</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3><strong>Part I</strong></h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p><a href="https://github.com/sanjaybhagia/webappsecretsdemo/tree/master/WebAppsWithSecrets" target="_blank" rel="noopener">Here</a> is the source code for this part. I will refer to some bits and pieces of this below:</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Here is how the setup looks like: </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":539} -->
<figure class="wp-block-image"><img src="/images/image-25.png" alt="" class="wp-image-539"/><figcaption>Web App reading from Storage Account directly using Connection String</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>If you look at the source code (<a href="https://github.com/sanjaybhagia/webappsecretsdemo/tree/master/WebAppsWithSecrets" target="_blank" rel="noopener">here</a>), in my <em>WebApWithSecrets</em> solution, I have two projects:</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":540} -->
<figure class="wp-block-image"><img src="/images/image-26.png" alt="" class="wp-image-540"/><figcaption>Solution structure - WebAppWithSecrets</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p><strong>deployment</strong> project contains the ARM template for provisioning the resources. The ARM Template does the following</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul><li>provisions a Storage Account<br/></li><li>provisions the web application</li><li>adds 'StorageAccountConnectionString' as a Connection String in the Web Application</li></ul>
<!-- /wp:list -->

<!-- wp:paragraph -->
<p>You have instructions to deploy this template in GitHub repository at the above URL (if you want to try it out yourself). Once the resources are provisioned, this is how my resource group looks like when I see it in Azure Portal.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":558} -->
<figure class="wp-block-image"><img src="/images/image-38.png" alt="" class="wp-image-558"/><figcaption>Three resources provisioned by ARM template in Azure subscription</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>If I navigate to my Web Application and look at the Application Settings, this is what I see: a connection string with the name 'StorageAccountConnectionString' </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":545} -->
<figure class="wp-block-image"><img src="/images/image-31.png" alt="" class="wp-image-545"/><figcaption>Connection String set in Web Application after deployment</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Once the resources are provisioned and in place, we can push the code to our web application. You can push this in a variety of ways but for now, I will just right-click and publish (<strong>WebAppsWithSecrets</strong> project (<em>don't do this in production</em>). Once the code is pushed successfully, the web application will open up in my browser (otherwise navigate to the web application). This is how it looks for me (the red box is my custom code that lists the URIs):</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":547} -->
<figure class="wp-block-image"><img src="/images/image-32.png" alt="" class="wp-image-547"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>At this point, the app is fully functional and there is nothing wrong here. If I navigate to <a href="https://github.com/sanjaybhagia/webappsecretsdemo/blob/master/WebAppsWithSecrets/WebAppsWithSecrets/appsettings.json" target="_blank" rel="noopener"><g class="gr_ gr_8 gr-alert gr_gramm gr_inline_cards gr_run_anim Grammar only-ins doubleReplace replaceWithoutSep" id="8" data-gr-id="8">appsettings.json</g></a> file in my source code, I will find this: a connection string to my storage account which my code picks and fetches content from Storage Account (you will find the code to render these URIs in <a href="https://github.com/sanjaybhagia/webappsecretsdemo/blob/master/WebAppsWithSecrets/WebAppsWithSecrets/Pages/Index.cshtml.cs" target="_blank" rel="noopener">Index.cshtml.cs</a> file)</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":548} -->
<figure class="wp-block-image"><img src="/images/image-33.png" alt="" class="wp-image-548"/><figcaption>Connection String in appsettings.json in the solution</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>I have this details not only to me but this is now checked-in to my source control.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>We can all argue that it is not that bad after all since this is hosted in GitHub (or Azure DevOps or whatever the version control system you are using). These are secure systems and only our team members have access to them - so why bother? </p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>This all holds true until someone gets hold of your GitHub account or you haven't configured the permissions correctly and someone who shouldn't have the detail might get access. Put it simply, we are increasing the attack surface and we might have to deal with some unwanted situations later on. The good news is we don't necessary have to do it this way, there is a better alternative which I will demonstrate in next part.</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3><strong>Part II</strong></h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p><a href="https://github.com/sanjaybhagia/webappsecretsdemo/tree/master/WebAppsWithoutSecrets" target="_blank">Here</a> is the source code for this part. I will refer to some bits and pieces of this below:</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>The alternative approach could be that we keep such sensitive information in some sort of secured place or vault which is encrypted and secure which we can rely on and our application simply <em>asks</em> for this information from that vault without ever knowing the details itself. We sort of want to delegate the responsibility of handling sensitive information of our application to some other party without us worrying about the implications. By doing this we not only simplify our lives but also gain much more benefits like it would be better to govern these secrets, we would be able to update the keys and connection strings and certificates without changing anything in our application. The good news is Microsoft Azure has exactly such <g class="gr_ gr_9 gr-alert gr_gramm gr_inline_cards gr_run_anim Grammar only-ins replaceWithoutSep" id="9" data-gr-id="9">offer</g> called 'Azure KeyVault'. You can read about this more <a href="https://docs.microsoft.com/en-us/azure/key-vault/key-vault-overview" target="_blank" rel="noopener">here</a>.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>This is how the redesign of our application looks like after introducing the Azure KeyVault: </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":550} -->
<figure class="wp-block-image"><img src="/images/image-35.png" alt="" class="wp-image-550"/><figcaption>Solution design after introducing Azure Key Vault</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>The Web Application has information about the KeyVault only and delegates the responsibility of retrieving ConnectionString to Storage Account to the KeyVault.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>If you look at the source code (<a href="https://github.com/sanjaybhagia/webappsecretsdemo/tree/master/WebAppsWithoutSecrets" target="_blank" rel="noopener">here</a>). In <em>WebApWithourSecrets</em> solution, I have two projects, same as before.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":574} -->
<figure class="wp-block-image"><img src="/images/image-24.png" alt="" class="wp-image-574"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>I have updated my ARM template for this. The template now does the following:</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul><li>provisions a Storage Account</li><li>provisions the KeyVault</li><li>provisions the web application</li><li>adds 'StorageAccountConnectionString' as a <em>Secret</em> in the KeyVault<br/></li><li>sets the name of the KeyVault in application settings</li></ul>
<!-- /wp:list -->

<!-- wp:paragraph -->
<p>There is one catch<g class="gr_ gr_18 gr-alert sel gr_gramm gr_replaced gr_inline_cards gr_disable_anim_appear Punctuation only-ins replaceWithoutSep" id="18" data-gr-id="18">, </g>however. You might ask, wait for a second here! Fine, I moved my storage account's connection string to the KeyVault but I still need details of the vault in my web application to read anything from it. Though I moved the secret of storage account my web application now holds even more sensitive information - all the secrets !! I feel you, I'm with you. But don't worry, this is a solved problem. Microsoft quickly realized this and provided a very nice solution for this very problem. They introduced something called 'Managed Service Identity' aka MSI (now being renamed to just Managed identities) a while back. You can read more about this <a href="https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview" target="_blank" rel="noopener">here</a> but in short, it abstracts this detail in such a way that you don't need to keep any ClientId/ClientSecret or Key of the vault in your web application. You simply enable Managed Identity in your web application (it creates an app principle which gets permissions to read from the Vault). <a href="https://blog.kloud.com.au/2018/04/13/demystifying-managed-service-identities-on-azure/" target="_blank" rel="noopener">Here</a> is a really good premier of MSI if you want to get a deeper understanding of the feature.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>If you navigate to <a href="https://github.com/sanjaybhagia/webappsecretsdemo/blob/master/WebAppsWithoutSecrets/WebAppsWithoutSecrets/Program.cs" target="_blank" rel="noopener"><em>Program.cs</em></a> file in the solution, here is code that hooks up the KeyVault into our application (BuildWebHost method):</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code lang="clike" class="language-clike">public static IWebHost BuildWebHost(string[] args) =>
     WebHost.CreateDefaultBuilder(args)
     //this is where KeyVault magic happens - we are setting up configurations from Azure KeyVault using Managed Service Identity
     //without specifiying any details of the Azure KeyVault itself (except the Url of the vault)
    .ConfigureAppConfiguration((context, config) =>
    {
       var builtConfig = config.Build();
       var keyVaultUrl = $"https://{builtConfig["KeyVaultName"]}.vault.azure.net";

       //this comes with .net core 2.1
       config.AddAzureKeyVault(keyVaultUrl);
                
       //if using 2.0, you should use this apporach
       //AzureServiceTokenProvider - this is the magic piece that makes it seamless to work with MSI
       //var azureServiceTokenProvider = new AzureServiceTokenProvider();
       //var keyVaultClient = new KeyVaultClient(
       //new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
       //config.AddAzureKeyVault(keyVaultUrl, keyVaultClient, new DefaultKeyVaultSecretManager());
       })
       .UseStartup&lt;Startup>()
       .Build();</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>if you look closely, all information we have about the Azure KeyVault in our web application is the name (or URL if you will) of the KeyVault (<em>KeyVaultName</em>) stored in our appsettings.json (or Application Settings in the published web app). Everything else is managed by MSI for us. <br/></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>In ARM template, we enable MSI for the web application with this property: </p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code lang="json" class="language-json">"identity": {
     "type": "SystemAssigned"
   }	</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>This enables this setting for the web application</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":569} -->
<figure class="wp-block-image"><img src="/images/image-23.png" alt="" class="wp-image-569"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>So, as we did it Part I, let's deploy the template to provision new set of resources for us (You will find instructions in <g class="gr_ gr_4 gr-alert gr_gramm gr_inline_cards gr_run_anim Grammar only-ins replaceWithoutSep gr-progress sel" id="4" data-gr-id="4">GitHub</g> repository for this). Once resources are provisioned, this is how it looks for me in Azure Portal:</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":559} -->
<figure class="wp-block-image"><img src="/images/image-40.png" alt="" class="wp-image-559"/><figcaption>Four resources provisioned by ARM template in Azure Account</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>If i navigate to the application settings of my web application, this is what I see: </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":556} -->
<figure class="wp-block-image"><img src="/images/image-37.png" alt="" class="wp-image-556"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Notice, there is no connection string as we had earlier, instead there is an App Settings 'KeyVaultName' which contains the name of the vault. That's the only information this web application has. Let's deploy the code for the web application (like earlier, right-click and publish). After <g class="gr_ gr_6 gr-alert gr_spell gr_inline_cards gr_run_anim ContextualSpelling ins-del multiReplace" id="6" data-gr-id="6">successfull</g> deployment, this is what you should see: </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":566} -->
<figure class="wp-block-image"><img src="/images/image-22.png" alt="" class="wp-image-566"/><figcaption>Web Application without Secrets after successful deployment</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>This looks exactly same as previous web application since we haven't changed the logic. The only thing that was changed in this web app was to read connection string from KeyVault. </p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Now let's uncover some more details. If you navigate to the Azure KeyVault resource and then head over to <em>Secrets</em>, you will see this: </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":560} -->
<figure class="wp-block-image"><img src="/images/image-41.png" alt="" class="wp-image-560"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>If you pay attention, you don't see anything, instead, it tells you that "<em>You are unauthorized to view these contents.</em>" But the web application lists the URIs just fine! Let's go to Access Policies tab, you will see that there is one policy created for a web application: </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":561} -->
<figure class="wp-block-image"><img src="/images/image-42.png" alt="" class="wp-image-561"/><figcaption>Access Policy for Web Application</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>This was created by the <a href="https://github.com/sanjaybhagia/webappsecretsdemo/blob/master/WebAppsWithoutSecrets/deployment/azuredeploy.json" target="_blank">ARM template</a> after web application was provisioned. This is the section in ARM Template which defines this policy for the web application</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code lang="json" class="language-json">"accessPolicies": [
          {
            "tenantId": "[reference(concat(resourceId('Microsoft.Web/sites', variables('webAppName')), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
            "objectId": "[reference(concat(resourceId('Microsoft.Web/sites', variables('webAppName')), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
            "permissions": {
              "keys": [ "all" ],
              "secrets": [ "all" ]
            }
          }
        ],
        "tenantId": null,
        "tenantId": "[reference(concat(resourceId('Microsoft.Web/sites', variables('webAppName')), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
        "sku": {
          "name": "Standard",
          "family": "A"
        }</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>Since at the moment, the only allowed access is for the web application you are not able to see any secrets when you go to <em>Secrets</em> tab. You can give access to your account if you want to see these secrets. <em>Remember to click Save button after adding the policy, otherwise settings will not be saved</em></p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":565} -->
<figure class="wp-block-image"><img src="/images/image-43.png" alt="" class="wp-image-565"/><figcaption>Adding Access Policy in Azure Key Vault</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>now, when I navigate the Secrets again, i'm able to see the Secrets. </p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":564} -->
<figure class="wp-block-image"><img src="/images/image-45.png" alt="" class="wp-image-564"/><figcaption>Secrets visible after adding access policy for the logged in user in Azure Portal</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Remember: <em>If you want to use secrets from the KeyVault while development, you will have to assign access policy for your user in Azure KeyVault. Otherwise, you will get access denied exception. </em></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>Conclusion</strong>: </p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>So as we saw, it's fairly straightforward to keep sensitive information in Azure KeyVault and configure our Web Application to read these secrets from the Vault. Moreover,  Managed Service Identity feature further facilitates us to keep the information about KeyVault out of our web application. This can be applied to any scenarios - this is not necessarily bound to the web application only. You can keep connection strings, keys, certificates and all sorts of information you don't want to keep in your consumer application.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>So, how do you handle your secrets? What is your opinion on this topic? I would love to hear more from you, please leave your comments. </p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Cheers</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong></strong></p>
<!-- /wp:paragraph -->