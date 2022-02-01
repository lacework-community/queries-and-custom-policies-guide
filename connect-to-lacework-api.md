
# Connect to the Lacework application programming interface (API)

Users need a bearer access token to communicate with the Lacework API. The bearer access token grants *temporary* access to the Lacework API. This means that the tokens have an *expiration*. To retrieve a token, users need an API key to create a POST request to the bearer access token endpoint. 

### Steps 

#### 1. Create an API key

* a. To create an API key, navigate to **Settings** within the Lacework Console. 
* b. Under **Settings**, click the **API keys** section 
* c. Click **Create new** on the top left of the page. 
* d. After creating a new API key, download and store it in a safe space. For more documentation
on generating API keys, read our [Generate API access keys and tokens](https://support.lacework.com/hc/en-us//articles/360011403853) support article.

#### 2. Retrieve a bearer access token

You need the API key ID and the API key secret to make a POST request to the access token's endpoint. Once you download your API key, you can make a POST request to retrieve a bearer access token. 

* a. Use the sample curl request below to fetch a bearer access token. You can specify the token's expiry by replacing the number next to `expiryTime`. The max value is 86,400 seconds, which is 24 hours.

`curl --location --request POST 'https://YourLacework.lacework.net/api/v2/access/tokens' \
--header 'X-Lw-Uaks: <Insert Secret from API Key>' \
--header 'Content-Type: application/json' \
--data-raw '{
"keyId":"<Insert KeyId from API key>",
"expiryTime": 86400
}'`

* b. You will receive a response from the bearer access token's endpoint, similar to the one below. For security purposes, we redacted the actual token returned from the response.

`{
"expiresAt": "2021-09-28T21:57:15.494Z",
"token": "<redacted>"
}`

### Recommendations
* Store the API key in a secure place and do not share it with others.
* If someone else needs to access the API, they must create their own API key.
* Regularly rotate API keys.

