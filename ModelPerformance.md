# Evaluate generative AI model performance

## Create an Azure AI Foundry hub and project

1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using the Azure credentials

2. Navigate to `https://ai.azure.com/managementCenter/allResources` and select Create new. Then choose the option to create a new AI hub resource.

3. In the Create a project wizard, enter a valid name for your project, and select the option to create a new hub. Then use the Rename hub link
   to specify a valid name for your new hub, expand Advanced options, and specify the following settings for your project:

   <img width="509" height="576" alt="Screenshot 2025-10-28 at 10 43 01 AM" src="https://github.com/user-attachments/assets/c25ee113-455f-46c0-80fa-c20cad2fcf3a" />

## Deploy models

In this exercise, we’ll evaluate the performance of a gpt-4o-mini model. We’ll also use a gpt-4o model to generate AI-assisted evaluation metrics.

1. In the navigation pane on the left for your project, in the My assets section, select the Models + endpoints page.

2. In the Models + endpoints page, in the Model deployments tab, in the + Deploy model menu, select Deploy base model.

3. Search for the gpt-4o model in the list, and then select and confirm it.

4. Deploy the model with the following settings by selecting Customize in the deployment details:

    <img width="1491" height="837" alt="Screenshot 2025-10-28 at 10 48 38 AM" src="https://github.com/user-attachments/assets/f5b9f738-b47a-4e78-8b6e-69af55f7cd4f" />

5. Return to the Models + endpoints page and repeat the previous steps to deploy a gpt-4o-mini model with the same settings.

    <img width="516" height="735" alt="Screenshot 2025-10-28 at 10 51 44 AM" src="https://github.com/user-attachments/assets/0445d214-f3c2-41c9-82ca-1f46b63f2425" />

## Manually evaluate a model

1. Download the travel_evaluation_data.jsonl from and save it in a local folder as `travel_evaluation_data.jsonl`

  `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl`

2. In the navigation pane, in the Protect and govern section, select Evaluation.

3. In the Evaluation page, view the Manual evaluations tab and select + New manual evaluation.

4. In the Configurations section, in the Model list, select your gpt-4o model deployment.

5. Change the System message to the following instructions for an AI travel assistant:

  `Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.`

6. In the Manual evaluation result section, select Import test data and upload the travel_evaluation_data.jsonl file we
   downloaded previously; scrolling down to map the dataset fields as follows:

    <img width="817" height="549" alt="Screenshot 2025-10-28 at 10 58 27 AM" src="https://github.com/user-attachments/assets/b20d746e-1638-49f1-a1e7-187bf41a9d41" />
    
7. Review the questions and expected answers in the test file - we’ll use these to evaluate the responses that the model generates.

8. Select Run from the top bar to generate outputs for all questions you added as inputs. After a few minutes, the responses from the model
    Should be shown in a new Output column, like this:

   <img width="805" height="593" alt="Screenshot 2025-10-28 at 1 51 05 PM" src="https://github.com/user-attachments/assets/65a70c9b-37be-4e7f-a0e0-cb6b2685d4d5" />

9. Review the outputs for each question, comparing the output from the model to the expected answer and “scoring” the results by selecting the
    thumbs up or down icon at the bottom right of each response.

11. After you’ve scored the responses, review the summary tiles above the list. Then in the toolbar, select Save results and assign a suitable name.
    Saving results enables you to retrieve them later for further evaluation or comparison with a different model.

## Use automated evaluation

Automated evaluation is an approach that attempts to address these shortcomings of manual evaluation by calculating metrics and using AI to 
assess responses for coherence, relevance, and other factors.

1. Use the back arrow (←) next to the Manual evaluation page title to return to the Evaluation page.

2. View the Automated evaluations tab;  Select Create a new evaluation, and when prompted, select the option to evaluate a Evaluate a model
   and select Next.
   
3. On the Select data source page, select Use your dataset and select the travel_evaluation_data_jsonl_xxxx… dataset based on the file you
   uploaded previously, and select Next.

4. On the Test your model page, select the gpt-4o-mini model and change the System message to the same instructions for an AI travel assistant
   you used previously:
   ```Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.```
5. For the query field, select {{item.question}}.

    <img width="1364" height="859" alt="Screenshot 2025-10-28 at 2 00 13 PM" src="https://github.com/user-attachments/assets/8f57b7c3-c277-4fe1-a482-539b54806ff3" />

6. On the Configure evaluators page, use the +Add button to add the following evaluators, configuring each one as follows:

    Model scorer:
      Criteria name: Select the Semantic_similarity preset
      Grade with: Select your gpt-4o model
      User settings (at the bottom):
      
      Output: {{sample.output_text}}
      Ground Truth: {{item.ExpectedResponse}}

    Likert-scale evaluator:
      Criteria name: Select the Relevance preset
      Grade with: Select your gpt-4o model
      Query: {{item.question}}

   Text similarity:
      Criteria name: Select the F1_Score preset
      Ground truth: {{item.ExpectedResponse}}

   Hateful and unfair content:
      Criteria name: Hate_and_unfairness
      Query: {{item.question}}

   <img width="1501" height="853" alt="Screenshot 2025-10-28 at 2 07 00 PM" src="https://github.com/user-attachments/assets/3a2001a8-34a0-4dd5-99b6-fa4861d0c75e" />

8. Select Next and review your evaluation settings. You should have configured the evaluation to use the travel evaluation dataset
   to evaluate the gpt-4o-mini model for semantic similarity, relevance, F1 score, and hateful and unfair language.

9. When the evaluation has completed, scroll down if necessary to review the results.

    <img width="844" height="553" alt="Screenshot 2025-10-28 at 2 14 22 PM" src="https://github.com/user-attachments/assets/5f50a4b7-497a-4af2-84aa-6ed66ff0318e" />


