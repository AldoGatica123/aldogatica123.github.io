---
layout: post  
date: 2020-9-13  
categories: coding  
title: "Tecnobot for Messenger"  
---

## Business needs
This project was requested by a marketing company, they needed an automated way to gather the clients business information.
After gather the information, a new marketing campaign in Google AdWords has to be created. The business planned to have
a web page to display a form, but we agreed that a conversation interface was more dynamical, and the business already has
a Facebook page to host a chatbot.  

## Dialogflow attempt
When starting the chatbot project, Dialogflow was the first option for the NLU engine. I started creating a simple prototype
using the console's features (A simple agent with a single intent with multiple prompts, asking for different parameters).
Quickly I identified a limitation when using prompts: No validations can be done when capturing @any entities and if an 
entity is captured, no re-prompt can be triggered again. Currently, while writing this post, a new version has been released
as a beta and I personally haven't used it to test if those limitations have been fixed.  

<img src="https://user-images.githubusercontent.com/10179447/93028362-be201e00-f5d0-11ea-8f63-e29d43e862f3.png" width="600" height="90">

After assuring that an intent with prompts weren't going to be enough for capturing the information, I started an web
project for slot-filling in Firebase using the Dialogflow. I started it using the publicly available code for slot-filling
and then the same problem appeared in this implementation. At this point I wasn't going to reject the possibility of using
Dialogflow since it has a simple Facebook response integration. 

## Facebook Messenger 
I evaluated the possibility of making validations while capturing data from the conversation, but after a lot of thought 
I decided to remove the use of Dialogflow and use the Facebook Messenger SDK and code the data extraction from the ground up.
The Facebook Developers page provides some bot samples where we can test the full functionality of a Messenger bot.
I used [this](https://www.messenger.com/t/OriginalCoastClothing) chatbot as a sample, here is the full
[documentation](https://developers.facebook.com/docs/messenger-platform/getting-started/sample-apps) of the sample projects. 
This project is available [here](https://github.com/AldoGatica123/tecnobot_messenger)  


The conversation flow needed to change for this kind of implementation, since there is not a way to persist the conversation
information in the Lambda execution, I decided to persist the conversation in a DynamoDB table using each property as a 
field to be filled on each message. This way the conversation is stored and if the user resumes the creation of the campaign
, the chat will re-prompt which are the missing fields.  

### Messenger Page Properties
To customize the Messenger chat, Facebook provides some features that can be added. I have added a persistent menu, a greeting 
and ice-breakers to the start page of the chat.  
<img src="https://user-images.githubusercontent.com/10179447/93028401-02132300-f5d1-11ea-8428-1e7a0a85959b.png?raw=true" width="250" height="350">

### Amplify Backend Deployments
For the deployment of the Messenger application I used Amplify because of the convenience of automating the creation of 
resources. The resources used for the project were a Lambda function, an API Gateway endpoint and a DynamoDB table.  

<img src="https://user-images.githubusercontent.com/10179447/93031028-df8b0500-f5e4-11ea-9f1b-327ea561c0af.png?raw=true" width="350" height="100">

A very useful resource for starting this project was [this](https://youtu.be/aJlvJsdVTLU) video.  

## Tecnobot

The second part of the project was developed by another person, his blog is [this](http://blog.dromero.xyz/).
The purpose of this automation is to validate payments made in Págalo platform and create the marketing campaigns in Google 
Adwords. 

This is the high level architecture of the automation and the code is [here](https://github.com/AldoGatica123/tecnobot).

<img src="https://github.com/davidromero/tecnobot/blob/master/docs/tecnobot.png?raw=true" width="558" height="507"> 



