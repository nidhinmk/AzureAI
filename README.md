# AzureAI

**Computer Vision**

Login to Microsoft Azure portal (https://portal.azure.com) and create a resource. 

<img width="1508" height="513" alt="Screenshot 2025-10-19 at 8 49 06 AM" src="https://github.com/user-attachments/assets/9cb19d34-f93a-4c51-8aca-9fa9cd1efeb5" />

Search for Computer Vision

<img width="1915" height="718" alt="Screenshot 2025-10-19 at 12 55 07 PM" src="https://github.com/user-attachments/assets/bee31c4e-560e-4b55-a272-fcb052cf2f5a" />

Select the subscription and Plan

<img width="788" height="364" alt="Screenshot 2025-10-19 at 12 56 15 PM" src="https://github.com/user-attachments/assets/6c83e537-8716-48db-a591-9f74b9b9c8a7" />

Select the Region and Trier as below, Review and Create. 

<img width="788" height="706" alt="Screenshot 2025-10-19 at 12 57 24 PM" src="https://github.com/user-attachments/assets/ba66649d-449f-45a8-9d1f-c3a031acdad6" />

Under Resource Maangemnt page, select the Computer Vision resource just created and select Manage Keys -> Select Keys and Endpoints. 

<img width="1017" height="587" alt="Screenshot 2025-10-19 at 1 00 06 PM" src="https://github.com/user-attachments/assets/bd36b3b4-da90-4278-9876-02be26fd02f1" />


**Develop an image analysis app with the Azure AI Vision SDK**

In this exercise, we’ll complete a partially implemented client application that uses the Azure AI Vision SDK to analyze images.

use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell. Use Poweshell

<img width="772" height="249" alt="Screenshot 2025-10-19 at 11 28 17 AM" src="https://github.com/user-attachments/assets/141e7130-df8a-4d2c-904e-10d7479475bf" />

Ensure you've switched to the classic version of the cloud shell before continuing.

<img width="1205" height="362" alt="Screenshot 2025-10-19 at 11 29 35 AM" src="https://github.com/user-attachments/assets/58beaa7a-f1b2-40f2-a1a5-1052a0ea4657" />

In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for the Computer Vision exercise in Python

```
 rm -r mslearn-ai-vision -f
 git clone https://github.com/MicrosoftLearning/mslearn-ai-vision
```
Use the following command to navigate to and view the folder containing the application code files:

```
cd mslearn-ai-vision/Labfiles/analyze-images/python/image-analysis
ls -a -l
```

Install the Azure AI Vision SDK package and other required packages by running the following commands:

```
python -m venv labenv
./labenv/bin/Activate.ps1
pip install -r requirements.txt azure-ai-vision-imageanalysis==1.0.0
```

Enter the following command to edit the configuration file for the app:
```
code .env
```
The file is opened in a code editor.

In the code file, update the configuration values it contains to reflect the endpoint and an authentication key for your Computer Vision resource (copied from its Keys and Endpoint page in the Azure portal).
After you’ve replaced the placeholders, use the CTRL+S command to save your changes and then use the CTRL+Q command to close the code editor while keeping the cloud shell command line open.

**Add code to suggest a caption**

1. enter the following command to open the code file for the client application
```
code image-analysis.py
```

2. In the code file, add the following code to import the namespaces you will need to use the Azure AI Vision SDK:

```
# import namespaces
from azure.ai.vision.imageanalysis import ImageAnalysisClient
from azure.ai.vision.imageanalysis.models import VisualFeatures
from azure.core.credentials import AzureKeyCredential
```

3.  Under `Authenticate Azure AI Vision` client, add the following code to create and authenticate a Azure AI Vision client object.
```
# Authenticate Azure AI Vision client
cv_client = ImageAnalysisClient(
     endpoint=ai_endpoint,
     credential=AzureKeyCredential(ai_key))

```

5. Under `Analyze image`, add the following code:
```
with open(image_file, "rb") as f:
     image_data = f.read()
print(f'\nAnalyzing {image_file}\n')

result = cv_client.analyze(
     image_data=image_data,
     visual_features=[
         VisualFeatures.CAPTION,
         VisualFeatures.DENSE_CAPTIONS,
         VisualFeatures.TAGS,
         VisualFeatures.OBJECTS,
         VisualFeatures.PEOPLE],
)
```

6. Under `Get image captions`, add the following code to display image captions and dense captions
```
# Get image captions
if result.caption is not None:
     print("\nCaption:")
     print(" Caption: '{}' (confidence: {:.2f}%)".format(result.caption.text, result.caption.confidence * 100))
    
if result.dense_captions is not None:
     print("\nDense Captions:")
     for caption in result.dense_captions.list:
         print(" Caption: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))
```

7. Save the file enter the following command to run the program with the argument images/street.jpg:
```
python image-analysis.py images/street.jpg
```

8. The output for the below street.jpg image should include a suggested caption:

<img width="403" height="267" alt="Screenshot 2025-10-19 at 12 51 26 PM" src="https://github.com/user-attachments/assets/3204b2ac-d292-4f4b-974f-0dc8388d48a3" />

9. Observe the below captions generated for the image and the confidence level.

<img width="897" height="274" alt="Screenshot 2025-10-19 at 12 53 39 PM" src="https://github.com/user-attachments/assets/c2554c19-e4e4-4b47-afda-4eac0af665a2" />







