

## 1 Create The JavaScript Function App

Next we will create the Serverless Function App that will execute the image processing. Select the 'Create a Resource' button and this time select 'Compute' then 'Function App'.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop1.JPG" width="700">

Enter the configuration parameters use a name something like '[yourshortname]functionapp', select the Resource Group you created earlier from the drop-down list, choose the OS as Windows, Hosting Plan as 'Consumption', location as UK South, Runtime Stack as 'JavaScript', and select the existing storage account you created in the last step. Select [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview) as 'On' with a location of 'North Europe'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop2.JPG" width="700">

## 2 Build the HTTP Request Triggered Serverless Function 

Now we've create the Serverless Function App, we can create the individual Functions that run within the App we created. We're going to create a HTTP triggered Function that executes when we call it through a HTTP Request like an API. These type of Functions are great for building Microservice architectures.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createjsfunctioncode.JPG" width="700">


Select 'In-Portal Development' and 'Continue'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createjsfunctionportal.JPG" width="700">

Then select 'Webhook + API' followed by 'Create'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createjsfunctionwebhookandapi.JPG" width="700">

This operation will create a direcotry and the scaffolding for a Node.JS based HTTP triggered Function. 

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createjsfunctionscaffolding.JPG" width="700">

Let's do a quick test to check that this scaffold works as expected. Click on 'Test' at the right hand edge of the page. You'll see a test console where you can change the HTTP request type, add and remove query parameters, headers and request body. Edit the request body and replace "name":"Azure" with "name" : "[Your name]".

e.g. 
```javascript
{
    "name": "Ben"
}
```
Click the 'Run' button a the right hand bottom of the page and a Request will be made to the URL of your function (you can see the URL by clicking '</> Get function URL' in the top middle of the screen). When the Function executes you'll see the execution Log in the middle bottom of the page, you'll see the output of the HTTP request 'Hello Ben' and the HTTP Status '200 OK' 

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createjsfunctiontestscaffold.JPG" width="700">

## 3 Edit the Function App To Perform The Key Phrase Extraction 
 
As we start to build out our Node.JS code, we need to import some Node packages to enable us to make HTTP requests to the Cognitive Services APIs. To do this we need to access the Kudu interface where we can make the NPM commands. Click on the name of your Function to reveal the Configuration menu. 

createjsfunctionsettings.jpg

Then click on 'Platform Features' then 'Console (CMD/PowerShell)'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createjsfunctionconsole.JPG" width="700">


Once into the Kudu menu, click 'Debug Console' at the top, then select 'CMD'. In the CMD screen enter

```javascript
{
    npm install request
}
```
You may see a few message in red, ignore these, the Node.JS modules will still be available to our Function.



```javascript

var request = require('request');

　
module.exports = function (context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    // Make a call out to Cognitive Services
    if (req.query.text)
    {

        request.post({
            url: 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyphrases?',
            headers: {
                "Content-Type": "application/json",
                "Ocp-Apim-Subscription-Key": "[Cogntive Services Key]"
            },
            json: {
                "documents": [
                  {
                      "language": "en",
                      "id": "id1",
                      "text": req.query.text
                  }
                ]
            }
        },
        function (err, res, body) {
            context.log("Response from Cog API (err, res, body)");
            context.log(JSON.stringify(err, null, " "));
            context.log(JSON.stringify(res, null, " "));
            context.log(JSON.stringify(body, null, " "));

            // Check to see if we succeeded.
            if (err || res.statusCode != 200) {
                context.log(err);
                context.res = {
                    status: 500,
                    body: err
                }
                context.done();
                return
            }

            context.res = {
                // status: 200, /* Defaults to 200 */
                // Send back the score we got from the Cog API
                    body
            };
            context.done();
        });
    }
    else {
        // Bad request - Missing property on body
        context.res = {
            status: 400,
            body: "Expected 'text' property on query string"
        };
        context.done();
    }
};

```