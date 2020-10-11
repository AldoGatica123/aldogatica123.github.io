---
layout: post  
date: 2020-10-11  
categories: coding  
title: "Dialogflow Voicebots"  
---

## Business needs
I was asked to research about the use of Dialogflow for creating voicebots. Since I have already have checked Dialogflow 
features I knew that could be accomplished with one of the provided integrations. But after a quick demo of their capabilities
I was asked to make a demo making outbound calls and triggering a Dialogflow fulfillment after the conversation is over.
I will show you what I found during this research.  

### Dialogflow Phone Gateway
Dialogflow Phone Gateway is great if you need a quick way to test an voicebot agent, its configuration is minimal. The upsides are
that the latency of the bots responses is very small, you don't need any previous knowledge to make it work and in general
a great choice for inbound call agents. The downside is that if you need an additional feature, this option is unable to
help you with it and I was not able to change the trial phone number to a permanent one, since they provide you with a free 
number for only 30 days.  

<img src="https://user-images.githubusercontent.com/10179447/93239764-79b39000-f740-11ea-89db-4238eb6258c6.png" width="350" height="340">

#### Issues
I also checked the majority of the options on the one-click telephony section, but they all have the same limitation. 
The section below for Telephony seemed to be the option for me since what I was looking for was outbound calls for my agents.
While researching about Genesys, I encountered a big turndown for me, they ask you to enter the free trial before they give
you information about how to use their products, I didn't even know if the outbound telephony option would be available,
so I had to skip the research on Genesys Cloud and go right to Twilio.

### Twilio 
For starters, Twilio has great documentation. I had no issue when checking the tool's capabilities, I rapidly knew that 
outbound calls are available in Twilio by using it SDK for node. Also there were demos using a Dialogflow integration, they
use [media-streams](https://github.com/twilio/media-streams) to allow communication between the user and the agent.  

#### Issues
I set up a project that used the media-stream provided by Twilio and outbound calls from US to Guatemala and the results 
weren't as satisfactory as I would have expected. For starters, when making the call from the code to my phone, there were
times that the agent didn't start the conversation. In almost every call, there was a latency in the agent's response, it 
was about 4-6 seconds, and it was painful to wait that long on every bots response. 


### Amazon Connect
Based on a previously made project from another team. I adjusted the code to work with a more structured codebase. After
refactoring the code, I got it running. It was based on a contact flow in Amazon Connect. The conversation flow is the following

<img width="1495" alt="Screen Shot 2020-10-11 at 1 34 05 PM" src="https://user-images.githubusercontent.com/10179447/95688330-b0a58600-0bc6-11eb-90e0-944bb2976d20.png">

The code is mostly a project in Spring Boot framework for stream parsing. It retrieves the stream from the call to an encoded
audio format, the audio goes to Google Speech-to-Text service and finally the result goes to Dialoflow to get the last response.
This service is active all times and the streams are triggered by the lambdas in the contact flow. The first one is KVSTrigger,
which sends the stream information of the call to the Java service. When the communication is set, the ResponseRequester 
function goes to the same service to get the responses from Dialogflow.  

<img width="350" alt="architecture" src="https://user-images.githubusercontent.com/10179447/95688908-9cfc1e80-0bca-11eb-842e-ed0249416823.jpg">  

#### Issues
In a test I got a major problem, the communication in the contact flow is looped until the response requester gets a message
of goodbye from Dialogflow. So I had a call that didn't get to the end of conversation and the communication continued until
the service reached to its limit. After that I had to get a refund of that credit and ask for more capacity to keep going.

<img width="687" alt="Screen Shot 2020-10-05 at 2 46 17 PM" src="https://user-images.githubusercontent.com/10179447/95688334-bb601b00-0bc6-11eb-9286-8b3ab46ba935.png">  

This is the most complete solution, even if the latency is about 5 seconds, the capabilities of the tools are wide, and 
there is a lot of room for improvement.