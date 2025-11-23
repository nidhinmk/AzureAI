# Build a Conversational language understanding model

In this module, we'll learn how to use the service to create a natural language understanding app using Azure Language. [Code](/clock-client.py)

## Exercise - Create a language understanding model with the Language service

The Azure AI Language service enables us to define a conversational language understanding model that applications can use to interpret natural language utterances from users (text or spoken input), predict the users intent (what they want to achieve), and identify any entities to which the intent should be applied.

For example, a conversational language model for a clock application might be expected to process input such as:

What is the time in London?

This kind of input is an example of an utterance (something a user might say or type), for which the desired intent is to get the time in a specific location (an entity); in this case, London.

In this exercise, you'll use the Azure AI Language service to create a conversational language understand model, and use the Python SDK to implement a client app that uses it.

## Provision an Azure AI Language resource

1. Open the Azure portal at `https://portal.azure.com`, and sign in using the Microsoft account.

2. Create a resource; search for Language service. Then, in the results, select Create under Language Service

  
  <img width="977" height="690" alt="Screenshot 2025-11-23 at 9 40 15 AM" src="https://github.com/user-attachments/assets/83ffec75-6745-4eed-8e8f-14acdc24c6b2" />

  <img width="753" height="813" alt="Screenshot 2025-11-23 at 9 42 21 AM" src="https://github.com/user-attachments/assets/2c22cd05-b4d6-4022-8732-279188561477" />


## Create a conversational language understanding project

1. Open the Azure AI Language Studio portal at https://language.cognitive.azure.com/ and sign in using the Microsoft account.

2. Choose the below Language resource

  <img width="1291" height="872" alt="Screenshot 2025-11-23 at 9 46 02 AM" src="https://github.com/user-attachments/assets/74fff9d3-6e46-48ca-a7ed-b163c0d840c0" />

3. At the top of the portal, in the Create new menu, select Conversational language understanding.

4. In the Create a project dialog box, on the Enter basic information page, enter the following details and then select Next

  <img width="856" height="662" alt="Screenshot 2025-11-23 at 9 48 24 AM" src="https://github.com/user-attachments/assets/0137765b-d56f-45c9-9759-6b11ef669a82" />

6. On the Review and finish page, select Create.

### Create intents

The first thing we'll do in the new project is to define some intents. The model will ultimately predict which of these intents a user is requesting when submitting a natural language utterance.

1. On the Schema definition page, on the Intents tab, select ＋ Add to add a new intent named `GetTime`.

2. Verify that the GetTime intent is listed (along with the default None intent). Then add the following additional intents:
    - GetDay
    - GetDate
  
  <img width="1146" height="594" alt="Screenshot 2025-11-23 at 9 52 15 AM" src="https://github.com/user-attachments/assets/0eb76f7e-cd51-495f-8030-38d17f934f10" />

### Label each intent with sample utterances

To help the model predict which intent a user is requesting, we must label each intent with some sample utterances.

1. In the pane on the left, select the Data Labeling page.

2. Select the new GetTime intent and enter the utterance `what is the time?`. This adds the utterance as sample input for the intent.

3. Add the following additional utterances for the GetTime intent:

    - what's the time?
    - what time is it?
    - tell me the time

4. Select the GetDay intent and add the following utterances as example input for that intent:

    - what day is it?
    - what's the day?
    - what is the day today?
    - what day of the week is it?
  
5. Select the GetDate intent and add the following utterances for it:

    - what date is it?
    - what's the date?
    - what is the date today?
    - what's today's date?
  
6. After we've added utterances for each of your intents, select Save changes.

  <img width="1261" height="854" alt="Screenshot 2025-11-23 at 9 56 37 AM" src="https://github.com/user-attachments/assets/1943cd86-e8ba-4e3a-b302-5451c1669ba8" />

### Train and test the model

Now that we've added some intents, we'll train the language model and see if it can correctly predict them from user input.

1. In the pane on the left, select Training jobs. Then select + Start a training job.

2. On the Start a training job dialog, select the option to train a new model, name it `Clock`. Select Standard training mode and the default Data splitting options.

3. To begin the process of training your model, select Train.

  <img width="1499" height="865" alt="Screenshot 2025-11-23 at 9 58 25 AM" src="https://github.com/user-attachments/assets/51b1a3e4-601c-47bb-bd43-0fbd309d560d" />

4. When training is complete (which may take several minutes) the job Status will change to Training succeeded.

5. Select the Model performance page, and then select the Clock model. Review the overall and per-intent evaluation metrics (precision, recall, and F1 score) and the confusion matrix generated by the evaluation that was performed when training.

  <img width="1461" height="851" alt="Screenshot 2025-11-23 at 10 02 32 AM" src="https://github.com/user-attachments/assets/01a4c34e-b885-4cee-bc10-ec2224a9e7a1" />

