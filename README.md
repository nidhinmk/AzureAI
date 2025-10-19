# AzureAI

**Computer Vision**

Login to Microsoft Azure portal (https://portal.azure.com) and create a resource. 

<img width="1508" height="513" alt="Screenshot 2025-10-19 at 8 49 06 AM" src="https://github.com/user-attachments/assets/9cb19d34-f93a-4c51-8aca-9fa9cd1efeb5" />

Search for Computer Vision

<img width="1512" height="699" alt="Screenshot 2025-10-19 at 8 51 09 AM" src="https://github.com/user-attachments/assets/40559ce5-c3ca-4b70-8c8c-43c82852fceb" />

Select the subscription and Plan

<img width="1510" height="454" alt="Screenshot 2025-10-19 at 8 52 10 AM" src="https://github.com/user-attachments/assets/a097e2d8-9897-442d-b6ce-1716730bc277" />

Select the Region and Trier as below. 

<img width="1506" height="763" alt="Screenshot 2025-10-19 at 8 58 02 AM" src="https://github.com/user-attachments/assets/be059309-5bea-4069-953c-f6dcf72d432f" />

Review and Create

<img width="647" height="816" alt="Screenshot 2025-10-19 at 8 59 34 AM" src="https://github.com/user-attachments/assets/93847f3b-9bc2-4a85-ada9-e583d0be7a1f" />

Deployment in progress

<img width="1512" height="558" alt="Screenshot 2025-10-19 at 9 00 13 AM" src="https://github.com/user-attachments/assets/67291bf4-4051-4fcb-91dd-4bb51d86dbf9" />

Deployment Complete

<img width="1507" height="699" alt="Screenshot 2025-10-19 at 9 00 41 AM" src="https://github.com/user-attachments/assets/49446eaa-4307-40e4-8145-9b120bc1dba7" />

Under Resource Maangemnt page, select the Computer Vision resource just created. 

<img width="1512" height="656" alt="Screenshot 2025-10-19 at 9 06 08 AM" src="https://github.com/user-attachments/assets/a18500d7-a208-4e52-a071-a902e67bd983" />

Select the Manage Keys and select Keys and Endpoints

<img width="1100" height="583" alt="Screenshot 2025-10-19 at 9 08 22 AM" src="https://github.com/user-attachments/assets/9affed95-5d31-49e4-844a-050c46f0557d" />


Develop an image analysis app with the Azure AI Vision SDK

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

8. 










