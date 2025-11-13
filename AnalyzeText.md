# Analyze text with Azure AI Language

Azure AI Language supports analysis of text, including language detection, sentiment analysis, key phrase extraction, and entity recognition. In this exercise, we'll use the Azure AI Language Python SDK for text analytics to implement a simple hotel review application based on this example. [Code](/text-analysis.py)

## Provision an Azure AI Language resource

1. Open the Azure portal at `https://portal.azure.com`

2. Create a resource.

  <img width="1903" height="715" alt="Screenshot 2025-11-12 at 6 59 33 PM" src="https://github.com/user-attachments/assets/379f5184-98ef-4478-bb62-844bacd6dee1" />

  <img width="924" height="962" alt="Screenshot 2025-11-12 at 7 00 41 PM" src="https://github.com/user-attachments/assets/5d21d0a5-f186-4c9c-8049-801d9ac14161" />

  <img width="791" height="955" alt="Screenshot 2025-11-12 at 7 02 26 PM" src="https://github.com/user-attachments/assets/44be9a94-8375-4867-9edb-c67404ebbba5" />

  
3. View the Keys and Endpoint page in the Resource Management section.

   <img width="1016" height="812" alt="Screenshot 2025-11-12 at 7 04 52 PM" src="https://github.com/user-attachments/assets/b727b129-c3e6-4d1f-958b-33867cbff368" />


## Clone the repository

1. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal.

2. In the Settings menu, select Go to Classic version.

3. Enter the following commands to clone the GitHub repo for this exercise

        rm -r mslearn-ai-language -f
        git clone https://github.com/microsoftlearning/mslearn-ai-language

4. Navigate to the folder containing the application code files.

        cd mslearn-ai-language/Labfiles/01-analyze-text/Python/text-analysis

## Configure your application

1. View the code files in the text-analysis folder

        ls -a -l

2. Create a Python virtual environment and install the Azure AI Language Text Analytics SDK package and other required packages by running the following command

        python -m venv labenv
        ./labenv/bin/Activate.ps1
        pip install -r requirements.txt azure-ai-textanalytics==5.3.0

  <img width="1409" height="680" alt="Screenshot 2025-11-12 at 7 09 09 PM" src="https://github.com/user-attachments/assets/3a0d5917-2476-4aa2-8dd7-fbc1ae10e72f" />

3. Edit the application configuration file

        code .env

4. Update the configuration values to include the endpoint and a key from the Azure Language resource we created earlier. Right click, Save and Quit

   <img width="1036" height="961" alt="Screenshot 2025-11-12 at 7 16 48 PM" src="https://github.com/user-attachments/assets/56a4b7d3-6628-4d68-965a-4936f18787e4" />


## Add code to connect to the Azure AI Language resource

1. Edit the application code file.

        code text-analysis.py

2. Import the namespaces we will need to use the Text Analytics SDK

        # import namespaces
        from azure.core.credentials import AzureKeyCredential
        from azure.ai.textanalytics import TextAnalyticsClient

3. Create client using endpoint and key

        # Create client using endpoint and key
        credential = AzureKeyCredential(ai_key)
        ai_client = TextAnalyticsClient(endpoint=ai_endpoint, credential=credential)

4. Save the file and run the program.

        python text-analysis.py

5. The application successfully creates a client for the Text Analytics API, but doesn't make use of it. We'll fix that in the next section.

   <img width="1918" height="883" alt="Screenshot 2025-11-12 at 7 20 30 PM" src="https://github.com/user-attachments/assets/95ccf273-ba43-4f1e-a1b0-0ed496de48f8" />

## Add code to detect language

1. Add the code necessary to detect the language in each review document

        # Get language
        detectedLanguage = ai_client.detect_language(documents=[text])[0]
        print('\nLanguage: {}'.format(detectedLanguage.primary_language.name))

2. Save and re-run the program. Note that this time the language for each review is identified

  <img width="1914" height="921" alt="Screenshot 2025-11-12 at 7 24 23 PM" src="https://github.com/user-attachments/assets/7835e162-3889-4ee3-a0df-05034d28b8bf" />

## Add code to evaluate sentiment

Sentiment analysis is a commonly used technique to classify text as positive or negative (or possible neutral or mixed). It's commonly used to analyze social media posts, product reviews, and other items where the sentiment of the text may provide useful insights.

1. Add the code necessary to detect the sentiment of each review document.

        # Get sentiment
        sentimentAnalysis = ai_client.analyze_sentiment(documents=[text])[0]
        print("\nSentiment: {}".format(sentimentAnalysis.sentiment))

2. Observe the output, note that the sentiment of the reviews is detected.

  <img width="1910" height="766" alt="Screenshot 2025-11-12 at 7 26 10 PM" src="https://github.com/user-attachments/assets/f454d73f-0aff-4b5f-8a62-68ceeb617a16" />

## Add code to identify key phrases

It can be useful to identify key phrases in a body of text to help determine the main topics that it discusses.

1. Add the code necessary to detect the key phrases in each review document.

        # Get key phrases
        phrases = ai_client.extract_key_phrases(documents=[text])[0].key_phrases
        if len(phrases) > 0:
            print("\nKey Phrases:")
            for phrase in phrases:
                print('\t{}'.format(phrase))

2. Observe the output, note that each document contains key phrases that give some insights into what the review is about

  <img width="1916" height="764" alt="Screenshot 2025-11-12 at 7 27 02 PM" src="https://github.com/user-attachments/assets/3f7945cf-a265-4936-a16f-37c712176a04" />

## Add code to extract entities

Often, documents or other bodies of text mention people, places, time periods, or other entities. The text Analytics API can detect multiple categories (and subcategories) of entity in the text

1. Add the code necessary to identify entities that are mentioned in each review.

        # Get entities
        entities = ai_client.recognize_entities(documents=[text])[0].entities
        if len(entities) > 0:
            print("\nEntities")
            for entity in entities:
                print('\t{} ({})'.format(entity.text, entity.category))
        
2. Observe the output, note the entities that have been detected in the text

  <img width="523" height="388" alt="Screenshot 2025-11-12 at 7 27 58 PM" src="https://github.com/user-attachments/assets/2f13be66-0d19-4a53-bd7b-5c85e99554d7" />

## Add code to extract linked entities

In addition to categorized entities, the Text Analytics API can detect entities for which there are known links to data sources, such as Wikipedia.

1. Add the code necessary to identify linked entities that are mentioned in each review.

        # Get linked entities
        entities = ai_client.recognize_linked_entities(documents=[text])[0].entities
        if len(entities) > 0:
            print("\nLinks")
            for linked_entity in entities:
                print('\t{} ({})'.format(linked_entity.name, linked_entity.url))
   
3. Observe the output, note the linked entities that are identified.

  <img width="919" height="204" alt="Screenshot 2025-11-12 at 7 28 56 PM" src="https://github.com/user-attachments/assets/fd3d990f-6b55-47a6-acb6-cbf5f64aff2a" />

## Clean up resources

Delete the resources you created in this exercise

1. Close the Azure cloud shell pane

2. In the Azure portal, select Delete and follow the instructions to delete the resource.

  <img width="1919" height="964" alt="Screenshot 2025-11-12 at 7 33 04 PM" src="https://github.com/user-attachments/assets/b0fecd09-68e0-41ca-9d66-1d493136f56f" />
