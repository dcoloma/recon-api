# RECON API
##INTRO

###What is Recon?

ReCon is a project run by Northeastern University intended to detect Personal Information leaks in Mobile Applications. It consists in a Machine Learning system that analyses the traffic that goes between mobile devices and the network and detects potential information that is leaked. That information is aggregated so that is possible to offer information about the insights discovered thanks to the analysis of all the PI leaks discovered in the user base of the tool. So far we have built an API on top of that aggregated data that let developers get information about which apps are leaking information (and which type of information) and which domains are receiving it.

###How is the API built

This is API is built using Firebase, this means that all the data is available in a Firebase tree structure and can be queried by either requesting the top-level branches (to get the whole set of information) or sub-branches to get following the tree branches. The responses are always JSON files that describe the tree structure corresponding to the URL that has been queried (e.g. the endpoint that has been requested)

####Firebase

Firebase is a service that let developers store information in tree structure that can be easily retrieved by developers, especially in Web environments, as the whole structure or sub-structures can be retrieved as json objects that can be easily manipulated. This means that by storing all the information in Firebase we get automatically an API to fetch data from it.

####Firebase SDK

You can query data hosted in the Firebase either by directly requesting the endpoint URLs as explained below or using any of the <a href="https://www.firebase.com/docs/"> Firebase SDKs: </a> Web, iOS, Android or REST.

####The root endpoint

The information is stored in a tree, that can be visualized at the Root URL of the Firebase. The URL of the Recon endpoint is: https://recon.firebaseio.com/.

