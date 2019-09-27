---
id: 225
title: Creating loosely coupled and generic process steps implementation
date: 2013-05-02T19:42:31+02:00
author: sanjaybhagia
layout: post
guid: http://sanjaybhagia.wordpress.com/?p=225
permalink: /2013/05/02/creating-loosely-coupled-and-generic-process-steps-implementation/
layers:
  - 'a:1:{s:9:"video-url";s:0:"";}'
dsq_thread_id:
  - "2863786209"
categories:
  - 'C#'
  - SharePoint
  - Uncategorized
tags:
  - flexible
  - Inheritence
  - Interface
  - Loosely couple
  - Process
  - ProcessSteps
  - Queue
  - Rollback
  - Site Collection
  - Stack
  - Workflow
---
<p style="text-align:justify;">Recently, during one of the SharePoint implementations we came across the requirement to build the workflow for site provisioning engine that we are developing.</p>
<p style="text-align:justify;">One of the requirements of the process is to rollback everything that we create/modify if there is any problem/exception during the process. So for example, if we created the site collection and then in the next step process failed at setting up custom permissions, the process should be roll backed to the initial stage.</p>
<p style="text-align:justify;">Normally, this is no big deal to implement everything as a standard workflow and you write a piece of code for different activities of the workflow (or use standard workflow activities from Visual Studio toolbox) but then an idea came up why not write an implementation of the process steps independent of the workflow in a nice and loosely couple way that should take care of roll back in a much flexible and nicer and cleaner way !</p>
<p style="text-align:justify;">So in this post, I will try to describe the solution that I came up with and also implementation details.</p>
<p style="text-align:justify;">So here is the implementation details:</p>
<p style="text-align:justify;">First, I created an interface that contains the method declarations for Execute and RollBack methods</p>

<pre><code class="csharp">
public interface IProcessStep
{
   void Execute(ProcessStepProperties properties);

   void RollBack(ProcessStepProperties properties);
}

</code></pre>
<p style="text-align:justify;">As you see in the code above, I'm using parameter of type ProcessStepProperties. This is basically a custom class that I use as a data transfer object (DTO) across different steps and methods. It's basically a collection (dictionary in my implementation) that contain key/value pairs. Here is the implementation:</p>

<pre><code class="csharp">
public class ProcessStepProperties
{
   public Dictionary&lt;String, Object&gt; Keys = new Dictionary&lt;String, Object&gt;();

   public bool KeyExists(String key)
   {
     if (Keys.ContainsKey(key) &amp;&amp; Keys[key] != null)
        return true;
     return false;
   }
}
</code></pre>
<p style="text-align:justify;">Having interface ready, we write a class that defines one the process steps. This step is CreateSiteStep that creates the site collection. This class inherits from our interface IProcessStep and hence it provides the implementation of Execute and RollBack methods.</p>

<pre><code class="csharp">
public class CreateSiteStep : IProcessStep
{
   //private method to check for preconditions for Execute method
   private bool PrequisitesPresent(ProcessStepProperties properties)
   {
      if (properties.KeyExists(&quot;SiteNumber&quot;) &amp;&amp; properties.KeyExists(&quot;Title&quot;) &amp;&amp; properties.KeyExists(&quot;TemplateName&quot;))
         return true;
      return false;
   }

   public void Execute(ProcessStepProperties properties)
   {
      //Check if preconditions are met
      if (PrequisitesPresent(properties))
      {
         //Create site collection
         CreateSiteFromRequest(properties);

         //Update the property bag
         var spSite = properties.Keys[&quot;SPSite&quot;] as SPSite;
         if (spSite != null)
         {
            properties.Keys[&quot;SiteUrl&quot;] = spSite.Url;
         }
      }

      //Write checkpoint for rollback
      TransactionLogger.Log(this);
   }

   public void RollBack(ProcessStepProperties properties)
   {
        // Add Logging information  - ULS or event logger if appropriate
        SPSite site = properties.Keys[&quot;SPSite&quot;] as SPSite;
        DeleteSite(site);
   }
</code></pre>
<p style="text-align:justify;">Now there are couple of things that need to be discussed in the above code.</p>
<p style="text-align:justify;">Firstly, as you see, I have created a private method to check pre-conditions before the Execute method is executed. This is totally upto the specific step to check if it requires any pre-conditions to be met or any values it requires before it proceeds.</p>
<p style="text-align:justify;">Secondly, as i mentioned above I'm using ProcessProperties class (key/value collection) to transfer the state across methods and steps, I'm updating (adding) the value in the collection right after Site collection has been created. In this way, the next step (or rollback) step will have the updated information that might be useful for further execution of the process.</p>
<p style="text-align:justify;">Thirdly and quite importantly there is a call to Log method of  TransactionLogger class.
<strong>TransactionLogger.Log(this);</strong></p>
<p style="text-align:justify;">This is very important. Again, TransactionLogger is my custom class that I'm using to log in the information for steps that have successfully been completed. This serves the purpose of check point for me that i use to roll back when anything fails. I'm using Stack as a data structure to push the steps (of type <em>IProcessStep</em>) whenever any step is successfully executed and if there is any failure during the execution, i simply pop the steps and invoke RollBack method until stack is empty - hence everything is cleaned up in case of failover.</p>
<p style="text-align:justify;">Here is the implementation of TransactionLogger class.</p>

<pre><code class="csharp">
public class TransactionLogger
{
  //to keep checkpoints
  private static readonly Stack TransactionStack = new Stack();

