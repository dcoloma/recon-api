**INTRO**
----

***What is Recon***

ReCon is a project run by Northeastern University intended to detect Personal Information leaks in Mobile Applications. It consists in a Machine Learning system that analyses the traffic that goes between mobile devices and the network and detects potential information that is leaked. That information is aggregated so that is possible to offer information about the insights discovered thanks to the analysis of all the PI leaks discovered in the user base of the tool. So far we have built an API on top of that aggregated data that let developers get information about which apps are leaking information (and which type of information) and which domains are receiving it.

***How is the API built***

This is API is built using Firebase, this means that all the data is available in a Firebase tree structure and can be queried by either requesting the top-level branches (to get the whole set of information) or sub-branches to get following the tree branches. The responses are always JSON files that describe the tree structure corresponding to the URL that has been queried (e.g. the endpoint that has been requested)

* ***Firebase*** 

Firebase is a service that let developers store information in tree structure that can be easily retrieved by developers, especially in Web environments, as the whole structure or sub-structures can be retrieved as json objects that can be easily manipulated. This means that by storing all the information in Firebase we get automatically an API to fetch data from it.

* ***The root endpoint***

The information is stored in a tree, that can be visualized at the Root URL of the Firebase. The URL of the Recon endpoint is: https://recon.firebaseio.com/.

Sub-URLs can be also used to show only part of the tree in a browser (e.g. https://recon.firebaseio.com/apps) or to get directly the JSON representation of that part of the tree (e.g. https://recon.firebaseio.com/apps.json)

* ***Return format***

The API will return the structure of the tree serialized as a JSON object. 

* ***Passing Parameters***

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
  
**Show Application Leakiness Score**
----
  Show Application Leakiness Score
  This method fetches the list of applications analyzed by ReCon and  and plurality (one vs multiple)._>

* **URL**

  <_The URL Structure (path only, no root url)_>

* **Method:**
  
  GET


  
*  **URL Params**

   <_If URL params exist, specify them in accordance with name mentioned in URL section. Separate into optional and required. Document data constraints._> 

   **Required:**
 
   `id=[integer]`

   **Optional:**
 
   `photo_id=[alphanumeric]`

* **Data Params**

  <_If making a post request, what should the body payload look like? URL Params rules apply here too._>

* **Success Response:**
  
  <_What should the status code be on success and is there any returned data? This is useful when people need to to know what their callbacks should expect!_>

  * **Code:** 200 <br />
    **Content:** `{ id : 12 }`
 
* **Error Response:**

  <_Most endpoints will have many ways they can fail. From unauthorized access, to wrongful parameters etc. All of those should be liste d here. It might seem repetitive, but it helps prevent assumptions from being made where they should be._>

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 422 UNPROCESSABLE ENTRY <br />
    **Content:** `{ error : "Email Invalid" }`

* **Sample Call:**

  <_Just a sample call to your endpoint in a runnable format ($.ajax call or a curl request) - this makes life easier and more predictable._> 

* **Notes:**

  <_This is where all uncertainties, commentary, discussion etc. can go. I recommend timestamping and identifying oneself when leaving comments here._> 
