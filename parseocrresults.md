
# Azure Text Extraction Pipeline - Parse Text From Computer Vision OCR API

This repo walks a developer through parsing the text out of the JSON response returned by the Azure Computer Vision Optical Character Recognition API.

The JSON that is returned is not easily readable and requires some parsing to extract the text.

```javascript
{
  "language": "en",
  "textAngle": -0.020943951023931366,
  "orientation": "Up",
  "regions": [
    {
      "boundingBox": "37,93,146,35",
      "lines": [
        {
          "boundingBox": "66,93,87,15",
          "words": [
            {
              "boundingBox": "66,93,87,15",
              "text": "CERTIFIED"
            }
          ]
        },
        {
          "boundingBox": "37,111,146,17",
          "words": [
            {
              "boundingBox": "37,113,71,15",
              "text": "ETHICAL"
            },
            {
              "boundingBox": "113,111,70,15",
              "text": "HACKER"
            }

          ]
        }
      ]
    }
  ]
}
```

## Add A 'using ' Declaration

To extract the text, we'll use the .NET Newtonsoft.Json.Linq libraries, so we need to add a 'using' declaration to the source code.

Add this line under the other using declarations at the top of the code.

```javascript
using Newtonsoft.Json.Linq
```

## Add A Parsing Method to the Function

Next we will create the Serverless Function App that will execute the image processing. Select the 'Create a Resource' button and this time select 'Compute' then 'Function App'.

The code below parses the text elements out of the JSON response so that we can start to perform text analytics on the resulting words.

```javascript

/// <summary>
/// It's a cluncky approach, but it works. This method parses the JSON received from the 
private static string ParseImageText(string apiOutput, ILogger log)
{
    log.LogInformation("Parsing Image Text");

    string output = String.Empty;
    //Extract the text from the bounding boxes
    JObject json = JObject.Parse(apiOutput);
    var boxCollection = json.SelectToken("regions").Children();
    var line = boxCollection["lines"];
    var boxes = line.Children();
    var words = boxes["words"];
    
    foreach (var word in words.Children())
    {
        output += String.Concat((string)word["text"]," ");
    }
    return output;
}

```
Once you've completed these steps, the resulting text extracted from the JSON should be 

```javascript

CERTIFIED ETHICAL HACKER 

```