6. Go to the Deploying a model page, then select Add deployment.

7. On the Add deployment dialog, select Create a new deployment name, and then enter `production`

8. Select the Clock model in the Model field then select Deploy

  <img width="729" height="579" alt="Screenshot 2025-11-23 at 10 03 30 AM" src="https://github.com/user-attachments/assets/7019161e-01bc-42fe-a5fc-1d04d3d20756" />

9. Select the Testing deployments page, then select the production deployment in the Deployment name field.

10. Enter the following text in the empty textbox, and then select Run the test:

        what's the time now?

    Review the result that is returned, noting that it includes the predicted intent (which should be GetTime) and a confidence score that indicates the probability the model calculated for the predicted intent. The JSON tab shows the comparative confidence for each potential intent (the one with the highest confidence score is the predicted intent)

  <img width="1308" height="854" alt="Screenshot 2025-11-23 at 10 05 25 AM" src="https://github.com/user-attachments/assets/94ff5a8a-f5dc-4969-81ce-7173040a59a8" />

11. Clear the text box, and then run another test with the following text:

        tell me the time

    Again, review the predicted intent and confidence score.

12. Try the following text:

        what's the day today?

    The model predicts the GetDay intent.

  <img width="1178" height="873" alt="Screenshot 2025-11-23 at 10 07 57 AM" src="https://github.com/user-attachments/assets/2a0284a6-f69b-48db-ad2f-27f31cfd10c6" />

## Add entities

Most real applications include more complex utterances from which specific data entities must be extracted to get more context for the intent.

### Add a learned entity

The most common kind of entity is a learned entity, in which the model learns to identify entity values based on examples.

1. In Language Studio, return to the Schema definition page and then on the Entities tab, select ＋ Add to add a new entity.

2. In the Add an entity dialog box, enter the entity name `Location` and ensure that the Learned tab is selected. Then select Add entity.

  <img width="751" height="796" alt="Screenshot 2025-11-23 at 10 14 08 AM" src="https://github.com/user-attachments/assets/1bba5ab2-8e2d-470b-8eab-0a5e5a257264" />

3. After the Location entity has been created, return to the Data labeling page.

4. Select the GetTime intent and enter the following new example utterance:

        what time is it in Vancouver?

5. When the utterance has been added, select the word Vancouver, and in the drop-down list that appears, select Location to indicate that "Vancouver" is an example of a location.

  <img width="1173" height="837" alt="Screenshot 2025-11-23 at 10 17 30 AM" src="https://github.com/user-attachments/assets/00f16350-0142-4f4f-8242-975eb6325fb5" />


6. Add another example utterance for the GetTime intent:

        Tell me the time in Paris?

7. When the utterance has been added, select the word Paris, and map it to the Location entity.

8. Add another example utterance for the GetTime intent:

        what's the time in New York?
9. When the utterance has been added, select the words New York, and map them to the Location entity.

10. Select Save changes to save the new utterances.

  <img width="899" height="501" alt="Screenshot 2025-11-23 at 10 19 31 AM" src="https://github.com/user-attachments/assets/fef9e4d0-426d-41f3-829e-479e13e331d3" />

### Add a list entity

In some cases, valid values for an entity can be restricted to a list of specific terms and synonyms; which can help the app identify instances of the entity in utterances.

1. In Language Studio, return to the Schema definition page and then on the Entities tab, select ＋ Add to add a new entity.

2. In the Add an entity dialog box, enter the entity name `Weekday` and select the List entity tab. Then select Add entity.

  <img width="747" height="776" alt="Screenshot 2025-11-23 at 10 20 51 AM" src="https://github.com/user-attachments/assets/5f234b76-8c13-465c-8b9a-9e4ddbcc8191" />

3. On the page for the Weekday entity, in the Learned section, ensure Not required is selected. Then, in the List section, select ＋ Add new list. Then enter the following value and synonym and select Save:

        List key	synonyms
        Sunday	  Sun
   
4. Repeat the previous step to add the following list components:

        Value	    synonyms
        Monday	  Mon
        Tuesday	  Tue, Tues
        Wednesday	Wed, Weds
        Thursday	Thur, Thurs
        Friday	  Fri
        Saturday	Sat

  <img width="843" height="561" alt="Screenshot 2025-11-23 at 10 36 32 AM" src="https://github.com/user-attachments/assets/878391dc-9098-43c6-a955-8a6d85674669" />

