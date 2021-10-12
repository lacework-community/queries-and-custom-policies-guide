# Authentication

### Connecting to the Lacework API

Users need a bearer access token to communicate with the Lacework application programming interface (API). To retrieve a bearer access token, users need an API key to create a POST request to the access token endpoint. To create an API key,
navigate to **Settings** within the Lacework Console. Under **Settings**, click the **API keys** section and then click **Create
new** located on the top left. After creating a new API key, download and store it in a safe space because you will need it to create a bearer access token, which grants temporary access to the Lacework API. For more documentation
on generating API keys, visit our docs [here](https://support.lacework.com/hc/en-us//articles/360011403853).

Once you download your API key, you can make a POST request to retrieve a bearer access token.
Below is a sample curl request used to fetch an access token.

`curl --location --request POST 'https://YourLacework.lacework.net/api/v2/access/tokens' \
--header 'X-Lw-Uaks: <Insert Secret from API Key>' \
--header 'Content-Type: application/json' \
--data-raw '{
"keyId":"<Insert KeyId from API key>",
"expiryTime": 86400
}'`

Below is a sample response from the access token's endpoint.

`{
"expiresAt": "2021-09-28T21:57:15.494Z",
"token": "<redacted>"
}`

For security purposes, we redacted the actual token returned from the response.

### Key concepts

* You need to first create an API key within the Lacework UI to later create a bearer access token.
* Bearer access tokens provide *temporary* access to the Lacework API. This means that the tokens have an *expiration*.
* To determine a token's expiration, look at the response body from the POST request.
* When making a POST request to the access token's endpoint, you can specify the token's expiry. The max value is 86,400 seconds, which is 24 hours.
* When making a POST request to the access token's endpoint, you need two pieces of information&mdash;the API key ID and the API key secret.


### Recommendations
* Store the API key in a secure place and do not share it with others.
* If someone else needs to access the API, they must create their own API key.
* Regularly rotate API keys.

# Queries

Lacework provides a query language called Lacework Query Language (LQL) to help with data access. Queries are the
mechanism used to access said data. For more information on LQL and queries, please visit our documentation
[here](https://support.lacework.com/hc/en-us/articles/4402301824403-LQL-Overview).

### Creating queries

`curl --location --request POST 'https://YourLacework.lacework.net/api/v2/Queries' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <redacted>' \
--data-raw '{
"evaluatorId": "Cloudtrail",
"queryId": "LW_Custom_AWS_CTA_UnauthorizedAPICall",
"queryText": "LW_Custom_AWS_CTA_UnauthorizedAPICall {\n     SOURCE {\n        CloudTrailRawEvents\n    }\n    FILTER {\n     EVENT:eventType::String = '\''AwsApiCall'\''\n     AND ERROR_CODE IN ('\''AccessDenied'\'', '\''Client.UnauthorizedOperation'\'')\n    }\n    return DISTINCT {\n        INSERT_ID,\n        INSERT_TIME,\n        EVENT_TIME,\n        EVENT\n    }\n}"
}'`

In this example above, we create a query to detect when an unauthorized API call is made. View more examples of the kinds of queries
you can write [here](https://support.lacework.com/hc/en-us/articles/1500006140722-Example-LQL-Queries-and-Policies).

### Retrieving queries

You can verify that you successfully created the query above by retrieving it. Below is a sample curl request
to retrieve the newly created query.

`curl --location --request GET 'https://YourLacework.lacework.net/api/v2/Queries/LW_Custom_AWS_CTA_UnauthorizedAPICall' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <redacted>' \
--data-raw ''`

### Executing queries

To execute a query, run the following command.

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

Policies are a mechanism used to add annotated metadata to queries to improve the context of alerts, reports,
and information displayed in the Lacework Console. For more information policies, visit our support site
[here](https://support.lacework.com/hc/en-us/articles/360061720914-Create-Queries-and-Custom-Policies-Using-LQL).

### Creating Custom Policies

A policy for the created query can now be created using the following curl found below.

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

***Note:*** An Alert profile is the metadata defining how we display events. The only alert profile we currently support is
the one specified in the example. We are actively working on supporting other alert profiles to further customizations.

### Retrieving Custom Policies

Now you can fetch the recently created policy by the policyId that is returned in the response body using the following curl.

`curl --location --request GET 'https://YourLacework.lacework.net/api/v2/Policies/YourLacework-lwcustom-4567' \
--header 'Authorization: Bearer <redacted>'`

***Tip:*** The policyId for the get request is prefixed by your Lacework account name.

