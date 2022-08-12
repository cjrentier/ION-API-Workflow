# ION-API-Workflow
A demo on starting a new Workflow instance using ION API.

* Documentation on Workflows can be found on https://docs.infor.com/ion/latest/en-us/iondeskceug_cloud_osm/default.html, in the menu go to Workflow.
* Documentation on ION API can be found on https://docs.infor.com/ionapi/latest/en-us/ionapiag_cloud/default.html.

## Managing Workflow in ION Desk
First create the Workflow and activate it in the ION Desk. 
### Flow definition
A simple Workflow is created to notify the requesting user that the Workflow was called and showing all the relevant paramters. The Workflow has only a system activity *Set Parameter* and a user activity *Notification*.

![image](https://user-images.githubusercontent.com/82956918/184337329-36729545-ffa0-43b1-9890-4fa3669bd85c.png)

### Parameters
These are either the parameters that are filled by the Workflow or proved via input by the calling application. In our case the client application via ION API will provide them using the StartWorkflowJSONBody object.

Parameters filled by the Workflow:

![image](https://user-images.githubusercontent.com/82956918/184338433-f3d4393e-dd47-4aa0-8a39-f18b95b58338.png)

Parameters filled by the client application calling the Workflow:

![image](https://user-images.githubusercontent.com/82956918/184338626-3bbfcf93-9c91-4f38-a474-aa521ed89a48.png)

FYI: Drill Backs, Structures and Authorizations are not filled and used in this example.

### Set Parameters activity
The following parameters are set:
* Name - Date Type - Assignment Type
* ID - INTEGER - Expression : ID()
* Name - STRING - Expression : NAME()
* Now - DATE AND TIME - Expression : NOW()
* StartTime - DATE AND TIME - Expression : STARTTIME()
* Source - STRING - Expression : SOURCE()
* SourceType - STRING - Expression: SOURCETYPE()
* RequestorName - STRING - Person Name for Person ID

![image](https://user-images.githubusercontent.com/82956918/184339576-81c386a7-dca5-4413-b91a-c502a5abd7e4.png)

### Notification activity
The notification activity sends a message to the Requestor. 
* Notification: Please see information about WorkflowId [ID], started at [StartTime].
* Content: All parameters are selected to demo how they are filled.
* Distribution: The Requestor parameter is used, this is filled by the calling application with the IFS Person ID.

Activate the Workflow, no Authorization are added as not needed for calling via ION API

## Available APIs for Workflows
The Infor ION / ION Process Application Rest API has multiple API calls for Workflows:
* /v1/workflow/interface (GET) Returns a list of active workflows and their interface information.
* /v1/workflow/start (POST) Start a new Workflow instance
* /v1/workflow/cancel (POST) Cancel a running Workflow instance
* Endpoint: https://mingle-ionapi.inforcloudsuite.com/<Tenant\>/IONSERVICES/process/application

In this example we only review the starting of a Workflow.

### Definition of /v1/workflow/start
Parameters for calling the API:
* logicalId - String - (query) -

```
Name                  Type              Description
logicalId             string (query)    The application logicalId
StartWorkflowJSONBody object (body)     The Workflow start request as JSON

Example Model: StartWorkflowRequest{...}
Example Value: 
{
  "workflowName": "string",
  "instanceName": "string",
  "inputVariables": [
    {
      "name": "string",
      "dataType": "STRING",
      "value": "string"
    }
  ],
  "inputStructures": [
    {
      "name": "string",
      "fields": [
        {
          "name": "string",
          "dataType": "STRING",
          "value": "string"
        }
      ],
      "subStructures": [
        null
      ]
    }
  ]
}

```
