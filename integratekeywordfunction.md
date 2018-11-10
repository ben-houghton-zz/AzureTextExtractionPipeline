
# Azure Text Extraction Pipeline - Integrate Keyword Analysis

This page walks a developer through integrating the Keyword Analysis Function

To perform Keyword analysis of the text we retrieev from the image, we need to write a method that posts the text to the Node.js Function we created that performs keyword analysis.

Add the following code to your Image Analysis Function  you'll need to set the URI value to the URL of your Key Phrase analysis Function

```javascript

/// <summary>
/// Make a POST request containing the Image Text to the Node.js KeyWord Analysis Function we wrote
/// </summary>
/// <param name="lmageTextobUrl"></param>
/// <param name="log"></param>
/// <returns></returns>
private static string AnalyseKeyWords(string imageText, ILogger log)
{
    var requestBody = "{\"text\":\"" + imageText.Replace("\"", "") + "\"}";

    log.LogInformation($"Request body: {requestBody}");

    var uri = "[The URL of the KeyWord Analysis Function API you wrote]";
    log.LogInformation($"Calling Uri: {uri}");

    string response = HttpPost(uri, requestBody, log);

    return response;
}

```

You then need to invoke the method in your main method -

```javascript

 //This line posts the image text to the Text Analysis Keywords API
 var textKeywords = AnalyseKeyWords(ocrText, log);

```

and return the response to the user - 
```javascript
//Return the results to the user
    return imageUrl != null
        ? (ActionResult)new OkObjectResult(textKeywords)
        : new BadRequestObjectResult("There was an error: "+data);

```
