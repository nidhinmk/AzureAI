# Fine Tuning a Language Model

When we want to develop a chat application with Azure AI Foundry, we can use prompt flow to create a chat application that is integrated with 
a language model to generate responses. To improve the quality of the responses the model generates, one can try various strategies. 

The easiest strategy is to apply prompt engineering. We can change the way we format our question, but we can also update the system 
message that is sent along with the prompt to the language model.Prompt engineering is a quick and easy way to improve how the model acts, 
and what the model needs to know. When we want to improve the quality of the model even further, there are two common techniques that are used:

- **Retrieval Augmented Generation (RAG):** Ground our data by first retrieving context from a data source before generating a response.
- **Fine-tuning:** Train a base language model on a dataset before integrating it in our application.

Within prompt engineering, a technique used to "force" the model to generate output in a specific format, is to provide the model with various 
examples of what the desired output might look like, also known as one-shot (one example) or few-shot (few examples).
To maximize the consistency of the model's behavior, we can fine-tune a base model with our own training data.

## Fine-tune a language model (Exercise)

When we want a language model to behave a certain way, we can use prompt engineering to define the desired behavior. 
When we want to improve the consistency of the desired behavior, we can opt to fine-tune a model, comparing it to the prompt engineering 
approach to evaluate which method best fits our needs.

### Deploy a model in an Azure AI Foundry project
1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using the Azure credentials.

2. In the Explore models and capabilities section, search for the `gpt-4o` model, and select Use this model.

3. Create the project as below

    <img width="641" height="672" alt="Screenshot 2025-10-26 at 7 04 28 AM" src="https://github.com/user-attachments/assets/98600af4-4809-491f-8cf7-a9a07e844130" />
  
4. Select Customize and specify the following settings for your project:

    <img width="1256" height="853" alt="Screenshot 2025-10-26 at 7 07 12 AM" src="https://github.com/user-attachments/assets/c5bcec74-08b9-4bab-9259-0e2621eecdcd" />

5. In the Setup pane, note the name of your model deployment; which should be gpt-4o. You can confirm this by viewing the deployment
   in the Models and endpoints page

6. Select Overview to see the main page for the project

   <img width="1502" height="856" alt="Screenshot 2025-10-26 at 7 10 23 AM" src="https://github.com/user-attachments/assets/516e1ba5-e9b7-4127-837c-bf2803737e84" />

### Fine-tune a model

1. Download the training dataset at
   `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`
   and save it as a JSONL file locally.

2. Navigate to the Fine-tuning page under the Build and customize section, using the menu on the left

3. Select the button to add a new fine-tune model, select the gpt-4o model and then select Next.

4. Fine-tune the model using the following configuration

    <img width="471" height="780" alt="Screenshot 2025-10-26 at 7 18 14 AM" src="https://github.com/user-attachments/assets/37cf934e-d832-44fe-a081-cc511f889cb5" />

5. Submit the fine-tuning details, and the job will start. It may take some time to complete.
   We can continue with the next section of the exercise while you wait.

### Chat with a base model

1. In the navigation page, select Playgrounds and open the Chat playground.

2. Verify the deployed gpt-4o base model is selected in setup pane.

3. In the chat window, enter the query `What can you do?` and view the response.

    <img width="1325" height="813" alt="Screenshot 2025-10-26 at 7 21 25 AM" src="https://github.com/user-attachments/assets/7c129d96-2a00-4ca2-b8cc-148b406ac595" />

4. Update the system message in the setup pane with the following prompt:

    `You are an AI assistant that helps people plan their travel.`
   
6. In the chat window, enter the query What can you do? again, and view the response. 1 As a response, the assistant may tell you that
   it can help you book flights, hotels and rental cars for your trip. We want to avoid this behavior.

   <img width="1315" height="789" alt="Screenshot 2025-10-26 at 7 24 14 AM" src="https://github.com/user-attachments/assets/65511477-673f-43a6-aa70-4a97b36f33b6" />

