## Overview

[OAuth](http://oauth.net/2/) is an authentication and authorization framework for Internet users to log into third party websites using their Google, Facebook, Twitter or other accounts without exposing their password. Put simply Oauth enables the "Login with Facebook Account (or other social accounts)" to third party apps. When you authenticate to an app using some social account, Oauth defines mechanisms where certain user permissions are delegated to the app. For example, the app may have permissions to query your facebook page to retrieve your name, profile picture and other details which it can then use to create your profile page on the app. App's typically do this by querying API's exposed by the service.

## Building your own Twitter App using OAuth

In this guide we will try to build our own Twitter App. We will use OAuth to authenticate users and query their twitter accounts to retrieve their home timeline, profile page and other details. 

### Register your app on Twitter's developer portal

The first thing you need to do is logon to [twitter's developer portal](apps.twitter.com) and create a new app. The callback field is optional, but enter some value in there as the authentication will not work if it is left empty. We will change it programmatically later.

<img src="https://i.imgur.com/iIL9d5W.png" alt="Twitter Oauth Demo" width="600" height="400" />

Now check the permissions tab. The default permissions the Twitter gives are Read and Write, meaning you app can hit twitter's API's to send and recieve tweets. This is exactly what we are looking for our OAuth Twitter demo so leave this as is. 

<img src="https://i.imgur.com/gW7320Q.png" alt="Twitter Oauth Demo" width="600" height="400" />

Click on the Keys and Access token Tab. Copy the the API Key and API secret. We will need these later. 

<img src="https://i.imgur.com/HzNZTzq.png" alt="Twitter Oauth Demo" width="600" height="400" />

Finally, back on the details tab, copy the request token, Authorization token and Access Token API URL's. This is what we will use to perform the authentication in our app. More on this later. 

<img src="https://i.imgur.com/oDhtkGh.png" alt="Twitter Oauth Demo" width="600" height="400" />

### Building your demo app in xcode

Open xcode and create a new single view application. Navigate to Build Phases and add the Accounts and Social frameworks. We will be using these frameworks to build our app. 

%RED% screenshot %ENDCOLOR%

For the purpose of this guide, we need two files. A view controller to let users login and Twitter Client file, that implements the logic to query Twitter API's to send and receive tweets. 

- Create app in swift 
- put keys in
- login page for twitter
- table view for tweets
