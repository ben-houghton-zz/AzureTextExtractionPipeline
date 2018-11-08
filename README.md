# Azure Text Extraction Pipeline
This repo walks developer through the building a Text from Image ExtractionPipeline using Azure Cognitive Services and Serverless Functions


These [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) will process text embedded in images using the Azure Cognitive Services 
[Computer Vision OCR API](https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/concept-extracting-text-ocr) and [Text Analytics API](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/) 

The Computer Vision API provides state-of-the-art algorithms to process images and return information. For example, it can be used to determine if an image contains mature 
content, or it can be used to find all the faces in an image. It also has other features like estimating dominant and accent colors, categorizing the content of images, 
and describing an image with complete English sentences. Additionally, it can also intelligently generate images thumbnails for displaying large images effectively. In this instance we will use the OCR (Optical Character Recognition) feature to detect and extract text from images. We will then process this text to extract keywords and perform sentiment analysis.


## What you'll need

- An [Azure Subscription](https://azure.microsoft.com/en-gb/free/?&WT.srch=1&WT.mc_ID=SEM_Bing_UKAzureBG_)
- An [Azure Function App](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview)
- An [Azure DocumentDB](https://azure.microsoft.com/en-us/services/documentdb/) instance
- An [Azure Storage Account](https://azure.microsoft.com/en-gb/services/storage/)

## How it works

## 1 Provision the Cognitive Services Computer Vision API

We need to provision the Cogntive Services Computer Vision API with with which we can analyse the images. If you click on the 'Create a Resource' button in the top left corner of the Azure Portal. It will open the Azure Marketplace blade where you can start to provision Azure resources and services.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createaresource.JPG" width="700">

When you are in this menu, if you select AI & Machine Learning then Computer Vision you will see the screen for creating the service we will use for OCR.

 <img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop3.JPG" width="700">

Create the service with a unique to you name, use a Location of 'UK South', the 'F0' (Free pricing tier) and create a new [Resource Group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups) called something like '[yourshortname]functionapp-rg'.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop4.JPG" width="700">

When you click 'Create' the Compputer Vison API and the new Resource Group will get created. We will use the Resource Group to hold the resources we create for this lab.

Once the creation process for both the Computer Vision and Text Analytics APIs has completed, navigate to the both of the APIs you created and retrieve the authentication key for each service,  make a note of these in a text file as you'll need them later.

## 2 Provision the Cognitive Services Text Analytics API

Follow a similar process to the last step, but this time create a Text Analytics API.

Create the service with a unique to you name, use a Location of 'UK South', the 'F0' (Free pricing tier) and select the Resource Group you created earlier from the 'Resource Group' dropdown list. 

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop6.JPG" width="700">


Once the creation process for both the Computer Vision and Text Analytics APIs has completed, navigate to the both of the APIs you created and retrieve the authentication key for each service,  make a note of these in a text file as you'll need them later.

## 3 Provision a Storage Account

Next we need to create an [Azure Storage Account](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction) to hold files and also to provide [Storage Queue](https://docs.microsoft.com/en-gb/azure/storage/queues/storage-queues-introduction) capabilities later on.

Using the  'Create a Resource' button in the top left corner of the Azure Portal, open the Marketplace blade, select 'Storage' and then 'Storage Account - blob, file, table, queue'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createstorageaccount.JPG" width="700">


Create your storage account using the Resource Group that you created earlier (select from drop-down list), give it a name called something like '[yourshortname]functionappstg', use 'StorageV2' and 'LRS' replication.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createstorageaccountparams.JPG" width="700">


## 4 Create The .NET Framework Function App

Next we will create the Serverless Function App that will execute the image processing. Select the 'Create a Resource' button and this time select 'Compute' then 'Function App'.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop1.JPG" width="700">

Enter the configuration parameters use a name something like '[yourshortname]functionapp', select the Resource Group you created earlier from the drop-down list, choose the OS as Windows, Hosting Plan as 'Consumption', location as UK South, Runtime Stack as '.NET', and select the existing storage account you created in the last step. Select [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview) as 'On' with a location of 'North Europe'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createnetfunctionapp.JPG" width="700">

## 5 Build the HTTP Request Triggered Serverless Function 

Now we've create the Serverless Function App, we can create the individual Functions that run within the App we created. We're going to create a HTTP triggered Function that executes when we call it through a HTTP Request like an API. These type of Functions are great for building Microservice architectures.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createjsfunctioncode.JPG" width="700">


Select 'In-Portal Development' and 'Continue'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop8.JPG" width="700">

Then select 'Webhook + API' followed by 'Create'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop9.JPG" width="700">

This operation will create a directory and the scaffolding for a .NET based HTTP triggered Function. 

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop11.JPG" width="700">

Let's do a quick test to check that this scaffold works as expected. Click on 'Test' at the right hand edge of the page. You'll see a test console where you can change the HTTP request type, add and remove query parameters, headers and request body. Edit the request body and replace "name":"Azure" with "name" : "[Your name]".

e.g. 
```javascript
{
    "name": "Ben"
}
```
Click the 'Run' button a the right hand bottom of the page and a Request will be made to the URL of your function (you can see the URL by clicking '</> Get function URL' in the top middle of the screen). When the Function executes you'll see the execution Log in the middle bottom of the page, you'll see the output of the HTTP request 'Hello Ben' and the HTTP Status '200 OK' 

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createfunctiontestscaffold.JPG" width="700">

## 6 Edit the Function App To Use AI For Detecting and Extracting Text From Images

The Function parses the image URL and then POSTS it to the Cognitive Services Vision OCR API. If all is well, the API will return a JSON response containing the results of it's operation.

Earlier on you took a copy of the Cognitive Services Computer Vision authorisation key. We could embed this key in our code, but that's not very secure. We're going to secure the key in the Function application settings.

Click on the name of the Function and then 'Overview' and 'Application Settings'.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionappsettings.JPG" width="700">

Navigate to the 'Application Settings' section and click '+ Add new setting'. Add a setting with a name of 'ComputerVisionSubscriptionKey' and copy in the key value you stored earlier. Next click 'Save' at the top of the screen.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionappsettingsvalue.JPG" width="700">

You'll see in the code the line below. The imports the setting into the code at runtime, meaning the key is safely secured outside of the source code.

```javascript
var subscriptionKey = System.Environment.GetEnvironmentVariable("ComputerVisionSubscriptionKey", EnvironmentVariableTarget.Process);
```


Replace the existing scaffold code with the code below. This code accepts a HTTP request that has a POST body containing an image URL. 
 
```javascript

#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;
using System;
using System.Web;
using System.Text;
using System.Configuration;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string imageUrl = req.Query["imageUrl"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    imageUrl = imageUrl ?? data?.imageUrl;

    //Return the Cognitive Services output
    var apiOutput = AnalyseImage(imageUrl, log);

    return imageUrl != null
        ? (ActionResult)new OkObjectResult(apiOutput)
        : new BadRequestObjectResult("There was an error: "+data);
}


/// <summary>
/// Make a POST request to the ComputerVision API send the URL to the image in the blob container
/// </summary>
/// <param name="blobUrl"></param>
/// <param name="log"></param>
/// <returns></returns>
private static string AnalyseImage(string imageUrl, ILogger log)
{
    var requestBody = "{\"url\":\"" + imageUrl + "\"}";

    log.LogInformation($"Request body: {requestBody}");

    var queryString = "language=unk&detectOrientation=true";

    var uri = "https://uksouth.api.cognitive.microsoft.com/vision/v1.0/ocr?" + queryString;

    log.LogInformation($"Calling Uri: {uri}");

    string response = HttpPost(uri, requestBody, log);

    return response;
}


/// <summary>
/// Make a simple HTTP Post
/// </summary>
/// <param name="URI"></param>
/// <param name="body"></param>
/// <param name="log"></param>
/// <returns></returns>
public static string HttpPost(string URI, string body, ILogger log)
{

    var subscriptionKey = System.Environment.GetEnvironmentVariable("ComputerVisionSubscriptionKey", EnvironmentVariableTarget.Process);

    System.Net.WebRequest req = System.Net.WebRequest.Create(URI);
    req.Method = "POST";
    req.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    byte[] bytes = System.Text.Encoding.ASCII.GetBytes(body);

    log.LogInformation($"Posting data");

    req.ContentLength = bytes.Length;
    System.IO.Stream os = req.GetRequestStream();
    os.Write(bytes, 0, bytes.Length);
    os.Close();
    System.Net.WebResponse resp = req.GetResponse();
    if (resp == null) return null;
    System.IO.StreamReader sr = new System.IO.StreamReader(resp.GetResponseStream());
    return sr.ReadToEnd().Trim();
}



```

## 5 Test The Text Extraction Serverless Function

In a similar way as before  we can use the Test panel to the right of the page to test the new code. Replace the request body with a the URL of an image with some text in it. You can use anything that is publically available on the internet.

e.g. https://mediaforensicstoolkitstg.blob.core.windows.net/forensicsmedia/ceh.jpg


<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/testnetfunction.JPG" width="700">

You can see in the Output panel the analysis of the image and the text that was found in the image.

If you want to test this Function in more of a real world scenario as an API you can use a tool such as [Postman](https://www.getpostman.com/) or CURL.

With Postman you can create an API  request and call the Function as an external API. Retrieve the URL of your function (you can see the URL by clicking '</> Get function URL' in the top middle of the screen) and use Postman to make a request with the same request body you used in the Test panel. Make sure you set the Content Type to application/json

```javascript
{
    "imageUrl": "https://mediaforensicstoolkitstg.blob.core.windows.net/forensicsmedia/ceh.jpg"
}
  
```

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/postmantestnet.JPG" width="700">

## 6 Implement Text Analytics (Key Word Extraction and Sentiment Analsis) - OPTIONAL

Now we have implemented the text extraction via OCR, our next steps are to perform some text analytics on the text we have retrieved. 

To implement this, follow this lab - 

https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/keyphrasejsfunction.md

## 6 Review Monitoring Output

With any an app, it is important to continually monitor performance and availability. When we created the Function App we selected 'Application Insights' too. This provisioned a monitoring environment where we can review performance and set up alerting for metrics like availabilty.

If you look in the Resource Group you created, you'll see the Application Insights resource. Click on this an it will open the Application Insights control panel 

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/applicationinsights" width="700">



## 10 Clean Up Resources

Some of the benefits of cloud computing is the ability to create and destroy resources in an automated fashion as an when you need them. This gives you the ability to innovate rapidly but also keep costs low. It's important that to keep costs low we delete resources once we've finised with them, so now we've finished the lab let's destroy the resources we've used.

We could delete all the items one by one if needed, but the quickest and easiest way is to delete the whole [Resource Group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups). You can do this by navigating to the resource group you created in the Resource Group Explorer in the left-hand side menu,selecting the resource group, then clicking on the button at the top 'Delete Resource Group'. You'll be asked to confirm your delete action and to enter the resource group name as a secondary confirmation. Once you've done this and accepted, the Resource Group and the items within it will be deleted.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/deleteresourcegroup.JPG " width="700">


To make this demo work, you need to clone or download this repository, then - 

### Edit the Appsettings parameters with your own values

```javascript
{
  
```

### Upload the Functions code to you Azure Function App Environment
- You can do this in Visual Studio by right clicking on the project name and selecting 'Deploy'.
- You can use [Continuous Integration](https://docs.microsoft.com/en-us/azure/azure-functions/functions-continuous-deployment)
- Cut and paste this code into [a Function you created in the Azure Portal](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function-azure-portal)



## Learn more about developing [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference)