Sub-URLs can be also used to show only part of the tree in a browser (e.g. https://recon.firebaseio.com/apps) or to get directly the JSON representation of that part of the tree (e.g. https://recon.firebaseio.com/apps.json)

####Return format

The API will return the structure of the tree serialized as a JSON object. 

####Passing Parameters

Additional parameters can be passed by adding them to the root URL endpoint

Some examples:

* Getting the whole list of applications

  In this case we just need to append the URL of the branch that contains all the apps (it's important to use the .json prefix to get the json representation and not the graphical one):
  
  https://recon.firebaseio.com/apps.json
  
* Getting the list of applications for android

  In this case we also append the android string to determine that we only want to receive the tree structure that holds information about android applications:

  https://recon.firebaseio.com/apps/android.json

* Getting the information about a particular application

  If we just want to know the information about one app, what we can do is just using the endpoint where the information of that application is stored:
  
  https://recon.firebaseio.com/apps/android/Clean%20Master.json
  
##RECON API SPECIFICATION
  
**Show Application Leakiness**
----
  This method fetches the leakiness information about the application or list of applications that match the selected criteria.

* **URL**

 - apps.json   (To retrieve the information about all the apps)
 - apps/<_platform_>.json (To retrieve the information about all the apps for a given platform)
 - apps/<_platform_>/<_appname_>.json (To retrieve the information about a particular app for a specific platform)

   Where <_platform_> could be either android, windows, ios
   Where <_appname_> is the application name as registered in the Apple Store, Google Play, Windows Marketplace

* **Method:**
  
  GET

* **Success Response:**
  
  * **Code:** 200
  * **Content:** `{"nonTrackerCategories":{"AndroidID":{"url1":"ksmobile.com"}},"nonTrackers":true,"popularity":44,"trackerCategories":{"AdvertiserID":{"url1":"adkmob.com"},"AndroidID":{"url1":"adkmob.com"}},"trackers":true}`
  
  The content is JSON that shows the information for all the apps that match the category. For every app the following information is returned:

   * *popularity*: Popularity of the App in the corresponding Application Store
   * *score*: Leakiness score of the app. TBD: How is it calculated.
   * *trackers*: Whether the app is sending Personal Information to domains categorised as trackers or not.
   * *trackerCategories*: An array that contains all the information categories that are leaked to tracker domains. For every category, the list of receiving domains are offered, for instance: <code>{"trackerCategories":{"AdvertiserID":{"url1":"adkmob.com"},"AndroidID":{"url1":"adkmob.com"}}</code>
   * *nonTrackers*: Whether the app is sending Personal Information to domains categorised as trackers or not.
   * *nonTrackerCategories*: An array that contains all the information categories that are leaked to non tracker domains. For every category, the list of receiving domains are offered, for instance: <code>{"nonTrackerCategories":{"AndroidID":{"url1":"ksmobile.com"}}</code>
   


  
 
* **Error Response:**

  If no data is found for that application, the API will return a null value as reponse.

* **Sample Call:**

  * **Pure JavaScript via JSONP - NO SDK**

```javascript
<head>
<script>
  function gotData(data) {
    console.log(data); // contains the JSON result
  }
  function retrieveReconData() {
    var scriptE = document.createElement('script');
    // Set the source of the script element to the JSONP endpoint
    scriptE.src = 'https://recon.firebaseio.com/apps.json?callback=gotData';
    // append the script element to the page <head>
    document.getElementsByTagName('head')[0].appendChild(scriptE);
  }
</script>
</head>
<body onload="retrieveReconData()">
</body>
```
 
  * **JavaScript using Firebase Web SDK**

```javascript
<head>
  <script src="https://cdn.firebase.com/js/client/2.4.2/firebase.js"></script>
  <script>
    var myFirebaseRef = new Firebase("https://recon.firebaseio.com/");

    function retrieveReconData() {
      myFirebaseRef.once("value", function(snap) {
        console.log("read data!");
        console.log(snap.val());
      });
    }
  </script>
</head>
<body onload="retrieveReconData()">
</body>
```

* **Notes:**
  TBD
  
  
**Show Domain Leakiness**
----
  This method fetches the leakiness information about a particular domain.

* **URL**

 - domains/<_domainurl_>.json   (To avoid issues with encoding, the domainurl will be encoded in base16)
 
 For instance: 
 
 https://recon.firebaseio.com/domains/61646b6d6f622e636f6d.json
 
 (where 61646b6d6f622e636f6d is the domain adkmob.com encoded in base16.

* **Method:**
  
  GET

* **Success Response:**
  
  * **Code:** 200
  * **Content:** `{"apps":{"android":{"Clean Master":{"category1":"AndroidID","category2":"AdvertiserID"}}},"categories":{"AdvertiserID":{"app1":"Clean Master"},"AndroidID":{"app1":"Clean Master"}},"tracker":true,"url":"adkmob.com"}`
  
  The content is JSON that shows the information for the Personal Information the domain receives from mobile apps:

   * *tracker*: Whether the domain has been categorised as traker or not
   * *url*: Real URL of the domain.
   * *categories*: "AdvertiserID":{"app1":"Clean Master"},"AndroidID":{"app1":"Clean Master"}}
   * *apps*: An array that contains all the information categories that are leaked to tracker domains. For every category, the list of receiving domains are offered, for instance: <code>"apps":{"android":{"Clean Master":{"category1":"AndroidID","category2":"AdvertiserID"}}}</code>
 
* **Error Response:**

  If no data is found for that domain, the API will return a null value as reponse.

* **Sample Call:**

  * **Pure JavaScript via JSONP - NO SDK**

```javascript
<head>
<script>
  function gotData(data) {
    console.log(data); // contains the JSON result
  }
  function retrieveReconData() {
    var scriptE = document.createElement('script');
    // Set the source of the script element to the JSONP endpoint
    scriptE.src = 'https://recon.firebaseio.com/domains/61646b6d6f622e636f6d.json?callback=gotData';
    // append the script element to the page <head>
    document.getElementsByTagName('head')[0].appendChild(scriptE);
  }
</script>
</head>
<body onload="retrieveReconData()">
</body>
```
 
  * **JavaScript using Firebase Web SDK**

```javascript
<head>
  <script src="https://cdn.firebase.com/js/client/2.4.2/firebase.js"></script>
  <script>
    var myFirebaseRef = new Firebase("https://recon.firebaseio.com/");

    function retrieveReconData() {
      myFirebaseRef.once("value", function(snap) {
        console.log("read data!");
        console.log(snap.val());
      });
    }
  </script>
</head>
<body onload="retrieveReconData()">
</body>
```

* **Notes:**  
  
  
  
