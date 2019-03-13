---
layout: post  
date: 2019-03-12  
categories: coding  
title: "Using AWS Chalice with Travis"  
---

# eDent Backend
I will be sharing my experience while working a project using [AWS Chalice][chalice-link], 
I needed a RESTful API to save contact info for some patients in Python.
The code is [here][repo-link].  

What interested me the most of Chalice was that I can locally run my lambdas while using 
DynamoDB and S3 easily and the [workshops][workshop-link] looked pretty good.  

## Pytest
It's also my first time writing tests so I watched some tutorials on Udemy about pytest. 
It worked like a charm in my local environment, but I had to have some trouble when I wanted
to run my tests in Travis, because I got a lot of `ModuleNotFoundError` when importing modules into tests. 
What I found was that pytest needs to find `conftest.py` in the root directory to work properly.
```text
├── app.py
├── chalicelib
│   ├── __init__.py
│   ├── config.py
│   └── database.py
├── conftest.py
├── requirements.txt
├── scripts
│   └── clear_database.py
└── tests
    ├── config.py
    ├── test_app.py
    └── test_index.py
```

## CI/CD Travis Pipeline
The CI pipeline I wanted to have is something like this:  
_(Don't be so harsh on me, this is my first time using Travis-CI and pytest)_  
![Pipeline](https://user-images.githubusercontent.com/10179447/53920261-23856d00-4032-11e9-95f7-993098fe935e.png)

I used Travis because _**it's free for open source projects!**_ and Codacy because my python still 
sucks and I want to see where can I improve.

### Danger Zone

To make Travis run tests after fixing the problems I first encountered I had to store the AWS credentials into
environment variables.  

___What I didn't know was that Travis has two different platforms!___  
https://travis-ci.org/ and https://travis-ci.com/
_I was struggling cause when I needed to secure my credentials using_ `travis encrypt` _I was using the one that ends with .org_  
What I had to do to encrypt in the .com Travis was `travis login --pro` before encrypting!

```yaml
language: python
python:
  - "3.6"
install:
  - pip install -r requirements-test.txt
env:
  - TABLE_NAME=dynamodb_table_name
  - AWS_DEFAULT_REGION=region
  - secure: [encrypted]
  - secure: [encrypted]
script:
  - pytest --env qa -v
```

After making these changes I got my Travis running the tests in the QA stage!

#### Manage roles instead of credentials!
_The issue here is that `chalice deploy` wasn't working because it couldn't find the environment variables.
What I learned was that lambdas must **not** contain the `AWS_ACCESS_KEY_ID` or `AWS_SECRET_ACCESS_KEY` 
since they are reserved and the AWS execution role should provide them._ ([source][aws-env-var-link])  
_After tweaking the IAM roles, I changed the chalice qa and production stage so they use their roles._
```
"qa": {
      "manage_iam_role": false,
      "iam_role_arn": "arn:aws:iam::account-id:role/role",
      "environment_variables": {
        "TABLE_NAME": "table_name"
      },
      "api_gateway_stage": "qa"
    }
```

#### Avoid creating multiple API-Gateway endpoints
_I didn't have my `.chalice/deployed` directory in my commit files and Travis was doing multiple endpoints while
the project needed to be deployed on the same I had created. [Solution found here][chalice-issue-link]  
Also I had to make some changes to the AWS policies to work in DynamoDB and API Gateway_

I will still be working on this project, it will be documented in later entries.


[repo-link]: https://github.com/AldoGatica123/edent-contacts  
[workshop-link]: https://chalice-workshop.readthedocs.io/en/latest/  
[chalice-link]: https://chalice.readthedocs.io/en/latest/  
[aws-env-var-link]: https://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html#lambda-environment-variables  
[chalice-issue-link]:  https://github.com/aws/chalice/issues/915
