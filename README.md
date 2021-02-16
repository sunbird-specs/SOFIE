# SOFIE
Specification for Open Feature Integration and Extensions

Documentation is categorised into multiple sections:
* WorkFlow
* Vendor Registration of Apps to Sunbird
    * Global Vendor Registration API
* Technical Specification for third party apps
    * Param Data Supported
    * Summary Event Spec
    * API Documentation
    * Intent Handling


## WorkFlow

### Registration of External Apps
| |
|![Alt Text](attachments/2016411649/2164949010.png)|```{identifier:"",name:"",logo:"",appName:"",packageId:"",target:{mimeType:[],contentType:[],....//Allcontentattributes},appDetails:{organization:""}}```|


### Sunbird Third Party App Interaction
| |
|![Alt Text](attachments/2016411649/2165604363.png)|```{referrerPackageId:"com.google.bolo",referenceID:"App generated Code",mimeType:"",vendorCode:"",contentUrl:"",profileContext:"{handle:'',avatar:''}"}```|


## Vendor Registration of Apps to Sunbird
### Global Vendor Registration API
## Technical Specification for third party apps
### Param Data Supported
<details>
<summary>Expand Param Data</summary>

| Params | Description | Data Type|
| -- | -- | -- |
| referrerPackageId | Package details of referrer app | String |
| referenceID | Reference ID to be sent back to sunbird app | String |
| mimeType | Content MimeType | String |
| authKey | Authorization Key for referrer app | String |
| contentUrl | Url of the content | String |
| profileContext | Name and Avatar in JSONified String | String |
</details>

### Summary Event Spec
<details>
<summary>Expand Summary Event</summary>
https://github.com/sunbird-specs/Telemetry/blob/main/v3_event_details.md/#summary

```
{
  "edata": {
    "type": "", // Required. Type of summary. Free text. "session", "app", "tool" etc
    "mode": "", // Optional.
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
    }]
  }
}
```
</details>

### API Documentation
<details>
<summary>Expand API Documentation</summary>

</details>

### Intent Handling

* Need to create an intent filter in Android Manifest as follows 
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