7. Update the system message again with a new prompt:
    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries,
    such as visa requirements, weather forecasts, local attractions, and cultural norms.
   You should not provide any hotel, flight, rental car or restaurant recommendations.
   Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
8. Continue testing your chat application to verify it doesn’t provide any information that isn’t grounded in retrieved data. For example, ask the following questions and review the model’s answers, paying particular attention to the tone and writing style that the model uses to respond:

        Where in Rome should I stay?
        
        I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?
        
        What are some local delicacies I should try?
        
        When is the best time of year to visit in terms of the weather?
        
        What's the best way to get around the city?

   
    <img width="782" height="544" alt="Screenshot 2025-10-26 at 7 26 34 AM" src="https://github.com/user-attachments/assets/e84ac303-74f9-4b4d-a6a7-e848a0ebdd77" />

### Review the training file

The base model seems to work well enough, but we are looking for a particular conversational style from our generative AI app. 
The training data used for fine-tuning offers us the chance to create explicit examples of the kinds of response we want.
The downloaded JSONL file will look like below

```
 {"messages": [
     {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
     {"role": "user", "content": "What's a must-see in Paris?"},
     {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
     ]}

```
### Deploy the fine-tuned model
1. Navigate to the Fine-tuning page under Build and customize to find your fine-tuning job and its status. Use the Refresh button in the
   fine-tuning page to refresh the view.

   It's alsmost an hour and its still running
   
     <img width="1300" height="686" alt="Screenshot 2025-10-26 at 8 40 40 AM" src="https://github.com/user-attachments/assets/93e0d140-8237-48f6-a133-95471b492e5e" />

3. Select the fine-tuning job link to open its details page. Then, select the Metrics tab and explore the fine-tune metrics.

     <img width="1507" height="860" alt="Screenshot 2025-10-26 at 8 45 15 AM" src="https://github.com/user-attachments/assets/02ca474a-03ce-47b9-a80e-7862b653b419" />

    <img width="1284" height="760" alt="Screenshot 2025-10-26 at 9 00 35 AM" src="https://github.com/user-attachments/assets/8219303b-c0ef-475a-980a-f53f49ae5d4c" />

     <img width="682" height="534" alt="Screenshot 2025-10-26 at 9 01 21 AM" src="https://github.com/user-attachments/assets/8598bd1c-75e9-434d-8872-c2b14c7150ac" />

4. Deploy the fine-tuned model with the following configurations
   
    - Deployment name: A valid name for your model deployment
    - Deployment type: Standard
    - Tokens per Minute Rate Limit (thousands): 50K (or the maximum available in your subscription if less than 50K)
    - Content filter: Default
      
5. Check the Provisioning state until it has succeeded (you may need to refresh the browser to see the updated status).

### Test the fine-tuned model
1. When the deployment is ready, navigate to the fine-tuned model and select Open in playground.
2. Ensure the system message includes these instructions:
    ```
     You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries,
    such as visa requirements, weather forecasts, local attractions, and cultural norms.
   You should not provide any hotel, flight, rental car or restaurant recommendations.
   Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
3. Test your fine-tuned model to assess whether its behavior is more consistent now.
   For example, ask the following questions again and explore the model’s answers:

        Where in Rome should I stay?
        
        I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?
        
        What are some local delicacies I should try?
        
        When is the best time of year to visit in terms of the weather?
        
        What's the best way to get around the city?
   
5. After reviewing the responses, we can see that the fine-tuned model is behaving way more advanced compared to the base model

### Clean up the reources
  - Navigate to the Azure portal at `https://portal.azure.com`.
  - In the Azure portal, on the Home page, select Resource groups.
  - Select the resource group that you created for this exercise.
  - At the top of the Overview page for the resource group, select Delete resource group.
  - Enter the resource group name to confirm you want to delete it, and select Delete.
