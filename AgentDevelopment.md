# Explore AI Agent development

## Create an Azure AI Foundry project and agent

1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using your Azure credentials.

2. Select Create an agent.

    <img width="510" height="546" alt="Screenshot 2025-10-28 at 6 33 34 PM" src="https://github.com/user-attachments/assets/bb614425-7411-4847-98f2-bd4c0a31126b" />

3. If prompted, deploy a gpt-4o model using the Global standard or Standard deployment type
   (depending on quota availability) and customize the deployment details to set a Tokens per minute rate
   limit of 50K (or the maximum available if less than 50K).

     <img width="506" height="489" alt="Screenshot 2025-10-28 at 6 36 53 PM" src="https://github.com/user-attachments/assets/2f353940-91cb-4348-a1f9-f01ea58f7222" />

4. When the project is created, the Agents playground will be opened automatically so we can select or deploy a model:

    <img width="993" height="956" alt="Screenshot 2025-10-28 at 6 39 07 PM" src="https://github.com/user-attachments/assets/a5189df1-6fa4-412f-8e02-fbb1f525f7ca" />

## Create our agent

In this exercise, you’ll build a simple agent that answers questions based on a corporate expenses policy.

1. Open another browser tab, and download Expenses_policy.docx from
    ```
    https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-agents/
    main/Labfiles/01-agent-fundamentals/Expenses_Policy.docx
    ```
2. In the Agent playground, set the Agent name to `ExpensesAgent`

3. Set the Instructions to

    ```
    You are an AI assistant for corporate expenses.
    You answer questions about expenses based on the expenses policy data.
    If a user wants to submit an expense claim, you get their email address, a description of the claim,
    and the amount to be claimed and write the claim details to a text file that the user can download.
    ```
4. In the Setup pane, next to the Knowledge header, select + Add. Then in the Add knowledge dialog box, select Files.

    <img width="893" height="551" alt="Screenshot 2025-10-28 at 6 53 49 PM" src="https://github.com/user-attachments/assets/a11463c6-17ef-4f23-bb20-b4e28eca3847" />

5. In the Adding files dialog box, create a new vector store named `Expenses_Vector_Store`, uploading and saving the
   Expenses_policy.docx local file that you downloaded previously.

     <img width="892" height="640" alt="Screenshot 2025-10-28 at 6 55 13 PM" src="https://github.com/user-attachments/assets/ee5d2e48-c179-4167-a5e9-0264c2ba3fba" />

7. Below the Knowledge section, next to Actions, select + Add. Then in the Add action dialog box, select Code
   interpreter and then select Save (you do not need to upload any files for the code interpreter).

8. The agent will use the document you uploaded as its knowledge source to ground its responses (in other words,
   it will answer questions based on the contents of this document). It will use the code interpreter tool as
   required to perform actions by generating and running its own Python code.

     <img width="890" height="545" alt="Screenshot 2025-10-28 at 6 57 28 PM" src="https://github.com/user-attachments/assets/e24876b4-9f7d-49b0-b705-c9dec96813a6" />

## Test the agent

1. In the playground chat entry, enter the prompt: `What's the maximum I can claim for meals?` and review the agent’s
   response - which is based on information in the expenses policy document you added as knowledge to the agent
   setup.

    <img width="994" height="946" alt="Screenshot 2025-10-28 at 7 02 05 PM" src="https://github.com/user-attachments/assets/ec89d2de-9790-4400-aeb3-3caa10849968" />

2. Provide the below prompts

    `I'd like to submit a claim for a meal.`
    `fred@contoso.com`
    `Breakfast cost me $20`

   <img width="586" height="904" alt="Screenshot 2025-10-28 at 7 05 49 PM" src="https://github.com/user-attachments/assets/d034e00b-ae2e-4024-a93c-114d00f5b335" />

4. The agent should use the code interpreter to prepare the expense claim text file, and provide a
   link so you can download it.
   
    <img width="549" height="418" alt="Screenshot 2025-10-28 at 7 07 07 PM" src="https://github.com/user-attachments/assets/c50ba189-1144-4fa2-aefd-adaec500b6a5" />

5. Run info thread displays the chat history

   <img width="963" height="902" alt="Screenshot 2025-10-28 at 7 08 20 PM" src="https://github.com/user-attachments/assets/9ed19f4e-8006-45ef-a9c8-3ebe8bbc6d86" />

6. Clean up resources 
