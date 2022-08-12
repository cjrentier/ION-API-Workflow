# ION-API-Workflow
A small demo on starting a new Workflow instance using ION API. The definition of the Workflow in ION Desk is explained and how to call the Workflow using ION API.

Documentation:
* Workflows: https://docs.infor.com/ion/latest/en-us/iondeskceug_cloud_osm/default.html, in the menu go to Workflow.
* ION API: https://docs.infor.com/ionapi/latest/en-us/ionapiag_cloud/default.html go to Available APIs > Infor ION > IONProcessApplicationService

## Workflow Definition in ION Desk
Modelling the Workflow and activating it in the ION Desk. The Workflow Definition has been attached [WF_DisplayReceivedArguments](https://github.com/cjrentier/ION-API-Workflow/blob/main/WF_DisplayReceivedArguments.xml)
### Flow definition
A simple Workflow is created to notify the requesting user that the Workflow was called and showing all the relevant parameters. The Workflow has only a system activity *Set Parameter* and a user activity *Notification*.

![image](https://user-images.githubusercontent.com/82956918/184337329-36729545-ffa0-43b1-9890-4fa3669bd85c.png)

### Parameters
These are either the parameters that are filled by the Workflow or input by the calling application. In our case the client application via ION API will provide them using the StartWorkflowJSONBody object.

Parameters filled by the Workflow:

![image](https://user-images.githubusercontent.com/82956918/184338433-f3d4393e-dd47-4aa0-8a39-f18b95b58338.png)

Parameters filled by the client application calling the Workflow:

![image](https://user-images.githubusercontent.com/82956918/184356441-b7eac961-5df7-48e8-b74d-d4f7fa97ea48.png)

FYI: Drill Backs, Structures and Authorizations are not filled and used in this example. The calling application called another API (/Mingle/SocialService.Svc/User/Detail) to get its own PersonID, this will be used for the Notification distribution.

### Set Parameters activity
The following parameters are set:

**Name - Date Type - Assignment Type**
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
* Notification: Example: "Please see information about WorkflowId [ID], started at [StartTime]".
* Content: All parameters are selected to demo how they are filled.
* Distribution: The Requestor parameter is used, this is filled by the calling application with the IFS Person ID.

Activate the Workflow, no Authorization is added as not needed for calling via ION API

## Available APIs for Workflows
The Infor ION / ION Process Application Rest API has multiple API calls for Workflows:
* /v1/workflow/interface (GET) Returns a list of active workflows and their interface information.
* /v1/workflow/start (POST) Start a new Workflow instance
* /v1/workflow/cancel (POST) Cancel a running Workflow instance
* Endpoint: https://mingle-ionapi.inforcloudsuite.com/<Tenant\>/IONSERVICES/process/application

In this example we only review the starting of a Workflow.

### Definition of /v1/workflow/start
Parameters for calling the API:
* **logicalId** - string (query) - The application logicalId
* **StartWorkflowJSONBody** - object (body) - The Workflow start request as JSON

Example Value: 
```
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

Testing the Workflow API via ION API is possible too.

Fill **logicalId** with the relevant value e.g. lid://infor.test.myapp

Fill **StartWorkflowJSONBody** with below JSON object:
```
{
	"instanceName": "Test starting Workflow from API",
	"workflowName": "WF_DisplayReceivedArguments",
	"inputVariables": [
		{
			"name": "InputRequestor",
			"dataType": "STRING",
			"value": "c63d2ae6-edd1-4a74-9a82-d01727668827"
		},
		{
			"name": "InputString",
			"dataType": "STRING",
			"value": "PWinput"
		},
		{
			"name": "InputBoolean",
			"dataType": "BOOLEAN",
			"value": "True"
		},
		{
			"name": "InputInteger",
			"dataType": "INTEGER",
			"value": 123456789
		},
		{
			"name": "InputDecimal",
			"dataType": "DECIMAL",
			"value": 1234.6789
		},
		{
			"name": "InputHyperlink",
			"dataType": "STRING",
			"value": "https://microsoft.com"
		},
		{
			"name": "InputDate",
			"dataType": "DATE",
			"value": "2021-12-31"
		},
		{
			"name": "InputDateTime",
			"dataType": "DATETIME",
			"value": "2021-12-31T13:45:50Z"
		}
	]
}
```
Example:

![image](https://user-images.githubusercontent.com/82956918/184353423-6f523adf-91e2-4257-aa4f-7db3633cdb3c.png)

Response Code = 200 and Response body will contain the Workflow ID:
```
{
  "id": 207
}
```
In ION Desk > Monitors & Workflows > Active Workflows the Instance can be checked, there it will show the Instance Name as filled by the caller.

![image](https://user-images.githubusercontent.com/82956918/184360208-5301a52f-c34b-4835-8283-9a5602b46309.png)

The Notification in the Inbox will look like:

![image](https://user-images.githubusercontent.com/82956918/184356311-1a906620-6537-4edc-99da-382b8328ec7c.png)

## Building Client Application
A Client application (.NET, Java, Postman, Powershell or any other platform) can be used to call the ION API and trigger a Workflow.

The **/IONSERVICES/process/application/v1/workflow/interface** can be used to list all Workflows which are active and can be started.
For example call **/IONSERVICES/process/application/v1/workflow/interface?name=<WorkflowName\>** to check if the Workflow is active before starting it. Check if it responds, the .name property is present and filled with the <WorkflowName\>.

For example calling the ION API from Postman to test if Workflow is present and is Active:

![image](https://user-images.githubusercontent.com/82956918/184371788-657f697c-8347-4aec-b519-e871bcb5a585.png)

For example calling the ION API from Postman to start the Workflow with parameters filled:

![image](https://user-images.githubusercontent.com/82956918/184360346-fc944232-2a7c-4733-8292-c9d40856f6ed.png)

Response: 

![image](https://user-images.githubusercontent.com/82956918/184360415-c71f991e-aa0c-43a2-bca8-daf5093fcee8.png)

In ION Desk it will show like:

![image](https://user-images.githubusercontent.com/82956918/184360871-754b3f1b-fd38-471d-835b-223d58004388.png)


For any questions or details please mail to [Christiaan Rentier](mailto:Christiaan.Rentier@infor.com?subject=Using%20ION-API%20for%20Workflows)

