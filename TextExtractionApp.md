## Develop a text extraction app with the Azure AI Vision SDK

This exercise will complete a partially implemented client application that uses the Azure AI Vision SDK to extract text from images.

1. Prepare the application configuration
    Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment with no storage in your subscription
2. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files
   ```
    rm -r mslearn-ai-vision -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-vision
   ```
3. Use the following command to navigate to the application code files:
    ```
    cd mslearn-ai-vision/Labfiles/ocr/python/read-text
    ls -a -l
    ```
    <img width="792" height="265" alt="Screenshot 2025-10-20 at 10 33 19 AM" src="https://github.com/user-attachments/assets/2933c88d-f49c-43c6-82b5-31f7b3926bf7" />

4. Install the Azure AI Vision SDK package and other required packages by running the following commands:
    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt azure-ai-vision-imageanalysis==1.0.0
    ```
    <img width="958" height="265" alt="Screenshot 2025-10-20 at 10 35 31 AM" src="https://github.com/user-attachments/assets/f49f6655-b49e-452e-b5a0-d3be877fc876" />

5. Enter the following command to edit the configuration file for your app:
    ```
    code .env
    ```
6. In the code file, update the configuration values it contains to reflect the endpoint and an authentication key for your Computer Vision resource
    (copied from its Keys and Endpoint page in the Azure portal). Save (Ctrl+S) and Quit (Ctrl+Q)
  
8. Open the code file for the client application:
    ```
    code read-text.py
    ```
10. Add the following code to import the namespaces you will need to use the Azure AI Vision SDK
    ```
    # import namespaces
    from azure.ai.vision.imageanalysis import ImageAnalysisClient
    from azure.ai.vision.imageanalysis.models import VisualFeatures
    from azure.core.credentials import AzureKeyCredential
    ```
11. Add the following language-specific code to create and authenticate an Azure AI Vision Image Analysis client object
    ```
    # Authenticate Azure AI Vision client
    cv_client = ImageAnalysisClient(
         endpoint=ai_endpoint,
         credential=AzureKeyCredential(ai_key))
    ```
12. Add the following code to use the Image Analysis client to read the text in the image
    ```
    # Read text in image
    with open(image_file, "rb") as f:
         image_data = f.read()
    print (f"\nReading text in {image_file}")
    
    result = cv_client.analyze(
         image_data=image_data,
         visual_features=[VisualFeatures.READ])
    ```
13. Add the following code (including the final comment) to print the lines of text that were found and call a
      function to annotate them in the image (using the bounding_polygon returned for each line of text); Save the file (Ctrl+S)
    ```
    # Print the text
    if result.read is not None:
         print("\nText:")
    
     for line in result.read.blocks[0].lines:
         print(f" {line.text}")        
     # Annotate the text in the image
     annotate_lines(image_file, result.read)

     # Find individual words in each line
        
    ```
14. Enter the following command to run the program. 
    ```
    python read-text.py images/Lincoln.jpg
    ```
    <img width="843" height="724" alt="Screenshot 2025-10-20 at 11 42 34 AM" src="https://github.com/user-attachments/assets/39ba4feb-e3fe-4903-a6b8-01e941c4c88c" />

  
15. The program reads the text in the specified image file (images/Lincoln.jpg), which looks like this:
    
    <img width="503" height="641" alt="Screenshot 2025-10-20 at 11 40 34 AM" src="https://github.com/user-attachments/assets/4bd2486f-5fa2-413a-9027-32e08857618b" />

16. In the read-text folder, a lines.jpg image has been created. Use the (Azure cloud shell-specific) download command to download it:
    ```
    download lines.jpg
    ```
    <img width="789" height="346" alt="Screenshot 2025-10-20 at 11 49 40 AM" src="https://github.com/user-attachments/assets/5606e2bb-41e9-40ff-9a38-32dcc92af800" />

17. The download command creates a popup link at the bottom right of your browser, which you can select to download and open the file. The image should look simlar to this:
    
    <img width="1512" height="836" alt="Screenshot 2025-10-20 at 11 51 59 AM" src="https://github.com/user-attachments/assets/36857b7f-a35e-4020-bb97-21ffc5ace6d0" />

18. Run the program one more time, this time specifying the parameter images/Note.jpg to extract text from this image:
    <img width="503" height="377" alt="Screenshot 2025-10-20 at 11 56 27 AM" src="https://github.com/user-attachments/assets/d4724124-61e1-4d26-81f2-c930571991d3" />

19. Run the below code
    ```
    python read-text.py images/Note.jpg
    ```
20. Download and view the resulting lines.jpg file:
    ```
    download lines.jpg
    ```
    <img width="1504" height="736" alt="Screenshot 2025-10-20 at 11 58 51 AM" src="https://github.com/user-attachments/assets/2b090f66-c997-4384-874d-8e85e8d704cc" />

21. Add code to return the position of individual words

    ```
    # Find individual words in each line
    print ("\nIndividual words:")
    for line in result.read.blocks[0].lines:
         for word in line.words:
             print(f"  {word.text} (Confidence: {word.confidence:.2f}%)")
    # Annotate the words in the image
    annotate_words(image_file, result.read)
    ```
22. Rerun the program to extract text from images/Lincoln.jpg. Observe the output, which should include each individual word in the image, and the confidence associated with their prediction.
    In the read-text folder, a words.jpg image has been created. Use the (Azure cloud shell-specific) download command to download and view it:

    <img width="1508" height="825" alt="Screenshot 2025-10-20 at 12 05 23 PM" src="https://github.com/user-attachments/assets/71fbc86d-a0f5-4094-93c7-fea40e88b2b1" />

24. Clean up the resources to avoid additional costs. 

    

    
