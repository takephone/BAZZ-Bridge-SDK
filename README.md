# BAZZ-Bridge-SDK
SDK to allow 2-way communications between an Android phone and an Android Car Multimedia system

<!-- toc -->

* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Usage](#usage)
  * [Preparing your app](#preparing-your-app)
    * [Adding classes](#adding-classes)
    * [Gradle](#gradle)
    * [Manifest](#manifest)
  * [Initializing the SDK](#initializing)
  * [BAZZ operation modes](#bazz-operation-modes)
  * [Driving detection](#driving-detection)
  * [Incoming messages](#incoming-messages)
  * [Configuring operation](#configuring-operation)
    * [Controlling flow](#controlling-flow)
    * [Controlling playback](#controlling-playback)
    * [Internal setting screens](#internal-setting-screens)
    * [More settings](#more-playback)
  * [Generate your own messages(!)](#generate-your-own-messages)

<!-- toc stop -->

# Prerequisites

* BAZZ SDK works on **Android** devices with OS version **4.0.1** and up.
* BAZZ SDK requires a connected android device (i.e. **data** or **WiFi** connection) to work.
* You will need a developer account with BAZZ. Please [Contact Us](mailto:support@bazz.co) for details.

# Installation

Clone or download BAZZ-SDK from here, and extract it on your computer.
In it you will find a demo application for how the BAZZ SDK can be used.

You can import this project to Android Studio, and use it as a reference
or base to make your alterations.

If you already have an existing project, or want to create a new one,
The BAZZ SDK comes in the form of **bazz_sdk.aar**. You should receive it from us, along with
the App ID.

To add it to your project, follow the steps below:

1. With your project open in Android Studio, select '**File => New => New Module**'
2. In the dialog opened, select '**Import .JAR/.AAR Package**', and click '**Next**'
3. In the '**File name**' line - browse to the '**bazz_sdk**' folder of the demo project,
   and select the **aar** file. Click on '**Finish**'
4. After Gradle Sync is done, go to your project settings: right-click on the project module, and select '**Open Module Settings**'.
5. Select your **app** module in the left pane, and click the '**Dependencies**' tab. Click the **plus (+)** sign, and select '**Module dependency**'. Select '**:bazz_sdk**', and click '**OK**' twice.

Done. Now you can start integrating the BAZZ SDK as explained in the next sections.

# Usage

## Preparing your app

**IMPORTANT:** set your app '**minSdkVersion**' to **16**, and '**targetSdkVersion**' to **19**

### Adding classes

- Add a new java file: '**BazzNotificationServiceLink.java**':

```java
package <your app project package>

import com.esoof.android.bazz_sdk.BroadcastReceiver_Notifications;

public class BazzNotificationServiceLink extends BroadcastReceiver_Notifications {
}
```

- Add a new file: '**MyApplication.java**':

```java
package <your app project package>

public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public void onTerminate()
    {
        super.onTerminate();
    }
}
```

- Do not forget to add the reference to it in your Android Manifest under the '**Application**' header:

```xml
    <Application
        ...
        android:name=".MyApplication"
        />
```

**Note:** if you already have an '**Application**' class in your app - just add the code explained [below](#initializing)


### Gradle

- In your app **build.gradle**, make sure you have **minSdkVersion 16** and **targetSdkVersion 19**.

- Also under **dependencies** add ``` compile 'com.google.android.gms:play-services:7.8.0' ```


### Manifest

- Yes, we know - we're nagging... but this is important - make sure you have these setup:

```xml
    <!-- THIS IS VERY IMPORTANT - make sure your build.gradle also defines these values -->
    <uses-sdk
        android:minSdkVersion="16"
        android:targetSdkVersion="19" />
```

- Add the following permissions to your '**AndroidManifest.xml**' file:
(yes we know - there are tons of them, but they are all required for
the operation of BAZZ...)

```xml
    <uses-permission android:name="android.permission.GET_TASKS"/>

    <uses-permission android:name="android.permission.CALL_PHONE" />

    <uses-permission android:name="android.permission.BROADCAST_STICKY" />

    <uses-permission android:name="android.permission.READ_CONTACTS" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />

    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <uses-permission android:name="android.permission.PROCESS_OUTGOING_CALLS" />

    <uses-permission android:name="android.permission.MODIFY_PHONE_STATE" android:required="false" />

    <uses-permission android:name="android.permission.RECEIVE_SMS"/>
    <uses-permission android:name="android.permission.READ_SMS" />
    <uses-permission android:name="android.permission.READ_MMS" />

    <uses-permission android:name="android.permission.WRITE_SMS" />
    <uses-permission android:name="android.permission.SEND_SMS" />
    <uses-permission android:name="android.Manifest.permission.SEND_SMS" />

    <uses-permission android:name="android.permission.RECEIVE_MMS"/>
    <uses-permission android:name="android.permission.RECEIVE_WAP_PUSH"/>


    <uses-permission android:name="android.permission.RECEIVE_HEADSET_PLUG" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.BLUETOOTH" />

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="android.permission.DISABLE_KEYGUARD"/>

    <uses-permission android:name="com.google.android.gms.permission.ACTIVITY_RECOGNITION"/>

    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

- Also add this inside the '**Application**' tag:

```xml
    <!-- Add this to enable BAZZ to detect driving -->
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />

    <!-- Add this to enable BAZZ to detect driving -->
    <service
        android:name="com.esoof.android.bazz_sdk.ActivityRecognitionIntentService"
        android:label="@string/app_name"
        android:exported="false">
    </service>

    <!-- Add this to enable BAZZ to handle IM messaging apps -->
    <service android:name=".BazzNotificationServiceLink"
        android:label="@string/app_name"
        android:permission="android.permission.BIND_NOTIFICATION_LISTENER_SERVICE"
        android:exported="false">

        <intent-filter>
            <action android:name="android.service.notification.NotificationListenerService" />
        </intent-filter>
    </service>
```

## Initializing

- In your '**Application**' java class, add a global instance variable for BazzLib, and the APP_ID you received from us:

```java
    public static BazzLib mBazzLib = null;
    
    private static final String APP_ID = "<Your BAZZ SDK app id here>";
```

- In the '**onCreate**' function, add the following lines:

```java
        mBazzLib = new BazzLib();

        if (mBazzLib!=null)
        {
            // APP_ID is a string you will receive from us when we register your app
            mBazzLib.init(this,APP_ID);
            
            // You must call these functions, too to initialize the app properly
            mBazzLib.setAppName("<Your app user-friendly name>");
            mBazzLib.setMainActivity("<Class name of your main activity (e.g. MainActivity)>");

            // These calls enable treatment of the IM messaging apps
            mBazzLib.setIncomingWorkWithWhatsapp(true);
            mBazzLib.setIncomingWorkWithMessenger(true);
            mBazzLib.setIncomingWorkWithLine(true);
            mBazzLib.setIncomingWorkWithGmail(true);
            mBazzLib.setIncomingWorkWithLine(true);

            // *** You must add this to enable access to IM messaging apps ***
            bindService(new Intent(getApplicationContext(), BazzNotificationServiceLink.class), new ServiceConnection() {
                @Override
                public void onServiceConnected(ComponentName className, IBinder service) {
                }
                @Override
                public void onServiceDisconnected(ComponentName arg0) {
                }
            }, Context.BIND_AUTO_CREATE);
        }
```

- In the '**onTerminate**' function, add the following lines:

```java
        if (mBazzLib!=null)
        {
            mBazzLib.shutdown(this);
            mBazzLib = null;
        }
```

- **Important:** if you need your app to work with your own server - set the server URL by calling the following function **before** you call 'mBazzLib.init...':

```java
        mBazzLib = new BazzLib();

        if (mBazzLib!=null)
        {
            // Set a custom server URL:
            mBazzLib.setServerURL("<your server URL>");
        
            // APP_ID is a string you will receive from us when we register your app
            mBazzLib.init(this,APP_ID);
            ...
```            




## BAZZ operation modes

BAZZ has 3 operation modes:

* **Off**: BAZZ is idle
* **Always**: BAZZ is ON, and monitoring incoming messages, to treat them
* **When driving**: BAZZ is monitoring driving conditions (using 'sensors' like GPS, Bluetooth, Wired headset, connection to charger), and will handle incoming messages only when in a drive

By default BAZZ is in '**Always**' mode (you can see a green indicator in the device top status line).
To change modes use the following calls:

```java
    if (MyApplication.mBazzLib!=null)
    {
        MyApplication.mBazzLib.setBazzMode(BazzLib.BAZZ_MODE_OFF);
        
        MyApplication.mBazzLib.setBazzMode(BazzLib.BAZZ_MODE_ALWAYS_ON);

        MyApplication.mBazzLib.setBazzMode(BazzLib.BAZZ_MODE_ON_WHILE_DRIVING);
    }
```


To read the current mode call:

```java
    if (MyApplication.mBazzLib!=null)
    {
        int currMode = MyApplication.mBazzLib.getBazzMode();
    }
```


To check if BAZZ is '**active**' (i.e. treating messages):

```java
    if (MyApplication.mBazzLib!=null)
    {
        boolean isBazzActive = MyApplication.mBazzLib.getBazzActive();
    }
```


You can also listen to changes in BAZZ mode (e.g. when BAZZ turns ON cause it detected driving state):

```java
    MyApplication.mBazzLib.setOnBazzModeChangedListener(new BazzLib.BazzModeChangedListener() {
        @Override
        public void onBazzModeChanged() {
            // MYTODO: do somthing here cause BAZZ mode has changed...
        }
    });
```


As mentioned above, the BAZZ SDK indicates it's current state in the system notifications area (aka 'status line' - the top line on the screen) - you will see an **orange** icon when BAZZ is in '**When driving**' mode and waiting for a drive, or a **green** icon when BAZZ is either in '**Always**' mode or in "**When driving**' and in a drive. If you want to turn this feature off or on, use:

```java
    if (MyApplication.mBazzLib!=null)
    {
        boolean isIconInNotificationsAreaOn = MyApplication.mBazzLib.getShowStateInNotificationsArea();
        
        MyApplication.mBazzLib.setShowStateInNotificationsArea(true/false);
    }
```



## Driving detection

BAZZ uses various 'sensors' to detect driving conditions (decide if the device is now in a vehicle driving, or when it stopped). 

Use the following code to allow your app to monitor these events"

```java
    if (MyApplication.mBazzLib!=null)
    {
            //--- BAZZ driving detection events ----------------------------

            MyApplication.mBazzLib.setOnDrivingEventListener(new BazzLib.BazzDrivingEventsListener() {
                @Override
                public void onDrivingSensorEvent(String source, boolean newState) {

                    HandleCallbackFromLib("Driving sensor '"+source+"': "+(newState?"On":"Off"));
                }

                @Override
                public void onDrivingStarted() {
                    HandleCallbackFromLib("Driving started");
                }

                @Override
                public int onPlayDriveStartedPrompt() {
                    HandleCallbackFromLib("Play 'driving started' prompt");
                    return 0;
                }

                @Override
                public void onDrivingEnded() {
                    HandleCallbackFromLib("Driving ended");
                }

                @Override
                public int onPlayDriveEndedPrompt() {
                    HandleCallbackFromLib("Play 'driving ended' prompt");
                    return 0;
                }
            });
    }
```

**Note:** when it detects '**drive started**' or '**drive ended**' events, BAZZ plays default sounds prompts. You can change those, and assign your own resource ID by returning it from inside '**onPlayDriveStartedPrompt**' and/or '**onPlayDriveEndedPrompt**' callbacks.



BAZZ uses the following '**sensors**' to decide on drive/stop: 
- a mix of sensors and algorytms to device on driving based on speed, accelarations, etc.
- detection of Bluetooth connection with Bluetooth devices (e.g. Car multimedia system).
- user plugging in a wired headset.
- user pluggin the phone into a charger/docking station.

You can can enable/disable these using:

```java
    if (MyApplication.mBazzLib!=null)
    {
        MyApplication.mBazzLib.setDriveDetectByDriving(true/false);
        
        MyApplication.mBazzLib.setDriveDetectByBluetooth(true/false);
        
        MyApplication.mBazzLib.setDriveDetectByWiredHeadset(true/false);
        
        MyApplication.mBazzLib.setDriveDetectByCharger(true/false);
    }
```

You can can query current enable/disable state using:

```java
    if (MyApplication.mBazzLib!=null)
    {
        boolean bOn;
    
        bOn = MyApplication.mBazzLib.getDriveDetectByDriving();
        
        bOn = MyApplication.mBazzLib.getDriveDetectByBluetooth();
        
        bOn = MyApplication.mBazzLib.getDriveDetectByWiredHeadset();
        
        bOn = MyApplication.mBazzLib.getDriveDetectByCharger();
    }
```

## Bluetooth devices ignore list

User may not want ALL his/her Bluetooth devices to activate BAZZ (e.g. connecting your phone to your smart watch does not mean you're driving...). Use this code to popup the internal '**Bluetooth devices**' setting screen:

```java
    if (MyApplication.mBazzLib!=null)
    {
        MyApplication.mBazzLib.popupBluetoothIgnoreSettingsUI();
    }
```

## Incoming Messages

BAZZ is all about treating messages. It can intercept incoming messages from various sources, such as SMS/TEXT, Whatsapp, etc. and allow the user to hear & respond to them using voice only.

You can can enable/disable Messaging apps using:

```java
    if (MyApplication.mBazzLib!=null)
    {
        MyApplication.mBazzLib.setIncomingWorkWithSMSorMMS(true/false);
        
        MyApplication.mBazzLib.setIncomingWorkWithWhatsapp(true/false);
        
        MyApplication.mBazzLib.setIncomingWorkWithMessenger(true/false);
        
        MyApplication.mBazzLib.setIncomingWorkWithLine(true/false);
        
        MyApplication.mBazzLib.setIncomingWorkWithGmail(true/false);
        
        MyApplication.mBazzLib.setIncomingWorkWithWeChat(true/false);
    }
```

You can can query current enable/disable state using:

```java
    if (MyApplication.mBazzLib!=null)
    {
        boolean bOn;
    
        bOn = MyApplication.mBazzLib.getIncomingWorkWithSMSorMMS();
        
        bOn = MyApplication.mBazzLib.getIncomingWorkWithWhatsapp();
        
        bOn = MyApplication.mBazzLib.getIncomingWorkWithMessenger();
        
        bOn = MyApplication.mBazzLib.getIncomingWorkWithLine();
        
        bOn = MyApplication.mBazzLib.getIncomingWorkWithGmail();
        
        bOn = MyApplication.mBazzLib.getIncomingWorkWithWeChat();
    }
```

To be notified of incoming messages in your app, use:

```java
    MyApplication.mBazzLib.setOnIncomingMessagesListener(new BazzLib.BazzIncomingMessagesListener() {
        @Override
        public boolean onIncomingMessagesListener(String type,
                                                  String phone,
                                                  String name,
                                                  String text)
        {
            // handle the messages here...
        
            return false;
        }
    });
```

The parametrs here are:

- type: the type of the incoming message. possible values are:
```java
    BazzLib.MSG_TYPE_SMS
    BazzLib.MSG_TYPE_MMS
    BazzLib.MSG_TYPE_WHATSAPP_CONTACT
    BazzLib.MSG_TYPE_WHATSAPP_GROUP_PLAY_NAME_ONLY
    BazzLib.MSG_TYPE_WHATSAPP_GROUP_PLAY_CONTENT
    BazzLib.MSG_TYPE_WECHAT_CONTACT
    BazzLib.MSG_TYPE_WECHAT_GROUP_PLAY_NAME_ONLY
    BazzLib.MSG_TYPE_WECHAT_GROUP_PLAY_CONTENT
    BazzLib.MSG_TYPE_INCOMING_CALL
    BazzLib.MSG_TYPE_FACEBOOK
    BazzLib.MSG_TYPE_LINE
    BazzLib.MSG_TYPE_GMAIL
```
- phone: phone number of sender (if obtainable)
- name: name of sender
- text: body of message

**Note:** you can stop BAZZ form handling the message by returning **true** from the callback function.

## Configuring operation

### Controlling flow

Normally, when BAZZ gets an incoming message, it treats it like this:

First it pops up a screen as an indication to the driver (and to allow the driver to stop message handling at all time by a touch anywhere (note - this is a "panic button", user does **NOT** have to touch the screen to do this, he/she can command BAZZ to stop using voice commands, too).

If the drivier is using a navigation app, then the popup will not be a full-screen, but rather a floating bubble to allow the driver to still see and use the map. You can configure BAZZ to **always** use this floating bubble:

```java
        MyApplication.mBazzLib.setUseFloatingBubble(true/false);
        
        bOn = MyApplication.mBazzLib.getUseFloatingBubble();
```

Then BAZZ plays a voice prompt "new message from..." and the sender name or phone. Next step can be configured by your app:
- BAZZ prompts the user to say '**stop**' or '**continue**', then listens to the user's command (**default** setting).
- BAZZ can automatically continue to reading the message content.
- BAZZ can stop here.

To configure this:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setVoiceCommandsAfterSenderDoWhat(BazzLib.VOICE_COMMANDS_AFTER_SENDER_WAIT_COMMAND);
        // - or -
        MyApplication.mBazzLib.setVoiceCommandsAfterSenderDoWhat(BazzLib.VOICE_COMMANDS_AFTER_SENDER_READ_CONTENT);
        // - or -
        MyApplication.mBazzLib.setVoiceCommandsAfterSenderDoWhat(BazzLib.VOICE_COMMANDS_AFTER_SENDER_DO_NOTHING);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        int current = MyApplication.mBazzLib.getVoiceCommandsAfterSenderDoWhat();
    }
```

Now BAZZ plays the message content. Next step can be configured by your app:
- BAZZ prompts the user to select the reply method by voice command ('**stop**', '**callback**', '**text**', '**record**', '**repeat**'), then listens to the user's command (**default** setting).
- BAZZ can automatically send a pre-configured text message.
- BAZZ can stop here.

To configure this:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setVoiceCommandsAfterContentDoWhat(BazzLib.VOICE_COMMANDS_AFTER_CONTENT_WAIT_COMMAND);
        // - or -
        MyApplication.mBazzLib.setVoiceCommandsAfterContentDoWhat(BazzLib.VOICE_COMMANDS_AFTER_CONTENT_SEND_TEXT);
        // - or -
        MyApplication.mBazzLib.setVoiceCommandsAfterContentDoWhat(BazzLib.VOICE_COMMANDS_AFTER_CONTENT_DO_NOTHING);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        int current = MyApplication.mBazzLib.getVoiceCommandsAfterContentDoWhat();
    }
```

###Special case: Whatsapp group messages

For Whatsapp **group** messages, there is an additional configuration you can make:
- do not play them at all
- play the "a message from group <name> (and only once per drive)
- treat it like a 'normal' message

To configure this:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setWhatsappGroupTreatmentMode(BazzLib.WHATSAPP_GROUP_TREATMENT_DO_NOT_PLAY);
        // - or -
        MyApplication.mBazzLib.setWhatsappGroupTreatmentMode(BazzLib.WHATSAPP_GROUP_TREATMENT_PLAY_NAME_ONLY);
        // - or -
        MyApplication.mBazzLib.setWhatsappGroupTreatmentMode(BazzLib.WHATSAPP_GROUP_TREATMENT_PLAY_ALL);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        int current = MyApplication.mBazzLib.getWhatsappGroupTreatmentMode();
    }
```

### Whatsapp groups ignore list

User may not want BAZZ to read ALL his/her Whatsapp groups (some of them can be really noisey...) Use this code to popup the internal '**Whatsapp groups**' setting screen:

```java
    if (MyApplication.mBazzLib!=null)
    {
        MyApplication.mBazzLib.popupWhatsappGroupsIgnoreSettingsUI();
    }
```



###Incoming calls

You can also control treatment of incoming **calls**:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setIncomingHandleIncomingCalls(true/false);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        boolean bOn = MyApplication.mBazzLib.getIncomingHandleIncomingCalls();
    }
```

###Sound notifications from other apps

You can mute/unmute sound of notifications from other apps:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setIncomingMuteOthers(true/false);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        boolean bOn = MyApplication.mBazzLib.getIncomingMuteOthers();
    }
```

###Blocking unknown senders

And finally - you can ignore messages from unknown senders:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setIncomingReadBlockedOrUnknownSenders(true/false);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        boolean bOn = MyApplication.mBazzLib.getIncomingReadBlockedOrUnknownSenders();
    }
```

###Setting predefined TEXT reply

You can change the pre-defined TEXT:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setTextReply("Bla bla bla...");
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        String currStr = MyApplication.mBazzLib.getTextReply();
    }
```


### Controlling playback

**Playback volume**
You can set the playback volume (0-15):

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setPlaybackVolume(12);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        int currVolume = MyApplication.mBazzLib.getPlaybackVolume();
    }
```

**Playback Headset volume**
You can set the playback volume for a connected **wired headset** (0-15):

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setHeadsetVolume(8);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        int currVolume = MyApplication.mBazzLib.getHeadsetVolume();
    }
```

**Playback speed (for Hebrew)**

If you're handling **Hebrew** text messages, you can set the speed of message content playback (1-5):

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setPlaybackSpeed(3);
    }
```

You can get the current setting:

```java
    if (MyApplication.mBazzLib != null)
    {
        int currSpeed = MyApplication.mBazzLib.getPlaybackSpeed();
    }
```

### Internal setting screens

**Bluetooth playback devices configuration**

Due to the complexity of Bluetooth interface standards, user may have to configure the type of Bluetooth device he/she has manually. To open the (internal) '**Bluetooth devices**' setting screen:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.popupBluetoothDeviceSettingsUI();
    }
```


**Text-To-Speech**

You can configure BAZZ to handle multiple languages for text content readout. To open the setting screen:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.popupTextToSpeechSettingsUI();
    }
```


**System settings required - enabling 'notification access' to your app**

To allow BAZZ to read IM messaging apps input, the user must manuall enable your app in the **system** '**notifications**' settings screen. To open it:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.popupNotificationAccessUI();
    }
```

To check if this is needed:

```java
    if (MyApplication.mBazzLib != null)
    {
        boolean bAppEnabledInSystemNotifications = MyApplication.mBazzLib.getIsNotificationAccessEnabled();
    }
```


### More settings

You can set driver name / phone / gender:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setDriverName("<name>");
        MyApplication.mBazzLib.setDriverPhone("<phone>");
        MyApplication.mBazzLib.setDriverGenderIsFemale(true/false);
    }
```

You can get the current settings:

```java
    if (MyApplication.mBazzLib != null)
    {
        String  name      = MyApplication.mBazzLib.getDriverName();
        String  phone     = MyApplication.mBazzLib.getDriverPhone();
        boolean bIsFemale = MyApplication.mBazzLib.getDriverGenderIsFemale();
    }
```




## Generate your own messages(!)

You can send your own text messages to BAZZ for it to readout to the user. To do this:

```java
    if (MyApplication.mBazzLib != null)
    {
        String requestId = MyApplication.mBazzLib.requestAddIncomingMessage(phone,name,text);
    }
```

Parameters are:

- **phone:** the phone number to reply to - if this is specified, the user will be prompted to select a reply method - he/she can send back a text message, call back, record a voice message, or stop
- **name:** the sender name as BAZZ will read it to the user (i.e. "new message from (name)")
- **text:** the actual text to read to the user

Return value:

is a String holding the ID of the request - you can use it to get the response from:

```java
    if (MyApplication.mBazzLib != null)
    {
        MyApplication.mBazzLib.setOnBazzRequestResultListener(new BazzLib.BazzRequestResultListener() {
            @Override
            public boolean onRequestResult(String requestId, String requestText, String requestResult)
            {
                HandleCallbackFromLib("Message reply was "+requestResult);
                return false;
            }
        });
    }
```

In the callback you will get:

- **requestId:** the id of the message you asked to send
- **requestText:** the original text of the message you sent
- **requestResult:** the command the user selected

**Note:** return false to allow BAZZ to continue treating the user selected reply (e.g. send a text message, call the phone number you supplied, etc.), or true if you want to handle the user reply command yourself.

#Thank you for using BAZZ !
