# BAZZ-Bridge-SDK
SDK to allow 2-way communications between an Android phone and an Android Car Multimedia system

<!-- toc -->

* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Usage](#usage)
  * [Preparing your app](#preparing-your-app)
    * [Gradle](#gradle)
    * [Adding classes](#adding-classes)
    * [Manifest](#manifest)
  * [Initializing the SDK](#initializing)
  * [BAZZ operation modes](#bazz-operation-modes)
  * [BAZZ channels](#bazz-channels)
  * [Bidirectional messages](#bidirectional-messages)
  * [Incoming messages](#incoming-messages)
  * [Outgoing messages](#outgoing-messages)
  * [Configuring operation](#configuring-operation)
    * [Treating incoming messages](#treating-incoming-messages)
    * [Handling Whatsapp group messages](#handling-whatsapp-group-messages)
    * [Whatsapp groups ignore list](#whatsapp-groups-ignore-list)
    * [Handling incoming calls](#handling-incoming-calls)
    * [Blocking unknown senders](#blocking-unknown-senders)
    * [Bluetooth ignore list](#bluetooth-ignore-list)

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
The BAZZ-Bridge SDK comes in the form of **bazz_bridge_sdk.aar**. You should receive it from us, along with
the App ID.

You can also download it from here: https://dl.dropboxusercontent.com/s/vijxkoh6xf9w2r7/bazz_bridge_sdk.aar

To add it to your project, follow the steps below:

1. With your project open in Android Studio, select '**File => New => New Module**'
2. In the dialog opened, select '**Import .JAR/.AAR Package**', and click '**Next**'
3. In the '**File name**' line - browse to the '**bazz_bridge_sdk**' folder of the demo project, or to where you saved the file from the link above, and select the **aar** file. Click on '**Finish**'
4. After Gradle Sync is done, go to your project settings: right-click on the project module, and select '**Open Module Settings**'.
5. Select your **app** module in the left pane, and click the '**Dependencies**' tab. Click the **plus (+)** sign, and select '**Module dependency**'. Select '**:bazz_bridge_sdk**', and click '**OK**' twice.

Done. Now you can start integrating the BAZZ-Bridge SDK as explained in the next sections.

# Usage

## Preparing your app

**IMPORTANT:** set your app '**minSdkVersion**' to **16**, and '**targetSdkVersion**' to **19**


### Gradle

- In your app **build.gradle**, make sure you have **minSdkVersion 16** and **targetSdkVersion 19**.

- In your app **build.gradle**, make sure you have **volley** included. If you do not already have it - add the line:
```java
dependencies {
	...
	compile 'com.android.volley:volley:1.0.0'
	...
	}
```



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
            // Use BazzLib.SDK_MODE_CAR if you are building the app for the CAR multimedia system
            // Use BazzLib.SDK_MODE_PHONE if you are building the app for the PHONE multimedia system
            mBazzLib.init(this,<BazzLib.SDK_MODE_CAR or BazzLib.SDK_MODE_PHONE>,APP_ID,"<Your app name>");
            
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


You can also listen to changes in BAZZ mode:

```java
	// *** This callback works on both Phone and Car sides ***
    MyApplication.mBazzLib.setOnBazzModeChangedListener(new BazzLib.BazzModeChangedListener() {
        @Override
        public void onBazzModeChanged() {
            // MYTODO: do somthing here cause BAZZ mode has changed...
        }
    });
```

**NOTE:**
Once you call this with **BazzLib.BAZZ_MODE_PHONE** on the phone device, and with **BazzLib.BAZZ_MODE_CAR** on the car multimedia system, the two devices start trying to connect with each other over the 3 possible channels (see next section). Once they get connected on one or more (you can be notified on this with the callbacks described below) - the phone side will send notifications on incoming messages or calls to the car multimedia system, and the car multimedia system can instruct the phone device to send messages or make calls.



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
	// *** This callback works on both Phone and Car sides ***
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




## Bidirectional Messages

Once channel/s of communications are established, both sides (**Phone** and **Car**) can exchange string messages between them.

To send a string message call:

```java
    if (MyApplication.mBazzLib != null)
    {
        String reqID = MyApplication.mBazzLib.sendString(message);
    }
```


To be notified of incoming messages in your app, use:

```java
	// *** This callback works on both sides ***
    MyApplication.mBazzLib.setOnIncomingStringsListener(new BazzLib.BazzIncomingStringsListener() {
        @Override
        public boolean onIncomingString(String text)
        {
            // handle the text here...
        
            return false;
        }
    });
```




## Incoming Messages

BAZZ bridge on the **Phone** side can intercept incoming messages from various sources, such as SMS/TEXT, Whatsapp, calls, etc. and sends them over the communication channels to the **Car** side. The app on the **Car** side can them play them and allow the user to select a reply option. That selection is sent back to the **Phone** side for taking action (see the next section).

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
	// *** This callback works on the Car side only ***
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



## Outgoing Messages

From the **car multimedia** system side, you can initiate the following actions on the driver's phone side:

1. Send a text (SMS) message to a recipient:

```java
    if (MyApplication.mBazzLib != null)
    {
        String reqID = MyApplication.mBazzLib.sendSMS(message,phone);
    }
```

The Bridge library on the **Phone** side receives this command and sends a text (SMS) message.



2. Make a call to a recipient:

```java
    if (MyApplication.mBazzLib != null)
    {
        String reqID = MyApplication.mBazzLib.makeCall(phone);
    }
```

The Bridge library on the **Phone** side receives this command and initiates a call.



3. Send a WhatsApp message to a recipient:

```java
    if (MyApplication.mBazzLib != null)
    {
        String reqID = MyApplication.mBazzLib.sendWhatsApp(message,phone,name,groups);
    }
```

The Bridge library on the **Phone** side receives this command and triggers a callback to the **APK** module you have on the **Phone** side - it can then send a **WhatsApp** message using INPRIS SDK.


**Important:**
In the case of sending outgoing WhatsApp messages, you will have to implement the sending action in your app on the phone side. To do this, add a listener to be notifed of the request to send a WhatsApp message:

```java
	// *** This callback works on the Phone side only ***
    MyApplication.mBazzLib.setOnWhatsAppSendMessageRequestListener(new BazzLib.BazzWhatsAppSendMessageRequestListener() {
        @Override
        public boolean onWhatsAppSendMessageReques(String type,
                                                  String phone,
                                                  String name,
                                                  String text)
        {
            // handle the messages here...
        
            return true;
        }
    });
```




## Configuring operation

Here are some functions you can use to display screens for misc. settings of BAZZ Bridge operation:


![Alt text](https://dl.dropboxusercontent.com/s/q64qms3nql2u7nj/bazz_bridge_settings_screen.png "Configuration screens example")




### Treating incoming messages

BAZZ bridge on the **Phone** side can intercept incoming messages from various sources, such as SMS/TEXT, Whatsapp, calls, etc. and sends them over the communication channels to the **Car** side. The app on the **Car** side can them play them and allow the user to select a reply option. That selection is sent back to the **Phone** side for taking action (see the next section).

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



### Handling Whatsapp group messages

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

User may not want BAZZ to read ALL his/her Whatsapp groups (some of them can be really noisey...). You can manage an '**Ignore List**' with the following functions.

To get a list of all Whatsapp groups known to BAZZ Bridge (only those who were already treated by it)

```java
    if (MyApplication.mBazzLib!=null)
    {
    	ArrayList<WhatsAppGroupHistoryRec> trgArray = new ArrayList<WhatsAppGroupHistoryRec>();
    
        MyApplication.mBazzLib.getWhatsappGroups(trgArray);
    }
```

You pass an ArrayList and the function fills it up for you. Each item in the ArrayList is of type **WhatsAppGroupHistoryRec**:

```java
public class WhatsAppGroupHistoryRec {

	public String name;
	public int    blocked;
	public long   rowId;
}
```

You can display a list of the names, and allow the user to check/uncheck groups he/she wants to ignore by settings the **blocked** field, and saving:

```java
    if (MyApplication.mBazzLib!=null)
    {
    	//...
    	
    	trgArray.get(i).blocked = 1 or 0;
    	
    	//...
    
        MyApplication.mBazzLib.setWhatsappGroups(trgArray);
    }
```




### Handling incoming calls

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

### Blocking unknown senders

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


### Bluetooth ignore list

To detect connection on the **Bluetooth channel**, BAZZ Bridge scans all paired devices. User may want to ignore some devices, so that this scan does not waste time and battery.

To get a list of all paired Bluetooth devices:

```java
    if (MyApplication.mBazzLib!=null)
    {
    	ArrayList<BluetoothDeviceIgnoreRec> trgArray = new ArrayList<BluetoothDeviceIgnoreRec>();
    
        MyApplication.mBazzLib.getBluetoothIgnoreList(trgArray);
    }
```

You pass an ArrayList and the function fills it up for you. Each item in the ArrayList is of type **BluetoothDeviceIgnoreRec**:

```java
public class BluetoothDeviceIgnoreRec {

    public String  name;
    public boolean ignore;
}
```

You can display a list of the names, and allow the user to check/uncheck a device he/she wants to ignore by settings the **ignore** field, and saving:

```java
    if (MyApplication.mBazzLib!=null)
    {
    	//...
    	
    	trgArray.get(i).ignore = true or false;
    	
    	//...
    
        MyApplication.mBazzLib.setBluetoothIgnoreList(trgArray);
    }
```





# Thank you for using BAZZ Bridge !
