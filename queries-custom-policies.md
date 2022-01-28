# Queries

Lacework provides a query language called Lacework Query Language (LQL) to help with data access. Queries are the
mechanisms used to access said data. For more information on LQL and queries, see the
[LQL overview](https://support.lacework.com/hc/en-us/articles/4402301824403-LQL-Overview).

### Create queries

In the example below, we create a query to detect when an unauthorized API call is made. View more query examples in the [Example LQL queries and policies](https://support.lacework.com/hc/en-us/articles/1500006140722-Example-LQL-Queries-and-Policies) support article. 

`curl --location --request POST 'https://YourLacework.lacework.net/api/v2/Queries' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <redacted>' \
--data-raw '{
"evaluatorId": "Cloudtrail",
"queryId": "LW_Custom_AWS_CTA_UnauthorizedAPICall",
"queryText": "LW_Custom_AWS_CTA_UnauthorizedAPICall {\n     SOURCE {\n        CloudTrailRawEvents\n    }\n    FILTER {\n     EVENT:eventType::String = '\''AwsApiCall'\''\n     AND ERROR_CODE IN ('\''AccessDenied'\'', '\''Client.UnauthorizedOperation'\'')\n    }\n    return DISTINCT {\n        INSERT_ID,\n        INSERT_TIME,\n        EVENT_TIME,\n        EVENT\n    }\n}"
}'`

### Retrieve queries

You can verify that you successfully created the query above by retrieving it. Below is a sample curl request
to retrieve the newly created query:

`curl --location --request GET 'https://YourLacework.lacework.net/api/v2/Queries/LW_Custom_AWS_CTA_UnauthorizedAPICall' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <redacted>' \
--data-raw ''`

### Execute queries

To execute a query, run the following command:

`curl --location --request POST 'https://YourLacework.lacework.net/api/v2/Queries/LW_Custom_AWS_CTA_UnauthorizedAPICall/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <redacted>' \
--data-raw '{ "arguments": [
{"name": "StartTimeRange", "value": "2021-08-01T18:12:25.000Z"},
{"name": "EndTimeRange", "value": "2021-10-01T18:12:25.000Z"}
]
}'`

***Tip***: If you receive no data from executing the query, try adjusting the start and end time range.

# Policies

Policies are mechanisms used to add annotated metadata to queries to improve the context of alerts, reports,
and information displayed in the Lacework Console. For more information on policies, see
[Create queries and custom policies using LQL](https://support.lacework.com/hc/en-us/articles/360061720914-Create-Queries-and-Custom-Policies-Using-LQL).

### Create custom policies

A policy for the created query can now be created using the following curl:

`curl --location --request POST 'https://YourLacework.lacework.net/api/v2/Policies' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <redacted>' \
--data-raw '{
"policyType": "Violation",
"queryId": "LW_Custom_AWS_CTA_UnauthorizedAPICall",
"title": "Unauthorized API Call ",
"description": "An unauthorized API call was made",
"remediation": "Check the unauthorized call and make sure it'\''s not from a malicious user",
"severity": "high",
"evalFrequency": "Hourly",
"alertEnabled": true,
"alertProfile": "LW_CloudTrail_Alerts.CloudTrailDefaultAlert_AwsResource",
"evaluatorId": "Cloudtrail",
"policyId": "lwcustom-4567",
"enabled": true
}
'`

***Note:*** An alert profile is the metadata defining how we display events. The only alert profile we currently support is
the one specified in the example above. We are working on supporting other alert profiles to further customizations.

### Retrieve custom policies

Now you can fetch the recently created policy by the policyId that is returned in the response body using the following curl:

`curl --location --request GET 'https://YourLacework.lacework.net/api/v2/Policies/YourLacework-lwcustom-4567' \
--header 'Authorization: Bearer <redacted>'`

***Tip:*** The policyId for the GET request is prefixed by your Lacework account name.

