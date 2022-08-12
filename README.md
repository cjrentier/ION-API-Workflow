# ION-API-Workflow
A demo on starting a new Workflow instance using ION API.

* Documentation on Workflows can be found on https://docs.infor.com/ion/latest/en-us/iondeskceug_cloud_osm/default.html, in the menu go to Workflow.
* Documentation on ION API can be found on https://docs.infor.com/ionapi/latest/en-us/ionapiag_cloud/default.html.

## Managing Workflow in ION Desk
Before we can call the Workflow it must be created and activated in ION Desk. A simple Workflow is created to notify the requesting user that the Workflow was called and showing all the relevant paramters. The Workflow has only a system activity *Set Parameter* and a user activity *Notification*.
![image](https://user-images.githubusercontent.com/82956918/184337329-36729545-ffa0-43b1-9890-4fa3669bd85c.png)



## Available APIs for Workflows
The Infor ION / ION Process Application Rest API has multiple API calls for Workflows:
* /v1/workflow/interface (GET) Returns a list of active workflows and their interface information.
* /v1/workflow/start (POST) Start a new Workflow instance
* /v1/workflow/cancel (POST) Cancel a running Workflow instance
* Endpoint: https://mingle-ionapi.inforcloudsuite.com/<Tenant>/IONSERVICES/process/application

In this example we only review the starting of a Workflow.
