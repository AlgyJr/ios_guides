## Overview

[OAuth](http://oauth.net/2/) is an authentication and authorization framework for Internet users to log into third party websites using their Google, Facebook, Twitter or other accounts without exposing their password. Put simply Oauth enables the "Login with Facebook Account (or other social accounts)" to third party apps. When you authenticate to an app using some social account, Oauth defines mechanisms where certain user permissions are delegated to the app. For example, the app may have permissions to query your facebook page to retrieve your name, profile picture and other details which it can then use to create your profile page on the app. App's typically do this by querying API's exposed by the service.

## Building your own Twitter App using OAuth

In this guide we will try to build our own Twitter App. We will use OAuth to authenticate users and query their twitter accounts to retrieve their home timeline, profile page and other details. 

### Register your app on Twitter's developer portal

the first thing you need to do is create a new app on [twitter's developer portal](apps.twitter.com) and create a new app. 

<img src="https://i.imgur.com/iIL9d5W.png" alt="Twitter Oauth Demo" width="600" height="400" />