5. Return to the Data labeling page; Select the GetDate intent and enter the following new example utterance:

        what date was it on Saturday?

6. When the utterance has been added, select the word Saturday, and in the drop-down list that appears, select Weekday.

7. Add another example utterance for the GetDate intent:

        what date will it be on Friday?
8. When the utterance has been added, map Friday to the Weekday entity.

9. Add another example utterance for the GetDate intent:

        what will the date be on Thurs?
10. When the utterance has been added, map Thurs to the Weekday entity.

11. select Save changes to save the new utterances.

  <img width="912" height="531" alt="Screenshot 2025-11-23 at 10 39 28 AM" src="https://github.com/user-attachments/assets/4be44b09-f142-4f6d-9279-b95107bc7267" />

### Add a prebuilt entity

The Azure AI Language service provides a set of prebuilt entities that are commonly used in conversational applications.

1. In Language Studio, return to the Schema definition page and then on the Entities tab, select ＋ Add to add a new entity.

2. In the Add an entity dialog box, enter the entity name `Date` and select the Prebuilt entity tab. Then select Add entity.

  <img width="752" height="769" alt="Screenshot 2025-11-23 at 10 48 01 AM" src="https://github.com/user-attachments/assets/a3e03ada-1ac8-40c2-8651-ac8e68b976bb" />

3. On the page for the Date entity, in the Learned section, ensure Not required is selected. Then, in the Prebuilt section, select ＋ Add new prebuilt.

4. In the Select prebuilt list, select DateTime and then select Save.

5. After adding the prebuilt entity, return to the Data labeling page

6. Select the GetDay intent and enter the following new example utterance:

        what day was 01/01/1901?

7. When the utterance has been added, select 01/01/1901, and in the drop-down list that appears, select Date.

8. Add another example utterance for the GetDay intent:

        what day will it be on Nov 24th 2025?
9. When the utterance has been added, map Dec 31st 2099 to the Date entity.

  <img width="901" height="304" alt="Screenshot 2025-11-23 at 11 00 08 AM" src="https://github.com/user-attachments/assets/103014a0-75db-4506-b26e-520f89f411b7" />

10. Select Save changes to save the new utterances.

### Retrain the model

Now that you've modified the schema, you need to retrain and retest the model.

1. On the Training jobs page, select Start a training job.

2. On the Start a training job dialog, select overwrite an existing model and specify the Clock model. Select Train to train the model. If prompted, confirm you want to overwrite the existing model.

3. When training is complete the job Status will update to Training succeeded.

4. Select the Model performance page and then select the Clock model. Review the evaluation metrics (precision, recall, and F1 score) and the confusion matrix generated by the evaluation that was performed when training (note that due to the small number of sample utterances, not all intents may be included in the results).

  <img width="1480" height="848" alt="Screenshot 2025-11-23 at 11 02 04 AM" src="https://github.com/user-attachments/assets/861ff1a0-ebc3-480b-908c-e9574d08d316" />

5. On the Deploying a model page, select Add deployment.

6. On the Add deployment dialog, select Override an existing deployment name, and then select production.

7. Select the Clock model in the Model field and then select Deploy to deploy it. This may take some time.

8. When the model is deployed, on the Testing deployments page, select the production deployment under the Deployment name field, and then test it with the following text:

        what's the time in Vancovuer?
9. Review the result that is returned, which should hopefully predict the GetTime intent and a Location entity with the text value "Vancouver".

<img width="666" height="757" alt="Screenshot 2025-11-23 at 11 04 31 AM" src="https://github.com/user-attachments/assets/34f8af33-b28b-4e0b-9a1a-6e23afe713ec" />

<img width="489" height="764" alt="Screenshot 2025-11-23 at 11 04 45 AM" src="https://github.com/user-attachments/assets/4cf8ae00-445b-47ce-932d-3a339c54123c" />

10. Try testing the following utterances:

        what time is it in Tokyo?
        
        what date is it on Friday?
        
        what's the date on Weds?
        
        what day was 01/01/2020?
        
        what day will Mar 7th 2030 be?

## Use the model from a client app

### Prepare to develop an app in Cloud Shell

1. In the Azure Portal, use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal.

2. In the Settings menu, select Go to Classic version

3. Enter the following commands to clone the GitHub repo for this exercise

        rm -r mslearn-ai-language -f
        git clone https://github.com/microsoftlearning/mslearn-ai-language

4. Navigate to the folder containing the application code files

        cd mslearn-ai-language/Labfiles/03-language/Python/clock-client

### Configure the application

1. Run the following command to view the code files in the clock-client folder

        ls -a -l

