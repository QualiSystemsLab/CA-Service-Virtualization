In some modern web apps, features are implemented as a REST API backend first, then exposed through the GUI. This way the REST API is always feature-complete.

Suppose the backend of an app calls other services. For example an in-house order tracking system might upload certain information into SalesForce using the SalesForce API. While testing this system, it might be useful to replace the SalesForce endpoint with a dummy that replies in the correct format. 

CA Service Virtualization is an installable product that can make a recording of the app's interaction with the real API, then use the recording to serve a dummy service. The dummy service request-response pairs can also be defined manually.

CA Service Virtualization has evaluation versions available on Azure and AWS, with a trial license that has to be applied for.

We created a simple example app consisting of a sales lead entry form. It adds the name, company, and phone number to its own in-memory database. It also automatically creates a new lead in SalesForce with only the name and company. The webpage at /leads.html displays the latest contents of the in-memory database using AJAX calls to the REST API. There are APIs to create a lead and list all leads. The URL of the SalesForce API is an input to the program. It can either be https://login.salesforce.com or a dummy served from the CA Service Virtualization machine.

Instead of deploying the app here, we assume it is launched manually ahead of time. You can launch an instance of the app backed by the real SalesForce side by side with one backed by the virtualized SalesForce. For example the real one could be :5001 and the virtualized one could be on :5002.

To show the app working, go to http://(server ip):5001/leads.html or http://(server ip):5001/leads.html and create some leads. You should see activity in SalesForce (5001) or the virtual service (5002). Be sure to start the virtual service first. 

In CloudShell there are functions to turn the dummy service on and off using the CA Service Virtualization REST API. The service still must be created manually ahead of time. The functions are at the resource level and also at the sandbox level through a simple wrapper function that calls the resource function. 

![](screenshots/virtual%20salesforce%20auth.png)
![](screenshots/virtual%20salesforce%20auth%202.png)
Full auth response body:
{"access_token":"000000000000000000000000000000000000000000000000000000000000000000000000000000000000000","instance_url":"http://13.65.207.39:8001","token_type":"Bearer","issued_at":"1492885274097","signature":"xxxxxxxxxxxxxxxxxxxxxxxxxxx=="}

where 13.65.207.39 is the IP of the CA Service Virtualization installation.

![](screenshots/virtual%20salesforce%20lead.png)
![](screenshots/virtual%20salesforce%20lead%202.png)

Full lead creation response body:
{"id":"virtualized-salesforce-000000000000"}

