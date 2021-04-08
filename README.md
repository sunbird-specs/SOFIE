# SOFIE v0.5
A Specification for Open Feature Integration and Extensions

Documentation is categorised into multiple sections:
* Overview
* Registration Spec for Apps to Sunbird
    * Global Registration API
* Technical Specification for third party apps
    * Action Structure
    * Integration Specification
    * Integration via Intent
    * Integration via DeepLink
* Reference Example

* Appendix
    * Android Integration
      * Intent Handling
      * DeepLink Handling
    * iOS Integration
    * Content Model Specification
    * Summary Event Specification    
* Actions Supported from Sunbird App

## Overview
Intent of this specification is to integrate with external apps for feature extension (OR) feature integration of Sunbird Application. Every feature extension or integration is performed on pre-configured actions within the sunbird app. 

Following diagram represents the workflow of a 3rd party or custom extensions app registering and interacting with the sunbird app.

![Alt Text](attachments/2016411649/2164949010.png)

**Sunbird Third Party App Interaction**

![Alt Text](attachments/2016411649/2165604363.png)

---

## Registration Spec for Apps to Sunbird

Each third party app should be registered with Sunbird for the integrations to be enabled. The purpose of registration is to verify the authenticity of the app providing integration/extension to sunbird and also allow policy-makers (of the sunbird instance) to do appropriate review and accept the integration. 

