Overview

In this document, we will walk through API Manager Alerts, its need, usage, benefits and how to implement this using different options.

Anypoint Platform alerts you about request behaviour, performance issues, actions related to client application contracts, and runtime events. The platform supports three types of alerts: API alerts, contracts alerts, and runtime alerts. API alerts and contracts alerts are managed by Anypoint API Manager (API Manager). Runtime alerts are managed by Anypoint Runtime Manager (Runtime Manager).

Type Of API Alerts

API Manager enables you to add the following API alerts to an API instance:

·         Policy Violation

An infraction of one or more policies governing the API occurs.

·         Request Count

Users request access to the API more times than allowed in a specified interval of time.

Specify time interval values from 1 through 999999999. To prevent data bursts, intervals are implemented as sliding windows instead of as absolute minute boundaries.

·         Response Code

The API returns one of these HTTP codes upon receiving a request: 400, 401, 403, 404, 408, 500, 502, or 503.

·         Response Time

The response time of the API exceeds a specified timeout period.

 

API alerts are triggered only when states change, although the first occurrence of a state change might be undetected due to newly created alerts not having defined states.

When configuring an API alert, you select Business Group users to receive email notifications about the alert. Notifications include information about events that trigger alerts as specified by severity level. Users receive the following:

§ One email describes the alert:

Your API, CesarDemoAPI - Basic:151 (Environment: "NewOne"), has received greater than 2 requests within 1 consecutive periods of 1 minute.

§ Another email notifies you when the alert is resolved.

Your API Version, jsonplaceholderapi - 1.0.development, is no longer in an alert state. The number of policy violations was not greater than 1 in the last 1 consecutive periods of 1 minute.

After an alert is triggered, API Manager sends the first set of two notification emails and stops listening for alerts until the next alert period begins. This technique prevents repeated notification emails.

Implementation

There are three ways to create API Manager Alerts -

Using Any point platform API Manager UI

Anypoint Platform REST API

Anypoint CLI

Currently we are using option 1 to create API Manager alerts while we will explore option2 which will be picked for automation part.

We have used POST or GET method for Create and Get alert details.

Platform REST API

Create an API alert - 

Pre-requisites - Connected Apps with ‘Manage API Alerts’ permission.



POST  https://anypoint.mulesoft.com/apimanager/api/v1/organizations/{organizationId}/environments/{environmentId}/apis/{environmentApiId}/alerts


URI Parameters
OrganizationId:
  Type: String
  Required: True
  Pattern: ^[a-fA-F0-9]{8}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{12}$
EnvironmentId:
  Type: String
EnvironmentApiId:
  Type: Integer
  MinValue: 1
  MaxValue: 2147483647
  Json Body



{
    "apiAlertsVersion": "1.0.0",
    "condition": {
        "aggregate": "COUNT",
        "operator": "GREATER_THAN",
        "value": 90,
        "resourceType": "api"
    },
    "enabled": true,
    "name": "some alert name",
    "period": {
        "repeat": 5,
        "duration": {
            "count": 5,
            "weight": "DAYS"
        }
    },
    "recipients": [
        {
            "type": "user",
            "value": "57e281b5-ec5b-4701-80a0-92a936a68da3",
            "lastName": "Owner",
            "firstName": "Organization"
        }
    ],
    "severity": "CRITICAL",
    "type": "api-request-count"
}
Responses:

We get the successfully created alert definition.

HTTP Code – 201



{
    "apiAlertsVersion": "1.0.0",
    "api": {
        "id": 95,
        "name": "alerts"
    },
    "condition": {
        "aggregate": "COUNT",
        "operator": "GREATER_THAN",
        "value": 90,
        "resourceType": "api"
    },
    "createdAt": 1502053776408,
    "enabled": true,
    "environmentId": "b0f02fea-b5e9-4bf1-aef8-a2d4027eca5d",
    "id": "1e2a174f-162b-4803-ab74-fb8d5feeaf86",
    "lastModified": 1502053776408,
    "name": "some alert name",
    "organizationId": "f0c9b011-980e-4928-9430-e60e3a97c043",
    "period": {
        "repeat": 5,
        "duration": {
            "count": 5,
            "weight": "DAYS"
        }
    },
    "recipients": [
        {
            "type": "user",
            "value": "57e281b5-ec5b-4701-80a0-92a936a68da3",
            "lastName": "Owner",
            "firstName": "Organization"
        }
    ],
    "severity": "CRITICAL",
    "type": "api-request-count"
}
 

For Mulesoft Developers

We are using manual steps to create API Manager alerts on any new API and those are predefined with parameters. Attaching JSON body structure for all those predefined alerts here. which will be used in our automation process.

 

Open Common API Alerts.zip
Common API Alerts.zip
11 Mar 2024, 09:08 am
Alert name plays vital role to have unique Alert on API Manager instance. This is very much important for automation process as well.

File Naming convention should be AlertType-{Severity}.json example as below.  e.g.,response-code-400-info.json

Reason Behind this- Same alert can be defined on an API with different severity. so, we are keeping alert Type and severity in Alert name.. (*NOTE- Keep alert file name and alert name (inside json file) same as it is used in CI/CD pipeline for comparison)

response-code-400-warning.json

response-code-401-info.json

response-code-413-info.json

response-code-422-info.json

response-code-500-critical.json

response-code-503-critical.json

request-count-info.json

response-time-info.json

policy-violation-policyname-info.json - Policy_violation_alert.txt 
Please Refer below repository for alerts templates.
AlertsTemplates · master · Enterprise Integration / MuleTemplates / DevOps-Templates · GitLab (slc.co.uk)

We will be using these defined json data files in CI CD pipeline to create API Manager Alerts. 

 

