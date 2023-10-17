---
Title: 'Pulumi - What and Why?'
date: 2020-09-10
published: 2020-09-10
author: sanjay.bhagia@gmail.com (Sanjay Bhagia)
layout: post
categories:
  - Blog
tags:
  - devops
  - pulumi
  - ci/cd
---

> I am learning [Pulumi](https://www.pulumi.com/) and I'll be sharing my journey along the way here in the form of blog posts and code samples on my [GitHub](https://github.com/sanjaybhagia/pulumi-examples). Join me in this journey if this topic interest you. 

> Disclaimer: I have hands-on experience working with Microsoft Azure Cloud platform not so much with others. So in here I'll be looking at Pulumi from Azure's lens and most of my references and examples will be with Azure. 

## Context
[Infrastructure as Code (IaC)](https://en.wikipedia.org/wiki/Infrastructure_as_code) is the process of managing and provisioning computer data centres through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools. It's the practice of treating your infrastructure like you would treat your code. Meaning, you can check in the definition files in a source control system, review it, version it and it produces the same environment every time it is used. 

Infrastructure provisioning predates cloud. We used to provision our on-premises infrastructure before cloud became the thing and there are plenty of tools for that. With cloud comes the opportunity and challenge of managing large scale distributed systems, service based architectures and cloud native applications. If you are building apps on Cloud, you need to have automation and have the ability to quickly span up and tear down the environment in a short amount of time and with confidence. This is where the whole infrastructure as code approach shines. This gives you the superpowers to run any workload in cloud whilst taking advantage of core cloud tenants like speed, elasticity, cost-effectiveness.

Almost every cloud provider has a way to provision infrastructure. For this, they have their own [domain specific language (DSL)](https://en.wikipedia.org/wiki/Domain-specific_language) and often written in [JSON](https://en.wikipedia.org/wiki/YAML) or [YAML](https://en.wikipedia.org/wiki/YAML) format - called templates. For Azure, its [ARM (Azure Resource Manager)](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/overview), for Amazon its [CloudFormation](https://aws.amazon.com/cloudformation/), Google its [Cloud Development Manager](https://cloud.google.com/deployment-manager/docs) etc. Now they are very powerful in a sense that you can *declaratively* provision your complex cloud resources without knowing the details of the underlying workings - the cloud platform takes in your JSON/YAML files and do the magic for you. This has helped the entire Industry a lot in adaption of cloud platforms, irrespective of which platform you are operating on. However, there are certain challenges associated with this.  
  
With declarative approach:
1. You need to know and learn the DSL for each and every cloud platform you are working with or you need to type in YAML files (I know right !), 
2. You rely on the (not so great) tooling around it
3. Templates get very difficult to manage and go out of control very quickly
4. Lack of local development loop. You have to actually call-in cloud APIs (actually deploy them) to be sure the templates you have authored are correct and will work.

Now take a notch up, let's say you are going to work across cloud platforms, i.e., *n > 1*. We can discuss whether it's a good idea or not but it's becoming a reality. Even if your application doesn't work in multi-cloud scenario but you are most likely using other SaaS applications or services for example, using Cloudflare for managing CDN for your web application running in Azure. This adds further challenges to the list previously mentioned:  
  
5. You need to write infrastructure in different language for *n* number of cloud platforms you are working with  
6. Each has its own set of associated tools you might need to deal with  
7. Most likely, your infrastructure code will be placed in different locations not in a single coherent place *(not necessarily but likely)*  
etc. etc.

*...here enters [Terraform](https://www.hashicorp.com/products/terraform/) !*

Terraform solved this challenge of dealing with multi-cloud scenario. It's a very popular tool in IaC space today and I would say almost becoming the *de facto* standard when it comes to provisioning infrastructure in cloud these days. It's [free](https://www.terraform.io) and [open source](https://github.com/hashicorp/terraform). However, Terraform introduces another DSL which is called HCL (HashiCorp Configuration Language). It is a declarative language where you define the resources you would like to provision in a cloud platform. It works cross platforms and you need to know only HCL to work against any platform of your choice. There is a great ecosystem and community around Terraform which is great. Even though Terraform is fantastic but now you need to understand yet another language. 

*...now we are entering the realm of [Pulumi](https://www.pulumi.com/)*

Pulumi takes a step further and expands on this problem and provides us a way to really use *any* proper programming language of our choice and write the infrastructure provisioning code like we write any other code as developers and operations people. 

## What is Pulumi?
What Pulumi brings on the table is you can use the programming language of your choice to write the infrastructure for any cloud - **"Any Code, Any Cloud"**  
Pulumi is a Modern Infrastructure as Code with which you can create, deploy and manage infrastructure on any cloud using familiar programming languages and tools.  

> As of this writing, Pulumi supports these programming languages: Go, Python, JavaScript, TypeScript, C#, F# and Visual Basic (.NET Core)

It's also [free](https://www.pulumi.com/pricing/#community-edition) and [open source](https://github.com/pulumi).

## Why Pulumi?
By leveraging familiar programming languages for infrastructure as code, Pulumi makes you more productive, and enables sharing and reuse of common patterns. A single delivery workflow across any cloud helps developers and operators work better together.

Some reasons I think Pulumi is great:  
1. **Programming model** - You can write your infrastructure like you write your code in a programming language and tools of your choice 
  This is **HUGE**. Having the flexibility to provision your infrastructure with your preferred programming language is a big productivity boost. On top of that you can bring in the same software development practices and principles like composition, abstraction, testability etc. for your infrastructure and use the favourite tools you are accustomed to, such as IDEs etc. 
2. **Familiar tools**  
    IDEs, refactoring, testing tools, package management etc.
    Improves productivity from the get go
3. **Configuration Management**
4. **Testability**  
  With the power of programming language you can actually test your infrastructure like you would for your code. No one is stopping you to bring back the [TDD from dead](https://dhh.dk/2014/tdd-is-dead-long-live-testing.html), if you will ;) 
5. **Faster feedback loop**
  With programming language, testability and ability to preview the plans before you actually update/apply your changes comes very handy while writing the infrastructure code. 
2. **Multi cloud support**  
  Pulumi allows you to provision resources in multiple clouds and SaaS services. Even though you will be writing separate code for targeting to different clouds (providers) but you can have all of them in a single solution/project at one place. On top of that, you can have your entire end-to-end across clouds and SaaS infrastructure in a single place that you can check in your source code repository in GitHub. 
4. **Governance**  
  Besides providing infrastructure as code capabilities, Pulumi has introduced the support for Policy as code. You can write the policies for governance (which could be managed separately by security team, independent of the DevOps team) that can be executed while running Pulumi programs. It's not tied to cloud vendors (like Azure Policies), rather they are Pulumi specific but that can be run across clouds to enable same set of policies in multi-cloud scenarios.  
  Pulumi provides:
    - Policy as code  
    - Secret Management  
    - Enforce standards  
5. **Adapting existing infrastructure**
  You can bring in your terraform files to Pulumi (they provide a tool for this) or you can adapt existing environment from your already deployed resources in the cloud
6. **State management**  
  Besides providing the flexibility to completely manage the state ourselves (just like Terraform), Pulumi offers a free app.pulumi.com service that takes control of state management concerns for you.
7. **Validating your code before you actually provision the resources**  
  The ability to preview the changes you are making before you actually deploy the code helps in the faster feedback loop when you are writing the code. 

## Other tools in similar space
While looking at Pulumi I came across some other tools in this space that are doing similar things but there are still quite a few differences. I think Pulumi is far ahead of them - at least as of now.
- [Farmer](https://compositionalit.github.io/farmer/)  
  *this lets you write the code in F# and generates ARM templates at the end*
- [CDK for Terraform](https://www.hashicorp.com/blog/cdk-for-terraform-enabling-python-and-typescript-support/)

## What do I think about this
I am coming from a development background, so don't consider myself an Ops guy per se but let's be real. If you are working in cloud, the boundaries are getting blurred faster than we realise. If you are developing, the chances are you will be deploying the applications yourself. And having the mindset of operability of your applications you develop pushes you to build great software. It's a great learning experience in and of itself.

I think this space is going to get a lot of traction and will be adapted widely as we can already see the competitors getting into this space (referring to CDK). Even though it seems this is targeted more toward developers than operations people but the platform already support quite popular languages like Python and Go that are widely used in Ops world. Moreover, for Microsoft workloads, since we already have the support for .NET it's perhaps just a matter of time before PowerShell support is added. Another reason I see is the level of confidence this provides with the whole testability story that Pulumi enables. It allows us the flexibility to try out the infrastructure with confidence right from our local development machines and that only leads to the robust infrastructure. I'm excited about this if you ask me !  

## Wrapping up
Alright, this was a bit of an introduction to what Pulumi is and why it makes sense to me. As I'm learning more about Pulumi, I'll be sharing my learnings along the way. Thanks for sticking around. 

Let me know if you have fiddled with Pulumi yet and how do you find it? 

Until next time  
Ciao









