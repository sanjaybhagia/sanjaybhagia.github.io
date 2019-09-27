---
id: 122
title: Adding local variables on a page as a user control
date: 2012-12-20T19:49:30+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=122
permalink: /2012/12/20/adding-local-variables-on-a-page-as-a-user-control/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
dsq_thread_id:
  - "3072739120"
disqus_identifier: https://sanjaybhagia.com/2012/12/adding-local-variables-on-a-page-as-a-user-control/
categories:
  - JavaScript
  - SharePoint
tags:
  - Delegate Controls
  - JavaScript
  - MasterPage
  - SharPoint
  - User Control
---
<p style="text-align:justify;">Quite often you need to use both server side and client side approach to build SharePoint solution as both offer different level of flexibility and functionality. One of the benefits of using mixed approach is that you can utilize the strength of both approaches and solve the problems quite quickly and efficiently.</p>
<p style="text-align:justify;">In this post I'm going to present a part of the solution where we had to cache one control during one of the recent projects.</p>
<p style="text-align:justify;">It is a very common practice that we write separate .js file for our client side scripting and than we can add these file either on a page or on a master page that we can use later on in our controls underneath. This works totally fine but the only thing we need to be careful about external files is that we need to wait for them to load before we can use anything inside these files. That is also fine but in some situations but there might be a little delay until file is loaded and that delay might be of quite a significance.</p>
<p style="text-align:justify;">To overcome this issue, we (Me and <a href="http://maghansson.blogspot.com/">Magnus Hansson</a>, who is the mastermind of this solution) recently came up with an approach where we actually created a user control and in that user control, we added some script to set some local variables that we needed to use in on our page.</p>
<p style="text-align:justify;">Here is the markup of the user control (ascx control):</p>
<p style="text-align:justify;">Markup:</p>

<pre><code class="js">
&lt;script type=&quot;text/javascript&quot;&gt;
var Local = window.Local || {};
   Local.Variables = function () {
     return {
        currentRootWebUrl : '&lt;asp:Literal runat=&quot;server&quot; id=&quot;litRootWebUrl&quot; /&gt;',
        currentWebUrl : '&lt;asp:Literal runat=&quot;server&quot; id=&quot;litCurrentWebUrl&quot; /&gt;',
        currentUser : '&lt;asp:Literal runat=&quot;server&quot; id=&quot;litCurrentUser&quot; /&gt;',
        currentLCID : '&lt;asp:Literal runat=&quot;server&quot; id=&quot;litCurrentLCID&quot; /&gt;'
       }
} ();
&lt;/script&gt;
</code></pre>

and in the CodeBehind(.cs) we set these asp controls embedded in script above:

<pre><code class="csharp">
protected override void CreateChildControls()
{
      //Get current user's Web object and Root Web Object
      SPWeb currentWeb = SPContext.Current.Web;
      SPWeb currentRootWeb = currentWeb.Site.RootWeb;

      //Get Locale and user's login name
      litCurrentLCID.Text = SPContext.Current.Web.CurrencyLocaleID.ToString();
      litCurrentUser.Text = SPContext.Current.Web.CurrentUser.LoginName;
      litCurrentWebUrl.Text = currentWeb.Url;
      litRootWebUrl.Text = currentRootWeb.Url;

      base.CreateChildControls();
}
</code></pre>

So what we have achieved so far is basically set of variables that are set from code behind in control. When executed, the output would be a JavaScript object named "Local" that has a public property called "Variables" that contains some more properties.

Now in order to see this, we need to add this control either on application page or master page (or any where you find it appropriate in your case).

We added in on Master Page as we needed to used this on a control inside master page. In order to add it on a master page, you can directly Register your user control on master page and then added reference to the control in header section or even create a delegate control to attach it with master page through feature.

Once, control is added, navigate to your site and see the browser output. You should find this section rendered in the view source:

<pre><code class="js">
var Local = window.Local || {};

Local.Variables = function () {

return {
    currentRootWebUrl : 'http://demo.local.com',
    currentWebUrl : 'http://demo.local.com/site/hr',
    currentUser : 'domain\\user1',
    currentLCID : '1033'
  }

} ();

</code></pre>

Now as soon as DOM is loaded, you have your local variables present that you can use right away in your control further down in the DOM (or controls etc) without waiting for any other JavaScript file to load like following:

<pre><code class="js">
    var currentRootWebUrl = Local.Variables.currentRootWebUrl;
    var currentWebUrl = Local.Variables.currentWebUrl;
</code></pre>