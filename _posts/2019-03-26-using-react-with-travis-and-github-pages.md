---
layout: post  
date: 2019-03-26  
categories: coding  
title: "Using React with Travis and Github Pages"  
---

# eDent Frontend  
I haven't worked before as a web developer, I had experience making mobile applications but
I didn't really enjoy it. Since I am working in the eDent system I needed to get my hands dirty
with web development for the client app.
I chose [React][react-link] because I knew it had a lot of documentation and useful libraries, the one that
I liked the most was [Material-UI][materialui-link]
The code is [here][repo-link].  

## CI/CD Travis Pipeline  
After having a running application with a decent UI I was ready to make de CI/CD pipeline with [TravisCI][travis-link].
Even thought I already don't have any tests I wanted to automate deployments to a Github Page.
The CI pipeline I wanted to have is something like this:  
![eDent_react_pipeline](https://user-images.githubusercontent.com/10179447/55016839-321dcf00-4fb5-11e9-9ff3-6abf7cc05c79.jpg)

## Github Pages Deployments 
To make Travis deploy the application to Github Pages I needed to provide an Access Token from Github. So I generated
one and encrypted it with `travis encrypt GITHUB_TOKEN=secretvalue` and copied the .travis.yml configuration for
[Github Pages Deployments][githubpagesdep-link]. 

```yaml
language: node_js
node_js:
- stable
env:
  global:
    secure: [encrypted]
cache:
  directories:
  - node_modules
script:
- npm test
- npm run build
deploy:
  provider: pages
  skip_cleanup: true
  github_token: "$GITHUB_TOKEN"
  local_dir: build
  on:
    branch: master
```

### Homepage in package.json
The first try I didn't know why it didn't work, but then I read I had to add the **homepage** field to package.json.
I changed it to `"homepage": "https://aldogatica123.github.io/edent-react/"` so it wouldn't collide with the blog.

After pushing code to the repo, Travis took a minute or so to deploy to my page correctly.  
[Demo][demo-link]


[react-link]: https://reactjs.org/  
[materialui-link]: https://material-ui.com/  
[repo-link]: https://github.com/AldoGatica123/edent-react  
[travis-link]: https://travis-ci.com/  
[githubpagesdep-link]: https://docs.travis-ci.com/user/deployment/pages/  
[demo-link]: https://aldogatica123.github.io/edent-react/  
