---
layout: post
date: 2020-03-27
categories: coding
title: "Chatbots with DialogFlow"
---

# Chatbots with DialogFlow

## Chatbots using DialogFlow and GCP
This post consists in multiple chatbot projects using [DialogFlow][dialogflow-link]. I built them in for about 4 months, 
it was refreshing going back to development since I haven't made anything like that in my job.  
I will show how I built the latest chatbot, it integrates what I have learned along with the development of the previous projects.

The user interfaces used in the projects were different, I used [Microsoft Bot Framework][mbf-link] and an Angular 
[template][tiledesk-link] for the rest, also the components were built in [Google Cloud Platform][gcp-link], 
so I will only explaining what I made using the template to maintain simplicity.

Besides of omitting the C# code there is little to explain when making chatbots in [Azure][azure-link], just look for 
Web App Bots and go along the proposed deployment methods they provide.

## Architecture

![IA GT Architecture](https://user-images.githubusercontent.com/10179447/78912017-58899d80-7a44-11ea-8b6b-22e1019006a4.jpg)

The solution consists in an assistant chatbot for RPA related questions.
It answers frequently asked questions saved in the webhook and queries data from Datastore.  
The Datastore contains info extracted from csv files in Cloud Storage which is triggered by a scheduler.

## Deployments

I hosted the frontend and the webhook in Google Cloud [App Engine][appengine-link] so everything is in one single cloud. 
Something important is that to use App Engine you have to enable billing for the account and link it to the project before deploying it.  
The deployments for the projects didn't follow a CI/CD pipeline, they were deployed used the gcloud CLI.

### Working with DialogFlow

Here are some tips I learned that can be summarized:
- Group similar static responses in a group and trigger them in one single intent.
- Don't route the conversation using followup intents, use events instead.
- Use custom labels within the response to display something different in the frontend.
- When testing fulfillments, use ngrok to serve the localhost endpoint to DialogFlow.
- Use action names to identify them in the fulfillments.
- Add a timeout message as a default message when the intent has to fulfill an action.
- Assign numbers to the intents for easy identification.

### Webhook Design

When making my first chatbots I decided to use [Google Cloud Funtions][functions-link], later that turned out to be a 
bad idea. The cold start of the functions surpassed the 5 second timeout that dialogflow gives to the fulfillments. 
So the first try of querying data gave a timeout.  
Later that was fixed when I called a wake up function when the welcome intent is triggered, the fulfillment didn't expect an 
answer back from the function, it was used only to give a head start for the other actions to answer quickly in the future.  
But after that I realized that if the user waits too much the function goes cold again, so I moved the code to the webhook.  
So the important part is __respond to dialogflow in less than 5 seconds__, if something else has to be done don't make 
Dialogflow wait for an answer.

### Architecture Evolution

I started making a Microsoft Bot Framework chatbot, its purpose was to respond over a web chat 
then I started experimenting and checking out different sources to see what could be improved.  
Initially what I made was a simple bot that looked like this:

#### Sharepoint and Teams Chatbot

The users interact with the bot in [Microsoft Teams][teams-link] and throught a Sharepoint page, the bot responds to 
frequently asked questions. After responding to the question, the bot creates tickets in a Jira system by google cloud functions. 

![first_architecture](https://user-images.githubusercontent.com/10179447/78833326-ea909800-79a9-11ea-8567-707e14e8f1f8.jpg)

#### App Engine Chatbot

For this bot I only had to respond to FAQs again, so I did a much simpler design, and I grouped similar intents to a 
single one and added fulfillments with a flask application and cloud functions to respond them identifying entities. 
The problem with this architecture was that the responds took too long to respond caused by the cold start.

![second-architecture](https://user-images.githubusercontent.com/10179447/78836397-372aa200-79af-11ea-8341-e58f6944da88.jpg)

#### IA GT Chatbot

For the last project architecture, its purpose is to respond over user queries in a data base. The webhook replaces the
static responses and also communicates to the Datastore database. There is a google function that converts the rows in
a csv file and adds it to the database. The function is triggered by a scheduler.  

![last-architecture](https://user-images.githubusercontent.com/10179447/78912017-58899d80-7a44-11ea-8b6b-22e1019006a4.jpg)


[dialogflow-link]:  https://dialogflow.cloud.google.com/   
[teams-link]: https://products.office.com/en-us/microsoft-teams/group-chat-software  
[mbf-link]: https://dev.botframework.com/  
[tiledesk-link]: https://github.com/Tiledesk/dialogflowproxy  
[azure-link]: https://azure.microsoft.com/es-es/  
[gcp-link]: https://cloud.google.com/  
[appengine-link]: https://cloud.google.com/appengine  
[functions-link]: https://cloud.google.com/functions  