Common Alerts added under alerts directory on API template are as below- 

 

Attaching Sample project structure how it looks like once we add alerts folder under resource directory. We will be creating two directories. 

a. prod – as there are specific email addresses for prod environment to get alert notification.

b. nonprod - these can be used for all non live environment.

Open image (4).png
image (4).png
*** Developers needs to make sure they review and update alert configurations as per business requirement or NFR and need  to add API name as suffix to the alerts name and in configuration files as shown below to avoid the Name constraint across the environments. 


Open image-20240304-142159.png
image-20240304-142159.png
Open image-20240304-142420.png
image-20240304-142420.png
 

 

Attaching Common Alerts for prod and nonprod environment.

Open Common API Alerts.zip
Common API Alerts.zip
11 Mar 2024, 09:08 am
Default API Alerts defined on API Template

HTTP Error Code

Details

Alert Type

Threshold Value

NonProd Email Notification

Prod Email Notification

400

Bad Request

Info

25 requests in 1-minute for 1 consecutive period

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

400

Bad Request

Warning

30 requests in 1-minute for 1 consecutive period

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

400

Bad Request

Critical

40 requests in 1-minute for 1 consecutive period

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

401

Unauthorized

Critical

25 requests in 1-minute for 1 consecutive period for critical

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; saas_service_alerts@slc.co.uk; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

401

Unauthorized

Warning

10 requests in 1-minute for 1 consecutive period for critical

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; saas_service_alerts@slc.co.uk; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

500

Server Error

Critical

25 requests in1-minute for 1 consecutive period for critical

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; saas_service_alerts@slc.co.uk; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

500

Server Error

Warning

10 requests in1-minute for 1 consecutive period for critical

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; saas_service_alerts@slc.co.uk; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

503

Service Unavailable

Critical

25 requests in 1-minute for 1 consecutive period for critical

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; saas_service_alerts@slc.co.uk; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

503

Service Unavailable

Warning

10 requests in 1-minute for 1 consecutive period for critical

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; saas_service_alerts@slc.co.uk; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

Response Time

Response Time

Critical

(Critical) Response time greater than 10 seconds for occurrence > 25 for 5 consecutive time period of 1 min

mulesoft_c4e_integration_notification@slc.co.uk; enterprise_integration_non_live_notifications@slc.co.uk

mulesoft_c4e_integration_notifications_live@slc.co.uk ; saas_service_alerts@slc.co.uk; ca_support@slc.co.uk; enterprise_integration_live_notifications@slc.co.uk

 

Next Actions Items

For existing API’s - no need to add alerts directory as those alerts are already created. Unless n until any new alert needs to be added,

For New API’s- alerts directory with default predefined alerts json files will be present on template which needs to be reviews and update as per business requirement

CI/CD pipeline poc code needs to move into live pipeline. (Devops Task)

Under alerts tab create two directories → prod and nonprod and copy those alert files into both directories if applicable. Update email address in those Json file as alert notification email notification receiver is different for OAT n Live env.

 

For Devops Team

Automation CI/CD pipeline Steps 

Step1: - Developed API should include below default behaviour Alerts json files on /resources/alerts directory location. (those are added as paer of API template already)

Open response_code_500_alert.json
response_code_500_alert.json
07 Sept 2023, 09:35 am
Step2: - On CI/CD pipeline add step after API manager Deployment for Create Alerts. This step will take API menager Instance ID value from previous step while organizationId and environmentId from Pipeline Variables.

This step will validate /alerts directory available under /resources directory, if not then mark steps as failed but continue to next step as this is not mandatory steps.

If yes, then first call GET API Manager alerts endpoint to check if any existing alert present on API.

GET 

https://anypoint.mulesoft.com/apimanager/api/v1/organizations/{organizationId}/environments/{environmentId}/apis/{environmentApiId}/alerts?limits=1000

 URI Parameters

If response has array of alerts then loop over it and   compare with file names and if exist then check for next alert. If empty array returned then create alerts for each file contain.

To create Alert use below plaform API

POST

https://anypoint.mulesoft.com/apimanager/api/v1/organizations/{organizationId}/environments/{environmentId}/apis/{environmentApiId}/alerts

 URI Parameters



OrganizationId:
  Type: String
  Required: True
  Pattern: ^[a-fA-F0-9]{8}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{12}$
EnvironmentId:
  Type: String
EnvironmentApiId:
  Type: Integer
  MinValue: 1
  MaxValue: 2147483647
JSON body for this endpoint will be content from Alert Json file.

If any alert creation fails. mark step as failed and logs should clearly say what was root cause.

Do not mark CI/CD pipeline job as failed if Alert creation step is failed. In case if alert creation failed n need to be created manually then please go-ahead n create alerts manually. Keep Alert Name same as name mentioned in Alert file for a particular alert type.
There is now a provision to maintain alerts in JSON file format under same API repository in GitLab as shown in below,


Open image-20231114-125411.png

nonprod - Alerts applicable to OAT environment should place under nonprod directory.
prod - Alerts applicable to LIVE environment should place under prod directory.
please find the samples for Jenkins console output.
Case1: For DEV Env

Open image-20231114-130048.png

Case 2: For NEXT-TEST Env

Open image-20231114-130147.png

Case3: For OAT (New Alert, Wrong Json Format, Already Existing API)

Open image-20231114-130439.png


As part of Prepare to Apply Alerts Stage:


Open image-20231114-130818.png

As part of API Manager Stage:

Open image-20231114-131246.png

API Manager:


Open image-20231114-132320.png

Case 4: No Alerts Folder
As part of Prepare to Apply Alerts Stage

Open image-20231114-132834.png

As a part of API Manager Stage:


Open image-20231114-132927.png
