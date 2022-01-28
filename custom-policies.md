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
