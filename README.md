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
  * [BAZZ channels](#bazz-channels)
  * [Incoming messages](#incoming-messages)
  * [Outgoing messages](#outgoing-messages)
  * [Configuring operation](#configuring-operation)
    * [Whatsapp group messages](#whatsapp_group)
    * [Internal setting screens](#internal-setting-screens)
    * [More settings](#more-playback)
  * [Generate your own messages(!)](#generate-your-own-messages)

<!-- toc stop -->

# Prerequisites

* BAZZ-Bridge SDK works on **Android** devices with OS version **4.0.1** and up.
* BAZZ-Bridge SDK requires a connected android device (i.e. **data** or **WiFi** connection) to work.
* You will need a developer account with BAZZ. Please [Contact Us](mailto:support@bazz.co) for details.

# Installation

Clone or download BAZZ-Bridge-SDK from here, and extract it on your computer.
In it you will find a demo application for how the BAZZ-Bridge SDK can be used.

You can import this project to Android Studio, and use it as a reference
or base to make your alterations.

If you already have an existing project, or want to create a new one,
The BAZZ-Bridge SDK comes in the form of **bazz_bridge-debug.aar**. You should receive it from us, along with
the App ID.

To add it to your project, follow the steps below:

1. With your project open in Android Studio, select '**File => New => New Module**'
2. In the dialog opened, select '**Import .JAR/.AAR Package**', and click '**Next**'
3. In the '**File name**' line - browse to the '**bazz_bridge-debug**' folder of the demo project,
   and select the **aar** file. Click on '**Finish**'
4. After Gradle Sync is done, go to your project settings: right-click on the project module, and select '**Open Module Settings**'.
5. Select your **app** module in the left pane, and click the '**Dependencies**' tab. Click the **plus (+)** sign, and select '**Module dependency**'. Select '**:bazz_bridge-debug**', and click '**OK**' twice.

Done. Now you can start integrating the BAZZ-Bridge SDK as explained in the next sections.

# Usage

## Preparing your app

**IMPORTANT:** set your app '**minSdkVersion**' to **16**, and '**targetSdkVersion**' to **19**

### Adding classes

- Add a new java file: '**BazzNotificationServiceLink.java**':

```java
package <your app project package>

import com.esoof.android.bazz_bridge.BroadcastReceiver_Notifications;

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
    
    private static final String APP_ID = "<Your BAZZ-Bridge SDK app id here>";
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

            // These calls enable treatment of the IM messaging apps
            mBazzLib.setIncomingWorkWithSMSorMMS(true);
            mBazzLib.setIncomingWorkWithWhatsapp(true);

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


## BAZZ operation modes

BAZZ has 3 operation modes:

* **Off**:   BAZZ is idle

* **Phone**: BAZZ is ON, and works as driver's phone - monitoring text messages, and waiting for commands to send SMS, make calls, or send WhatsApp messages

* **Car**:   BAZZ is ON, and works on car multimedia system - reading incoming messages from phone, and handling them

By default BAZZ is in '**Off**' mode.
To change modes use the following calls:

```java
    if (MyApplication.mBazzLib!=null)
    {
        MyApplication.mBazzLib.setBazzMode(BazzLib.BAZZ_MODE_OFF);
        
        MyApplication.mBazzLib.setBazzMode(BazzLib.BAZZ_MODE_PHONE);

        MyApplication.mBazzLib.setBazzMode(BazzLib.BAZZ_MODE_CAR);
    }
```


To read the current mode call:

```java
    if (MyApplication.mBazzLib!=null)
    {
        int currMode = MyApplication.mBazzLib.getBazzMode();
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


## BAZZ channels

BAZZ has 3 channels:

* **Cable**:      Car multimedia system and driver's phone are connected via a USB cable

* **WiFi**:       Car multimedia system and driver's phone are connected via WiFi (Phone enables WiFi Hotspot)

* **Bluewtooth**: Car multimedia system and driver's phone are connected via Bluetooth

The SDK continuously scans the 3 channels and tries to detect connection in each. When detected, it establishes
a channel for communications based on priority: Cable -> WiFi - >Bluetooth

To change modes use the following calls:

To read the current channel state:

```java
    if (MyApplication.mBazzLib!=null)
    {
        int currCableState     = MyApplication.mBazzLib.getCableState();
        
        int currWiFiState      = MyApplication.mBazzLib.getWiFiState();
        
        int currBluetoothState = MyApplication.mBazzLib.getBluetoothState();
    }
```

possible values are:
```java
    BazzLib.CHANNEL_STATE_DISABLED (channel unavailable, e.g. cable disconnected, WiFi disables, Bluetooth disabled...)
    BazzLib.CHANNEL_STATE_NOT_CONNECTED (channel available, but not device found on other side)
    BazzLib.CHANNEL_STATE_CONNECTED (channel available, and connected to device on other side)
```


You can also listen to changes in channels state:

```java
    MyApplication.mBazzLib.setOnBazzChannelChangedListener(new BazzLib.BazzChannelChangedListener() {
        @Override
        public void onBazzChannelChanged(String channelType, int channelState, String channelName) {
            // MYTODO: do somthing here cause channel state has changed...
            // channelType is one of: BazzLib.CHANNEL_TYPE_CABLE, BazzLib.CHANNEL_TYPE_WIFI, BazzLib.CHANNEL_TYPE_BLUETOOTH
            // channelState: BazzLib.CHANNEL_STATE_DISABLED, BazzLib.CHANNEL_STATE_NOT_CONNECTED, BazzLib.CHANNEL_STATE_CONNECTED
            // channelName: the name or address of a channel
        }
    });
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



## Outgoing Messages

From the car multimedia system side, you can initiate the following actions on the driver's phone side:

Send a text (SMS) message to a recipient:

```java
    if (MyApplication.mBazzLib != null)
    {
        String reqID = MyApplication.mBazzLib.sendSMS(message,phone);
    }
```

Make a call to a recipient:

```java
    if (MyApplication.mBazzLib != null)
    {
        String reqID = MyApplication.mBazzLib.makeCall(phone);
    }
```

Send a WhatsApp message to a recipient:

```java
    if (MyApplication.mBazzLib != null)
    {
    	// if group has a value - the message will be sent to that group
    	// if name has a value - message will be sent to the contact in that name
    	// if phone has a value - message will be sent to a contact with that number
        String reqID = MyApplication.mBazzLib.sendWhatsApp(message,phone,name,groups);
    }
```





## Configuring operation

### Whatsapp group messages

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
