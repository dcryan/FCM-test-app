## Getting FCM working with Ionic.

Most of the instructions were taken from here.  
https://medium.com/one-tap-software/ionic-2-push-notifications-with-fcm-2a9078b90fe7


## Seting up iOS
You need to create the certs, provisioning profiles, and an APNs key. First you need to create a new Identifier.
Under AppIDs, press the '+' to register a new App ID. Set the App ID Description to anything, in my
case I set it to 'FCM Test - Daniel'. With 'Explicit App ID' selected, create a bundle ID. It'll
usually be something like 'com.companyname.appname'. Select Push Notifications under App Services.

Continue and press Register.

Now you need to create a Push Notification cert.
Under App IDs still, edit your App ID that you just created. Under Push Notifications, you'll
see a place to create a push notification cert for that app. Do the development cert for now,
and do the prod one later.

Now you need to create an APNs Key.
Under Keys, press the 'All' tab and press the '+' to create a new one. Create a name for it.
In my case, I named it 'Firebase'. Select 'Apple Push Notifications service (APNs)' and hit
continue and finish.

Next you need to create your app in iTunesConnect. Log in, and go to 'My Apps'. Click the '+'
button and select 'New App'. Select your new App ID, and finish.

All done setting up iOS certs, provisioning profiles, and APNs key. You will refer back here
to get some information when creating the app in the firebase console.

## Setting up your app in Firebase
Under Settings/General click Add App. Select iOS and refer to iTunesConnect and certs, identifiers,
and provisioning profiles for all the info.

```
Name             Found In
iOS bundle ID    App IDs
App nickname     N/A
App Store ID     iTunesConnect App info
```
  
Register App.  

Download the GoogleService-Info.plist file and save it in the root of your Ionic app directory.
Finish the wizard and ignore the Firebase SDK and initialization code.

Now go to Settings/Cloud Messaging.
It's asking for the APNs Authentication Key. Press 'upload'. It'll will launch another wizard.
In the Cert, Provisioning Profile, and Keys page, go to 'Keys' and find the key you created.
Download it and note the Key ID. Upload the downloaded key and paste the Key ID in. The App ID
prefix is located under 'Identifiers/App IDs/YOUR_APP'. You'll see 'Prefix' at the top.

Finish the wizard.

## Setting up the Ionic App

In your Ionic app, download the FCM cordova plugin.
```
ionic cordova plugin add cordova-plugin-fcm
npm install --save @ionic-native/fcm
```
In your app.module.ts, stick this with your other Ionic Native imports:

```
import { FCM } from ‘@ionic-native/fcm’;
```

Don’t forget to add it to the `providers:` section of your module.

Next it needs to be added to a Component. We’re going to add it to our root
Component `app.component.ts` so that we start listening to notifications ASAP. Import it like
you did in `app.module.ts` and add it to your constructor:

```
...blah: BlahBlah, private fcm: FCM)
```

Now, drop this inside `platform.ready()`:

```
this.fcm.subscribeToTopic('all');
this.fcm.getToken().then(token => {
  // backend.registerToken(token);
});
this.fcm.onNotification().subscribe(data => {
  alert('message received')
  if(data.wasTapped) {
   console.info("Received in background");
  } else {
   console.info("Received in foreground");
  };
});
this.fcm.onTokenRefresh().subscribe(token => {
  // backend.registerToken(token);
});
```

The backend.registerToken commands are pulled from the Ionic example. They’re meant to
call an imaginary method once those subscriptions get fired off.

Time to test your app. The iOS setup can be rather complicated and being one character
off will cause it not to work. So be patient and try a test app before implementing it
in your actual app.