Following is the info/specificiation required for the registration. The complete API details of the registration can be found [here](https://github.com/sunbird-specs/SOFIE/blob/main/registration.md)

```javascript
{
   name: "", // Required. Name of the app. For ex: "Sunbird ReadAlong"
   logo: "", // Required. Logo of the app. This is used to show the user to select the extension app he chooses to open with.
   provider: {
      name: "", // Required. Name of the company that has built the app
      copyright: "", // Optional. Any copyright information to be shown
      license: "" // Optional. License of the app.
   },
   osType: "", // Required. OS of the App. Android/iOS
   osMetadata: { // Required. Metata of the OS
      packageId: "", // Required. A fully qualified app name matching play store app id. For ex: "com.sunbird.readalong.app"
      appVersion: "", // Required. Version of the app. This is used by sunbird to check if the app is installed locally
      urlScheme: "", // Required for iOS. The URL scheme of the app. Used for the app invocation.
      compatibilityVer: "" // Required. Compatible with which version of sunbird app.
   },
   actions: [ActionData] // Actions to register with
}
```
---

## Technical Specification for third party apps

Every app that registers with sunbird would register for the actions supported by the app. For ex: Let us assume that the sunbird app at the content level supports actions such as "open" and "share". Any extension app which registers for these actions would be shown as available options to the user when the corresponding actions are triggered. 

The technical specification is broken down into the following sections:

1. **Action Data Structure** - Structure of the action data
2. **Integration Specification** - The data specification for the integration
3. **Integration via Intent** - The data specification for the integration via intent
4. **Integration via DeepLink** - The data specification for the integration via deeplink


### Action Data Structure

Following is the structure of generalized action data for which an app can register for.

```js
{
    "action": {
        "type": "IN/OUT", // Required. Whether the action is an in-ward/out-ward actions.
        "id": "", // Required. ID of the action. For ex: Play/Share/Search
        "payload": "", // Optional. Payload of the action. JSON String
        "ctx_id": "", // Optional. Context of the action. For ex: Content Id of the action is Play
        "ctx_type": "", // Optional. Context type. For ex: Content/Assessment etc
        "subctx_id": "", // Optional. Any Sub Context? For ex: CollectionId/TextbookId/GroupId etc
        "subctx_type": "", // Optional. Sub context type. For ex: Course/Textbook/Group etc
        "extra": "" // Optional. JSON String. Any extra params/data to be passed?
    }
}
```

### Integration Specification

Following is the specification of the integration, the data to be passed from one app to another

```js
{
    "packageId": "", // Required. Package details of referrer app
    "referenceID": "", // Required. Reference ID to be sent back to sunbird app
    "authKey": "", // Optional. Authorization Key for referrer app
    "data": "" // JSON string of ActionData
}
```

### Integration via Intent

Following is how the specification is transformed when integrated via intent.

```javascript

Intent launchIntent = getPackageManager().getLaunchIntentForPackage("<packageID>");
intent.putExtra("<key>","<any-serializable-object>");
start
```

Examples
```js
Intent launchIntent = getPackageManager().getLaunchIntentForPackage("org.sunbird.app");
intent.putExtra("data",{"type": 'ACTION_PLAY',
                        "payload":{
                            "request":{
                                "objectId":"do_id",
                                "collectionId": "do_id" 
                            }
                        }
                    });
```

### Integration via DeepLink
Following is how the specification is transformed when integrated via deep link.

```javascript
String url = "<url>";
Intent launchIntent = new Intent(Intent.ACTION_VIEW);
launchIntent.setData(Uri.parse(url)); 
startActivity(launchIntent);
```

Examples
```js
String url = "https://staging.sunbirded.org/play/collection/do_213218179972710400129?contentId=do_3127411394178826241699";
Intent launchIntent = new Intent(Intent.ACTION_VIEW);
launchIntent.setData(Uri.parse(url)); 
startActivity(launchIntent);
```

## Reference Example

> TODO: TBA

## Appendix

### Android Integration

#### Intent Handling

Need to create an intent filter in Android Manifest as follows 
```xml
<activity
    android:name="com.example.ExampleActivity"
    android:label="@string/title_example" >
    <intent-filter android:label="@string/play_view_web_example">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with "http://sunbird.staginged.in/play” -->
        <data android:scheme="https"
              android:host="sunbird.staginged.in"
              android:pathPrefix="/play" />
    </intent-filter>
    <intent-filter android:label="@string/play_view_app_example">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with "example://play” -->
        <data android:scheme="example"
              android:host="play" />
    </intent-filter>
</activity>
```
* Implement Activity as follows
```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);

    Intent intent = getIntent();
    String action = intent.getAction();
    Uri data = intent.getData();
    /** Trigger the Business Logic of App */
}
```

#### DeepLink Handling

> TODO: TBA

### iOS Integration

> TODO: TBA

### Content Model Specification
<details>
<summary>Expand API Documentation</summary>
The third party apps should do a HTTP GET call on the contentUrl parameter sent via the intent data.

```
GET intentdata.contentUrl

Headers {
  X-App-ID: <ID of the App>,
  X-App-Version: <Version of the App>,
  X-Device-ID: <Device Id>
}
```

```javascript
{
   id: "api.content.read",
   ver: "v1",
   ts: "2021-02-10 04:27:14:674+0000",
   params: {
      msgid: "90e184d1-c52c-106b-c410-6a168900ef05",
      err: "",
      status: "success",
      errmsg: ""
   },
   responseCode: "OK",
   result: {
      content: {} // Refer to the content model schema defined below
   }
}
```
For More Details on the content model refer Sunbird Documentation.
* content - https://github.com/sunbird-specs/LearningObjectModel/blob/main/v1/schemas/content/1.0/schema.json
* collection - https://github.com/sunbird-specs/LearningObjectModel/blob/main/v1/schemas/collection/1.0/schema.json
</details>

### Summary Event Specification

All 3rd party apps are required to at-least send back a summary of the content play session as per the sunbird telemetry spec. In addition the 3rd party apps can send any event as per [Sunbird Telemetry Spec](https://github.com/sunbird-specs/Telemetry/blob/main/specification.md)
<details>
<summary>Expand Summary Event</summary>
https://github.com/sunbird-specs/Telemetry/blob/main/v3_event_details.md/#summary

```javascript
{
  "edata": {
    "type": String, // Required. Type of summary. Free text. "session", "app", "tool" etc
    "mode": String, // Optional.
    "starttime": Long, // Required. Epoch Timestamp of app start. Retrieved from first event.
    "endtime": Long, // Required. Epoch Timestamp of app end. Retrieved from last event.
    "timespent": Double, // Required. Total time spent by visitor on app in seconds excluding idle time.
    "pageviews": Long, // Required. Total page views per session(count of CP_IMPRESSION)
    "interactions": Long, // Required. Count of interact events
    "envsummary": [{ // Optional
        "env": String, // High level env within the app (content, domain, resources, community)
        "timespent": Double, // Time spent per env
        "visits": Long // count of times the environment has been visited
    }],
    "eventssummary": [{ // Optional
        "id": String, // event id such as CE_START, CE_END, CP_INTERACT etc.
        "count": Long // Count of events.
    }],
    "pagesummary": [{ // Optional
        "id": String, // Page id
        "type": String, // type of page - view/edit
        "env": String, // env of page
        "timespent": Double, // Time taken per page
        "visits": Long // Number of times each page was visited
    }],
    "extra": [{ // Optional. Additional summary data specific to mime type or app. For ex: wordsPerMin
        "id": String, // Required. Key for the extra data
        "value": String // Required. Value for the extra data
    }]
  }
}
```
</details>

## Actions Supported from Sunbird App

Following Intents are broadly supported as part of Sunbird App

<details>  
    <summary>Expand Search Intent</summary>

```javascript
    package: 'org.sunbird.app',
    action: 'android.intent.action.VIEW',
    extras: {
         type: 'ACTION_SEARCH',
         payload: {
             request: {
                 {"filters":
                    {
                        "se_boards":["CBSE"],
                        "se_mediums":["English"],
                        "se_gradeLevels":["Class 10"],
                        "subject":[],
                        "audience":[],
                        "primaryCategory":["Digital Textbook"]},
                        "fields":[
                            "name","appIcon","mimeType","gradeLevel","identifier","medium","pkgVersion","board","subject","resourceType","primaryCategory","contentType","channel","organisation","trackable","se_boards","se_subjects","se_mediums","se_gradeLevels"],
                        "facets":["subject"]}
             }
         }
    }
```
</details>


<details>  
    <summary>
    Expand Play Intent
    </summary>

```javascript
    package: 'org.sunbird.app',
    action: 'android.intent.action.VIEW',
    extras: {
         type: 'ACTION_PLAY',
         payload: {
             request: {
                 "objectId": "do_id", // Can be Content ID/Question Set ID/Event Set ID/                     // Action Set ID
                 "collection": "do_id"
             }
         }
    }
```
</details>

<details>  
    <summary>
    Expand Set Profile Intent
    </summary>

```javascript
    package: 'org.sunbird.app',
    action: 'android.intent.action.VIEW',
    extras: {
         type: 'ACTION_SETPROFILE',
         payload: {
             request: {
                 {
                    "board":["CBSE"],
                    "medium":["English"],
                    "gradeLevel":["Class 10"]
                }
            }
         }
    }   
```
</details>

<details>  
    <summary>
    Expand Goto Intent
    </summary>
    
```javascript
    package: 'org.sunbird.app',
    action: 'android.intent.action.VIEW',
    extras: {
         type: 'ACTION_GOTO',
         payload: {
             request: {
                 "route": "routeUrl", 
                 "params": {
                     ....     // Can be optional
                 }
             }
         }
    }
```
</details>

<details>  
    <summary>
    Expand Deeplink Intent
    </summary>

```javascript
    package: 'org.sunbird.app',
    action: 'android.intent.action.VIEW',
    extras: {
         type: 'ACTION_DEEPLINK',
         payload: {
             request: {
                 "url": "routeUrl"
             }
         }
    }
```
</details>

