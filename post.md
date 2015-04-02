## Introduction
If you aren't using [Ionic](http://ionicframework.com "Ionic Framework") by now, then you are truly missing out on a great ~~framework~~ ecosystem for developing hybrid mobile applications.  Ionic has created a services platform called [ionic.io](https://ionic.io "Ionic Services Platform") that contains some value added services.  For example, Ionic Creator allows you to drag and drop UI components to create and generate an actual Ionic app.  Another value added service is Ionic View, which allows you to easily share your app with others by running your app inside of an app that they download from the app store.  Basically running your app inside of another app.

Ionic Push is the latest addition to the ionic.io services platform.
It provides you with the ability to easily send push notifications to your Ionic application.  This new service is currently in alpha so you will need to put in an [Ionic Push signup](https://apps.ionic.io/landing/push "Ionic Push signup") request to start using it.  If you have an existing Ionic project, you can [add push notification support](http://docs.ionic.io/push/installation/ "Add Ionic Push Notification Support to an existing Ionic app") to it or you can follow the [Quick Start guide](http://docs.ionic.io/push/ios/ "Quick Start Guide").  DISCLAIMER: Ionic Push is in early alpha and this is really just a sneak peak.  Breaking changes may occur later.

## Quick Start
The [Quick Start guide](http://docs.ionic.io/push/ios/ "Quick Start Guide") will walk you thru the steps of creating the starter app.  It will also guide you thru the Push Notification provider configuration.  For example, for iOS(Apple Developer Account Setup) or for Android(Google Project Setup).  I encourage you to follow the guide as I not going to regurgitate all of the information there.  I am just going to explain some areas in more detail and elaborate a little.

As part of the quick start guide, you will be asked to do the following to create a starter application.
```
$ ionic start pushItRealGood push
$ cd pushItRealGood
$ ionic upload
```
What this does is essentially place the app in the Ionic Services platform and assigns an `app_id` to your project.  It also places this `app_id` in the `ionic.project` file at the root of your project's directory.
```javascript
{
  "name": "pushItRealGood",
  "app_id": "raaa6345d"
}
```
You will need this `app_id` to configure the starter app.  For Android, you will also need the Google Project Number to provide in the `.config` block of the `app.js` in your project's root directory. 

Also, you will need to register your Google Server API key with Ionic.  This is not your Google Project Id.  This is the API key that is created in the Google Developer's Console.

```
 $ ionic push --google-api-key [your-google-api-key]
```

Now you are ready to deploy and launch the application.

## Launch the Starter App
First you will need to add the platform(s) you intend to use by doing the following.
```
$ ionic platform add ios android
```
When you deploy the app, I recommend you do so with livereload like this:
```
$ ionic run android -l -s -c
or 
$ ionic run ios -l -s -c
```
For now, since we just want to see this push notification feature work, you are going to copy the token from the console log and use it to send a push notification.  Normally, you wouldn't need to do this because we would [setup a web hook](http://docs.ionic.io/push/server/ "Setup a web hook for user identification and device registration") instead. Although, not recommended, you could make our own request to your own backend to inform it that the user and token are being registered.

## Identify and Register for Push
On the device, you will need to first "Identify" the user.  Tap on the "Identify" button and tap "ok" on the alert.  Then tap on the "Register for Push" button and tap "ok" on the alert. You should see some JSON displayed to the top of the page and some "Device token ok" text under the "Register for Push" button.  

(//imgur.com/kzUpPWF.png)

Among other things you should see something in the terminal window that looks like this:
```
Got token, APA91bGxOUuQiyVzKBSg0txjzkMYNseWgtjVQU3WRNvN9dCjyr6nfaz9wENsRG6jpAap6kt0mRNJPVn1jAUO0n7T1_secsGi-WhQLsQp3CSFNkEfsmPYLcNMvMiWHnMZQTtmMtxrYlo3yVy6myDxWO-RToqnTESOA-B2u_JPpNFGTQAxRzf4TZm3Ql-Yni4_qOIpFkjdD9z2, android
```
This token should be the same as the `regid` that was displayed at the top of the page in the app.  Copy this token because we are going to need it when we send the push notification.

## Sending Push Notifications
To send a push notification, you just need to send a Rest API request. You could create a `curl` request to test the sending of a push notification.  But [ain't nobody got time for that!](https://www.youtube.com/watch?v=waEC-8GFTP4 "Ain't nobody got time for that!"). But thanks to Ben Rondeau, you can install a node package that will send push notifications using a simple API.  You could actually use this in a Javascript/Node.js application to send push notifications based on some event.  For now, we are just going to use it to mimic what one of our server-side applications would do.  After all, its just a Rest API, so we could easily make those calls in just about any language these days.

First, install the ionic-push-server node package like this:
```
   $ npm install ionic-push-server
```
Now we can use this package by creating a little push client. Create a file called something like `ionic_push.js` with the following contents.
```javascript,linenums=true
var ionicPushServer = require('ionic-push-server');
 
var credentials = {
    IonicApplicationID : "ionic_app_id",
    IonicApplicationAPIsecret : "ionic_app_private_key"
};
 
var notification = {
  "tokens":["myDeviceToken"],
  "notification":{
    "alert":"Hi from Ionic Push Service!",
    "ios":{
      "badge":1,
      "sound":"chime.aiff",
      "expiry": 1423238641,
      "priority": 10,
      "contentAvailable": true,
      "payload":{
        "key1":"value",
        "key2":"value"
      }
    }
  } 
};
 
ionicPushServer(credentials, notification);
```
The `IonicApplicationID` is the `app_id` from your `ionic.project` file at the root of your project directory.  The `IonicApplicationAPIsecret` is the private API key. To locate these keys on the [apps.ionic.io](http://apps.ionic.io "apps.ionic.io") site go [here](http://docs.ionic.io/find-your-keys/ "Find your keys").

Now you can send the notification by executing the javascript.
```
$ node ionic_push
```
On your device, you should have received the the push notification with the message "Hi from Ionic Push Service" at the top of the page.

## Summary
Pat yourself on the back and send out a tweet(tag @ionicframework if you do) you are a rockstar.  You are well on your way to adding real-world push notifications to your existing or future Ionic applications.  Ionic continues to add to its ionic.io services platform.  Like Ionic Creator and Ionic View; Ionic Push is just one more example of the awesomeness coming from Ionic in terms of value added services.
