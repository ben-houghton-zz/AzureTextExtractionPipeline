# AzureTextExtractionPipeline
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

Create the service with a unique to you name, use a Location of 'UK West', the 'F0' (Free pricing tier) and create a new [Resource Group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups) called something like '[yourshortname]functionapp-rg'.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop4.JPG" width="700">

When you click 'Create' the Compputer Vison API and the new Resource Group will get created. We will use the Resource Group to hold the resources we create for this lab.

Once the creation process for both the Computer Vision and Text Analytics APIs has completed, navigate to the both of the APIs you created and retrieve the authentication key for each service,  make a note of these in a text file as you'll need them later.

## 2 Provision the Cognitive Services Text Analytics API

Follow a similar process to the last step, but this time create a Text Analytics API.

Create the service with a unique to you name, use a Location of 'UK West', the 'F0' (Free pricing tier) and select the Resource Group you created earlier from the 'Resource Group' dropdown list. 

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop6.JPG" width="700">


Once the creation process for both the Computer Vision and Text Analytics APIs has completed, navigate to the both of the APIs you created and retrieve the authentication key for each service,  make a note of these in a text file as you'll need them later.

## 3 Provision a Storage Account

Next we need to create an [Azure Storage Account](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction) to hold files and also to provide [Storage Queue](https://docs.microsoft.com/en-gb/azure/storage/queues/storage-queues-introduction) capabilities later on.

Using the  'Create a Resource' button in the top left corner of the Azure Portal, open the Marketplace blade, select 'Storage' and then 'Storage Account - blob, file, table, queue'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createstorageaccount.JPG " width="700">


Create your storage account using the Resource Group that you created earlier (select from drop-down list), give it a name called something like '[yourshortname]functionappstg', use 'StorageV2' and 'LRS' replication.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/createstorageaccountparams.JPG " width="700">


## 3 Create The HTTP Request Triggered Serverless Function To Detect and Extract Text From Images 

Next we will create the Serverless Function App that will execute the image processing. Select the 'Create a Resource' button and this time select 'Compute' then 'Function App'.

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop1.JPG " width="700">

Enter the configuration parameters use a name something like '[yourshortname]functionapp-fn', select the Resource Group you created from the drop-down list, choose the OS as Windows, Hosting Plan as 'Consumption', location as UK West, Runtime Stack as '.NET', and use the existing storage account you created in the last step. Select [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview) as 'On' with a location of 'North Europe'

<img src="https://github.com/ben-houghton/AzureTextExtractionPipeline/blob/master/images/functionworkshop2.JPG " width="700">



## 4 Test The Text Extraction Serverless Function


## 5 Create The Storage Queue Triggered Serverless Function To Perform Text Analysis 



## 6 Test The Text Analysis Serverless Function



## 7 Create a Cosmos DB NoSQL Database to store the results



## 8 Store The Results From the PipeLine in the NoSQL DB



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