  //public property to check number of steps in the stack
  public static int LogCount
  {
      get { return TransactionStack.Count; }
  }

  private IProcessStep Pull()
  {
     return TransactionStack.Pop();
  }

  //Pushes the process step into the stack
  public static void Log(IProcessStep step)
  {
      TransactionStack.Push(step);
  }

  //Pops the step from stack and invoke rollback method for every step  until stack is empty
  public static void RollBack(ProcessStepProperties properties)
  {
      while (TransactionStack.Count &gt; 0)
      {
          var step = TransactionStack.Pop();
          step.RollBack(properties);
      }
   }
}
</code></pre>
<p style="text-align:justify;">So, basically the idea is that in case of any failure everything is rolled back and in the implementation above, TransactionLogger class doesn't need to know the details about any ProcessStep class how to roll back because it is upto every step to roll back the things it has created. That is why TransactionLogger class adds IProcessStep object into the stack and that is our interface!
here is another class called SetSitePermissionGroupStep that basically adds custom permissions to the site collection that has been created in previous process step.</p>

<pre><code class="chsarp">
public class SetSitePermissionGroupStep : IProcessStep
{

   // .... code removed for clarity purpose ....

 public override void Execute(ProcessStepProperties properties)
 {
    if (PrequisitesPresent(properties))
    {
       AddAssociatedMemberGroups(properties, _key);
       TransactionLogger.Log(this);
    }
 }

 public override void RollBack(ProcessStepProperties properties)
 {
   //Roll back implementation
 }
}
</code></pre>
<p style="text-align:justify;">So, as a recap, we defined an interface called IProcessStep that contains the declaration of Execute and RollBack methods.</p>
<p style="text-align:justify;">Other classes (specific to process steps) inherit from IProcessStep and provide an implementation for Execute and RollBack methods. Execute method of every process step class also add check point after successful execution of the step using TransactionLogger.Log method.</p>
<p style="text-align:justify;">Now having all this setup we need some sort of controller class that could use this implementation. This could be used from within the workflow as well but in my demonstration I'm using a class that implements the Queue and in that queue we can insert separate steps and then Process the queue in the end. Here is the implementation of my controller class.</p>

<pre><code class="chsarp">
public class TestProcessSteps
{
    private Queue _queue = new Queue();
    private ProcessStepProperties _properties = new ProcessStepProperties();

    public void Initialize(ProcessStepProperties properties)
    {
       _properties = properties;
    }
    public void Enqueue(IProcessStep step)
    {
       this._queue.Enqueue(step);
    }

    public void ProcessQueue()
    {
      try
      {
         while (_queue.Count &gt; 0)
         {
            //pick item from the queue and invoke execute method
            var step = _queue.Dequeue();
            step.Execute(_properties);
         }
       }
       catch (Exception ex)
       {
          //roll back
          TransactionLogger.RollBack(_properties);
       }
    }
 }
</code></pre>
<p style="text-align:justify;">If you notice above, if anything fails (i.e, exception occurs), RollBack method is invoked from TransactionLogger class (that basically pops up items from the stack and invoke rollback method on respective step).</p>
<p style="text-align:justify;">Finally, here is how we test our implementation.</p>

<pre><code class="chsarp">
//Instantiate TestProcessStep class
TestProcessSteps testclient = new TestProcessSteps();

//setup properties
properties = InitializeProcessProperties(item);

//Invoke Initialize method on TestProcessSteps class (that basically initializes ProcessProperties)
testclient.Initialize(properties);

//Insert steps into Queue
testclient.Enqueue(new CreateSiteStep());
testclient.Enqueue(new SetSitePermissionGroupStep(&quot;MemberGroup&quot;));
testclient.Enqueue(new SetSitePermissionGroupStep(&quot;OwnerGroup&quot;));

//Process the Queue (that basically takes items from queue and invoke Execute methods on respective steps)
testclient.ProcessQueue();

</code></pre>
<p style="text-align:justify;">Note that during the enqueue, I'm adding SetSitePermissionGroupStep step two times with different parameters - in this way you can actually define atomic steps also using the same class.</p>
<p style="text-align:justify;">Hope it gives the general idea about my thought behind the implementation. Don't hesitate to provide any feedback or issues if you find any in my solution.</p>
<p style="text-align:justify;">Thanks for taking time to read this long post :)</p>