2. Create a Python virtual environment and install the Azure AI Language Conversations SDK package and other required packages by running the following command

        python -m venv labenv
        ./labenv/bin/Activate.ps1
        pip install -r requirements.txt azure-ai-language-conversations==1.1.0

  <img width="1489" height="813" alt="Screenshot 2025-11-23 at 11 08 39 AM" src="https://github.com/user-attachments/assets/ecea55f0-dfcd-4e3d-910c-e03f3d0232a9" />


3. Edit the configuration file

        code .env

4. Update the configuration values to include the endpoint and a key from the Azure Language resource we created

5. Use the CTRL+S command or Right-click > Save to save your changes and then use the CTRL+Q command or Right-click > Quit to close the code editor

### Add code to the application

1. Edit the application code file.

        code clock-client.py

2. Import the namespaces we will need to use the AI Language Conversations SDK

        # Import namespaces
        from azure.core.credentials import AzureKeyCredential
        from azure.ai.language.conversations import ConversationAnalysisClient

3. Create a conversation analysis client for the AI Language service

        # Create a client for the Language service model
        client = ConversationAnalysisClient(
            ls_prediction_endpoint, AzureKeyCredential(ls_prediction_key))
    
4. Call the Language service model to get intent and entities

        # Call the Language service model to get intent and entities
          cls_project = 'Clock'
          deployment_slot = 'production'
          
          with client:
              query = userText
              result = client.analyze_conversation(
                  task={
                      "kind": "Conversation",
                      "analysisInput": {
                          "conversationItem": {
                              "participantId": "1",
                              "id": "1",
                              "modality": "text",
                              "language": "en",
                              "text": query
                          },
                          "isLoggingEnabled": False
                      },
                      "parameters": {
                          "projectName": cls_project,
                          "deploymentName": deployment_slot,
                          "verbose": True
                      }
                  }
              )
          
          top_intent = result["result"]["prediction"]["topIntent"]
          entities = result["result"]["prediction"]["entities"]
          
          print("view top intent:")
          print("\ttop intent: {}".format(result["result"]["prediction"]["topIntent"]))
          print("\tcategory: {}".format(result["result"]["prediction"]["intents"][0]["category"]))
          print("\tconfidence score: {}\n".format(result["result"]["prediction"]["intents"][0]["confidenceScore"]))
          
          print("view entities:")
          for entity in entities:
              print("\tcategory: {}".format(entity["category"]))
              print("\ttext: {}".format(entity["text"]))
              print("\tconfidence score: {}".format(entity["confidenceScore"]))
          
          print("query: {}".format(result["result"]["query"]))

The call to the conversational understanding model returns a prediction/result, which includes the top (most likely) intent as well as any entities that were detected in the input utterance. The client application must now use that prediction to determine and perform the appropriate action.

5. Apply the appropriate action, code checks for intents supported by the application (GetTime, GetDate, and GetDay) and determines if any relevant entities have been detected, before calling an existing function to produce an appropriate response.

        # Apply the appropriate action
        if top_intent == 'GetTime':
            location = 'local'
            # Check for entities
            if len(entities) > 0:
                # Check for a location entity
                for entity in entities:
                    if 'Location' == entity["category"]:
                        # ML entities are strings, get the first one
                        location = entity["text"]
            # Get the time for the specified location
            print(GetTime(location))
        
        elif top_intent == 'GetDay':
            date_string = date.today().strftime("%m/%d/%Y")
            # Check for entities
            if len(entities) > 0:
                # Check for a Date entity
                for entity in entities:
                    if 'Date' == entity["category"]:
                        # Regex entities are strings, get the first one
                        date_string = entity["text"]
            # Get the day for the specified date
            print(GetDay(date_string))
        
        elif top_intent == 'GetDate':
            day = 'today'
            # Check for entities
            if len(entities) > 0:
                # Check for a Weekday entity
                for entity in entities:
                    if 'Weekday' == entity["category"]:
                    # List entities are lists
                        day = entity["text"]
            # Get the date for the specified day
            print(GetDate(day))
        
        else:
            # Some other intent (for example, "None") was predicted
            print('Try asking me for the time, the day, or the date.')
    
6. Run the program

        python clock-client.py

7. When prompted, enter utterances to test the application. For example, try:

        Hello
        
        What time is it?
        
        What's the time in London?
        
        What's the date?
        
        What date is Sunday?
        
        What day is it?
        
        What day is 01/01/2025?

<img width="1503" height="862" alt="Screenshot 2025-11-23 at 11 15 00 AM" src="https://github.com/user-attachments/assets/b8967c2d-172a-416f-b0b2-e84d24cdc2b1" />

## Clean up resources

On the resource page, select Delete and follow the instructions to delete the resource.
