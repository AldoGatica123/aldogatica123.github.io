---
layout: post
date: 2020-02-28
categories: coding
title: "ReactJS app using Amplify"
---

# ReactJS app using Amplify

## Lambda Survey

This project consists in a customer satisfaction survey built in [React.js][react-link],
hosted in [AWS Amplify][amplify-link] and with a [Chalice][chalice-link] backend. It was my first time using
[Hooks][hooks-link] and [functional components][components-link].

This project was a challenge for me since it needed to be done in a short time.
That said, I chose technologies I already knew for the frontend and backend.
I wanted a nice CI/CD pipeline and sincerely, I forgot how to use [Travis][travis-link], and
doing a little research, I saw that AWS has a nice product for deploying with a
simple setup.

The code frontend is [here][frontend-link] and the backend [here][backend-link]

Here is a [demo][demo-link] of the app

## Architecture

![screenshot](https://user-images.githubusercontent.com/10179447/75516560-63bbc900-59c2-11ea-9588-1bbf6e0ba37b.jpg)

## CI/CD Amplify Pipeline

Initially I only had one deployment environment, but later created another one
to host a test environment. Amplify gets the code from a remote repository, the
project gets built, tested and deployed very easily. For this project I didn't
make tests, just tested it in the testing branch.

![CICD Pipeline](https://user-images.githubusercontent.com/10179447/78611261-cd31c180-7823-11ea-98d6-6dd51b6576f8.jpg)


### Why Amplify and not Github Pages?

Using the Github Pages for my projects forces me to use the github.io domain
and indirectly links it to my blog, that doesn't happen with Amplify endpoints.
Amplify already has a simple configuration for building the React project.

### Using functional components and hooks

Initially I started using class components, later it became difficult to use the
state and props on each component. There was too much code for simple functionality.
After checking the documentation and tutorials I decided to refactor the
project to use functional components and hooks to manage the events and state.
The code became simpler, shorter and easier to test. From now on I will use this
for my React projects.

### Backend in chalice

The main goal for the results on is to be sent to an email address. I did this
in Python using Chalice since I already knew how to use it, but in the future
will checkout another alternative since I think the project is so popular as I
thought. I sent the emails using AWS Simple Email Service.



[react-link]: https://reactjs.org/  
[amplify-link]: https://aws-amplify.github.io/docs/js/react  
[frontend-link]: https://github.com/AldoGatica123/lambda_survey  
[backend-link]: https://github.com/AldoGatica123/lambda_survey_chalice  
[chalice-link]: https://chalice.readthedocs.io/en/latest/  
[hooks-link]: https://es.reactjs.org/docs/hooks-reference.html  
[components-link]: https://es.reactjs.org/docs/components-and-props.html 
[travis-link]: https://travis-ci.com/   
[demo-link]: https://prod.d1lvjpzd91wjlf.amplifyapp.com/  
