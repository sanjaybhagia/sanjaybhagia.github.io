---
Title: 'Capture your favourite quotes from physical books'
date: 2020-08-24
author: sanjay.bhagia@gmail.com (Sanjay Bhagia)
layout: post
categories:
  - Blog
tags:
  - azure
  - serverless
  - computer vision
  - azure cognitive services
---

This is a two part series blogpost:<br/>
- [Part 1: Setting up the logic app to retrieve text from an image (this)](/2020/08/24/capture-quotes-from-books)
- [Part 2: Pushing the detected text to a text file in blob storage](/2020/08/25/capture-quotes-from-books-2)

I like reading books. Though I love technology but certain books are best suited for physical form and I'm quite open about it - whatever works. One of the good things about digital books is they make it quite trivial to highlight or take notes of our favourite quotes or sections. When it comes to physical books, there are all kind of (manual) techniques you can use to take notes. While underlying few quotes in a book I was reading the other day, I thought wouldn't it be nice if I take a picture and the quote is written to a text file somewhere as a collection for me which I can refer to later on? Of course, I didn't just put down the book right away since I was enjoying it so I carried on - I know not so easy to do. 

Once done with the reading, I set out to create a solution for this. I work with Microsoft Azure in my day to day job so it was an obvious choice for me. I went about picking up the services that could help me easily put different pieces together to solve the challenge at hand. 

This is what I came up with. 

![architecture diagram](/images/capturequotes.drawio.png 'Archietcture Diagram')

**So the flow is:** I will take a picture of any quote with my phone, upload it to a certain folder in my OneDrive. The Logic app gets triggered if there is a new file in that folder, reads the contents of the file, passes that image to the Cognitive API (OCR to Text) that returns the detected text, we then call an azure function that appends the quote to a text file in my blob storage.

**Rationale**
- OneDrive (Personal): I already use it, it's my private drive, have secured access from anywhere and from my device
- Logic Apps: Helps me orchestrate the flow without writing much code and I can use built-in connectors to access OneDrive, Cognitive services, blob storage etc. 
- [Computer Vision API](https://docs.microsoft.com/en-au/azure/cognitive-services/computer-vision/): Makes it almost trivial to detect text from the images and works quite well with Logic Apps
- Azure Functions: Though I shouldn't have used this but this is to append the text in blob storage since there is no built-in connector for this currently. 
- Blob Storage: I could potentially store this file anywhere else but storage account is quite handy in this scenario (supports append blobs natively). 

With the high level architecture in place, let's dig into the implementation. 

## Create Logic App
- Head over to the Azure Portal and create a logic app.<br/>
![CreateLogicApp](/images/translate-quotes-create-logicapp.png)<br/>
- Once the resource is created, go to that resource. 
- Select 'Blank Logic App' from templates
- Search for 'OneDrive' connector and select 'When a file is created' from Triggers<br/>
![FileCreated](/images/translate-quotes-onedrive-filecreated.png)<br/>
- Sign in to your OneDrive account and select the folder where you will be placing your images from mobile.
![OneDriveSignIn](/images/translate-quotes-onedrive-signin.png)
- Set the frequency at which you want OneDrive connector to look for new files (during development, select smaller window and when you are ready to use it, increase it so you don't end-up polling OneDrive too often)
![OneDriveSetFrequency](/images/translate-quotes-onedrive-setfrequency.png)
- Click 'New Step' to add the next action. 
- Search for 'Cognitive' and select 'Computer Vision API' and then search for 'ocr'. 
- Select 'Optical Character Recognition (OCR) to Text' from the list<br/>
  ![ComputerVisionOcr](/images/translate-quotes-computervision-ocr-text.png)
- To fill this information we need a Computer vision resource.

## Create Computer Vision resource
- Open another tab/window and go to Azure Portal (portal.azure.com)
- Create a new resource of type 'Computer Vision'. Select 'F0' pricing tier (it's free - though you can have only one free resource in a subscription)
![ComputerVisionCreateResource](/images/translate-quotes-computervision-create.png)
- Once the resource is created, navigate to that resource (by selecting 'Go to resource' or search for it from the search bar at the top)
- Head over to 'Keys and Endpoint' under 'Resource  Management' section
- Copy 'Key1' and 'Endpoint' (in notepad or anywhere else you prefer)
![ComputerVisionKeys](/images/translate-quotes-computervision-keys.png)

## Update Logic App with Computer Vision Action
- Head back to the Logic App now
- Enter connection name 'computervisionconnection'
- Enter value of 'Key1' from computer vision API in 'Account Key' field
- Set 'Site Url' to the 'Endpoint' you coped from computer vision
- Click 'Create' button to create the connection<br/>
  ![ComputerVisionConnection](/images/translate-quotes-computervision-connection.png)
- For 'Image Source' - select 'Image Content' from the dropdown.
- For 'Image Content' - set it to File Content from previous step (by selecting the dynamic content. Put a cursor in - Image Content field and a pop up will show up, select the value from there) <br/>
  ![ComputerVisionActionSetContent](/images/translate-quotes-computervision-action-setcontent.png)<br/>
  ![ComputerVisionAction](/images/translate-quotes-computervision-action.png)
- This is how the logic app looks so far <br/>
  ![LogicAppStageI](/images/translate-quotes-logicapp-stagei.png)
- At this point, we have a logic app that will be triggered if there is a new file in OneDrive under '/ocr-images' folder and the text is translated using Computer Vision API. It's time to test
## Test the app
- Save the app. Upload a picture of some text in your folder in OneDrive. Check the run history of the logic app and select the last run.
- I have uploaded this picture
  ![TestPicture1](/images/translate-quotes-test-pic-1.jpg)
- Here is how it looks for me. 
  ![LogicAppRunHistory](/images/translate-quotes-logicapp-runhistory.png)
  ![LogicAppRunHistory](/images/translate-quotes-logicapp-stagei-success.png)

Check the text in 'Body' and you will see that it matches the text we had in the image. That's pretty rad, isn't it?

We are pretty much done here. But I would like to take this further. I would like the text to be stored in a text file that sits in my blob storage. I want the text to be appended every time the logic app runs so I have my quotes in one file on new line. This will be covered in part II of the series, so stay tuned. 


Cheers