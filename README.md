In some modern web apps, features are implemented as a REST API backend first, then exposed through the GUI. 
This way the REST API is always feature-complete. In this situation, both the GUI and the API itself must be tested.

Suppose the backend of an app calls other services. For example an in-house order tracking system might upload 
certain information into SalesForce using the SalesForce API. While testing the GUI or API of this system, 
it might be useful to replace the SalesForce endpoint with a dummy that replies in the correct format. Then the testing 
does not depend on a complex SalesForce setup being live at all times. 

CA Service Virtualization is an installable product that can make a recording of the app's interaction with the 
real API, then use the recording to implement a dummy service. The dummy service request-response pairs can also be 
defined manually.

CA Service Virtualization has evaluation versions available on Azure and AWS, with a trial license that has to be applied for.

In this package we provide CloudShell functions to turn the dummy service on and off using the 
CA Service Virtualization REST API. The virtualized service still must be defined in CA Service Virtualization
manually ahead of time. The functions are provided at the resource level and also at the sandbox level through a 
simple wrapper function that calls the resource function. 


In the example blueprint in this repo, the virtualized service and web app components are represented as CloudShell apps 
because they came from CloudShell Live, but ordinary resources or services can be used instead. 
Attach the scripts Activate_Virtual_Endpoint and Deactivate_Virtual_Endpoint to the resource or service model you use 
to represent the virtualized service. 


Choose an attribute to store the control URL of the virtualized service, e.g.

http://13.65.207.39:1505/api/Dcm/VSEs/VSE/data-driven-http-rest/actions/start

where VSE is the name of the virtual service environment and data-driven-http-rest is the name of the virtual service.

In this example, the attribute is Web Interface. You must update the scripts to read the virtual 
endpoint configuration URL from the attribute name you choose.



# Example web app with SalesForce backend
We created a simple example app consisting of a sales lead entry form. It adds the name, company, and phone number to 
its own in-memory database. It also automatically creates a new lead in SalesForce with only the name and company. 
The webpage at /leads.html displays the latest contents of the in-memory database using AJAX calls to the REST API. 
There are APIs to create a lead and list all leads. The URL of the SalesForce API is an input to the program. 
It can either be https://login.salesforce.com or a dummy served from the CA Service Virtualization machine.

Instead of deploying the app in the example blueprint, we assume it is launched manually ahead of time and 
hard-code its URL in the dummy deployment service. This arrangement can easily be replaced with a real deployment. 

Launch the app backed by the real SalesForce:
env SERVER_PORT=5001 python salesforce_app.py

Launch the app backed by the virtualized SalesForce:
env SERVER_PORT=5002 SALESFORCE_LOGIN_URL=http://13.65.207.39:8001 python salesforce_app.py

where 13.65.207.39:8001 is the address of the CA Service Virtualization server.

These apps can be launched in parallel on the same machine.


To show the app working, go to http://(server ip):5001/leads.html or http://(server ip):5002/leads.html 
and create some leads. You should see activity in SalesForce (5001) or the virtual service (5002). 
Be sure to start the CA Service Virtualization virtual service before trying the 5002 app. 


# Creating a virtual SalesForce service

## SalesForce auth
![](screenshots/virtual%20salesforce%20auth.png)
![](screenshots/virtual%20salesforce%20auth%202.png)
Full auth response body:
{"access_token":"000000000000000000000000000000000000000000000000000000000000000000000000000000000000000","instance_url":"http://13.65.207.39:8001","token_type":"Bearer","issued_at":"1492885274097","signature":"xxxxxxxxxxxxxxxxxxxxxxxxxxx=="}

where 13.65.207.39 is the IP of this CA Service Virtualization server.

The SalesForce login API returns a different URL like https://na50.salesforce.com that should be used for all 
subsequent SalesForce API calls. The virtualized service returns the URL of itself again since auth and the rest of the 
functionality are served from the same IP.  

## SalesForce create lead
![](screenshots/virtual%20salesforce%20lead.png)
![](screenshots/virtual%20salesforce%20lead%202.png)
Full lead creation response body:
{"id":"virtualized-salesforce-000000000000"}

