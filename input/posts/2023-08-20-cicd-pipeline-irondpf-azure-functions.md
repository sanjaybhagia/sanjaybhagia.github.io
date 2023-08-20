---
Title:  'Deploying IronPdf to Azure Functions via CI/CD using Azure DevOps'
Description: Guide to setup a CI/CD Pipeline (YAML) in Azure DevOps for running IronPdf in Azure Functions.
date: 2023-08-20
published: 2023-08-20
author: Sanjay Bhagia
layout: post
categories:
  - Blog
tags:
  - ironpdf
  - ci/cd
  - azuredevops
  - azure
  - azurefunctions
  - serverless
  - devops
  - github actions
---
> You can find the source code for the sample I am using in this blog post on my [GitHub](https://github.com/sanjaybhagia/testironpdf). This contains the code for Azure Function along-with YML pipeline that I'm going to discuss here. 

# Brief Introduction to IronPdf
IronPDF is the leading C# PDF library for generating & editing PDFs. Its user friendly API allows developers to rapidly deliver professional, high quality PDFs from HTML in .NET projects. Some of the key feature are: 
- Convert web forms, local HTML pages, and other web pages to PDF with .NET
- Allow users to download documents, send them by email, or store them in the cloud.
- Produce invoices, quotes, reports, contracts, and other documents.
- Work with ASP.NET, ASP.NET Core, web forms, MVC, Web APIs on .NET Framework, and .NET Core.
  
You can read more about it on their official [website](http://ironpdf.com)

# Setting the context

In this blogpost, I'm going to focus on deploying your code that uses IronPdf to an Azure Function. I'll be using Azure Function on Windows, but you can find the guidance on Linux on their website as well. 

For the purpose of demonstration, I've created a simple HTTP Triggered Azure Function that receives HTML string in request and returns the PDF File. I assume, the function app is already created.  
Here is how the generated PDF looks like. 
![Generated Pdf](/images/pdf-generated-by-ironpdf.png)


## Deploying to Azure Function via Azure DevOps
Now we have a functioning code, let's deploy this to Azure via CI/CD pipeline using Azure DevOps

The CI/CD pipeline is similar to any azure function pipeline, there is no major difference except choosing the right deployment method. 

## Choosing the Deployment method.
In Azure DevOps, for the `AzureFunctionApp@1` task, there are the following supported methods. 
- auto
- zipDeploy
- runFromPackage

`auto` is the default method, when you add a task. But for IronPdf, `zipDeploy` is the recommended mode

## YML Snippet
Here is the yml snippet for this step: 

```yml
steps:
- task: AzureFunctionApp@1
  displayName: Azure functions app deploy
  inputs:
    azureSubscription: <subscription name>
    appType: functionApp
    appName: ironpdf-azurefunc-app 
    package: $(Pipeline.Workspace)/azurefunction/a.zip
    deploymentMethod: zipDeploy
    appSettings: >
      -FUNCTIONS_EXTENSION_VERSION "~4"
```
The only different is setting the `deploymentMethod` to `zipDeploy` instead of default `auto`.

This will deploy the code from your solution in Azure Function. 

You can find the complete CI/CD pipeline in my GitHub repository [here](https://github.com/sanjaybhagia/testironpdf/blob/main/ironpdf-azurefunc-pipeline.yml). You will need to adjust the settings to make it work for your environment such as subscription name etc. 

# Bonus: GitHub Action
If you prefer using GitHub, I have also included the action for your in my repository. You can find it [here](https://github.com/sanjaybhagia/testironpdf/blob/main/.github/workflows/azure-functions-app-dotnet.yml)

Hope it helps. 
Cheers
