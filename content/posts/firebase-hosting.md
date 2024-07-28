---
title: "Hosting your Hugo static website on Firebase"
date: 2024-07-28
draft: false
---
## Deploying a Hugo static website on Firebase

static websites generators are a practical and simple way to publish content on the web and [Hugo](https://gohugo.io) is one of those generators, its simplicity, lightness and seamless structure is remarkable. Following the quickstart guide is straight forward. Once you're past creating a website and a post locally we can move to hosting.

First you'll need to install [firebase-cli](https://firebase.google.com/docs/cli#setup_update_cli) then run `firebase login`. Next initialize a firebase project with `firebase init hosting:github`. This will setup both firebase hosting and a CI/CD using github actions. You'll be asked to provide details to configure the hosting directory which is `public` by default and some other related hosting config then `firebase-cli` will create a service account that has the required permissions to deploy the website from github action pipeline then insert the service account credentials as secrets in github and create a skeleton for a github action pipeline. Here's the one I wrote:

```
name: Deploy to Firebase Hosting on merge
'on':
  push:
    branches:
      - master
jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Checkout submodules
        run: git submodule update --init --recursive

      - uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: "latest"
          extended: true

      - run: hugo --minify
      - uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: '${{ secrets.GITHUB_TOKEN }}'
          firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT }}'
          channelId: live
          projectId: $GCP_PROJECT_ID
```

This will deploy the website on merging to master. if you want to do other CI tasks to do static checks create another pipeline and use `on: pull_request` instead. Commit the github action. At this point you'll probably have a different Host name configurations in your `config.toml` and what is configured on Firebase which will be something like `$PROJECT_ID.web.app` to fix that you'll need to use your desired/existing domain name with you Firebase hosting deployment following [Custom host setup](https://firebase.google.com/docs/hosting/custom-domain). Now you have a working CI/CD setup and serving your static website securely over HTTPS.
