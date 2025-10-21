# Choose and deploy a language model

## Explore models

  1. In a web browser, open the Azure AI Foundry portal at https://ai.azure.com and sign in using your Azure credentials

  2. In the Explore models and capabilities section, search for the gpt-4o model.

      <img width="1483" height="819" alt="Screenshot 2025-10-20 at 6 57 10 PM" src="https://github.com/user-attachments/assets/f9dc84ae-859e-44c1-a0e3-e01cc99f2ca7" />

  3. gpt-4o benchmarks

      <img width="1501" height="852" alt="Screenshot 2025-10-20 at 7 00 27 PM" src="https://github.com/user-attachments/assets/bcc7a9e5-193a-40b3-ac7f-9ed34d2a0fd7" />

  4. Model comparision

      <img width="1493" height="840" alt="Screenshot 2025-10-20 at 7 05 25 PM" src="https://github.com/user-attachments/assets/0f872fa4-b494-45ad-91fc-a7cb932838c1" />

## Create an Azure AI Foundry project

  1. At the top of the gpt-4o model overview page, select Use this model; Create a project to work with gpt-4o

      <img width="1499" height="801" alt="Screenshot 2025-10-20 at 7 12 04 PM" src="https://github.com/user-attachments/assets/1e5fa43a-364e-4561-8a82-9fbf2b15ae6b" />

  2.  If prompted, deploy the gpt-4o model using the Global standard deployment type and customize the deployment details to set a Tokens per minute rate limit of 50K

      <img width="638" height="607" alt="Screenshot 2025-10-20 at 7 13 50 PM" src="https://github.com/user-attachments/assets/d9141968-68a1-4f0e-b63a-772e044e1448" />

  3.  The chat playground will be opened automatically so one can test your model:

      <img width="1495" height="856" alt="Screenshot 2025-10-20 at 7 15 13 PM" src="https://github.com/user-attachments/assets/ec569f5b-4236-43b8-8099-b4cd01cc5677" />
        
## Chat with the gpt-4o model

  1. In the chat playground, in the Setup pane, ensure that your gpt-4o model is selected and in the Give the model instructions and context field,
       set the system prompt to `You are an AI assistant that helps solve problems.`

  2. In the chat window, enter the following query

      ```
      I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time.
      If I leave the chicken and the grain unattended, the chicken will eat the grain.
      If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
      ```

  3. View the response. Then, enter the following follow-up query

      ```
      Explain your reasoning.
      ```

     <img width="1509" height="855" alt="Screenshot 2025-10-20 at 7 21 43 PM" src="https://github.com/user-attachments/assets/afdcc448-e102-4001-8032-93f46772799a" />

  5. Clean up the resources by deleting the resource group.

     <img width="1503" height="652" alt="Screenshot 2025-10-20 at 7 34 16 PM" src="https://github.com/user-attachments/assets/ee8e8dfa-afb3-4854-bf51-e55f2f1bb589" />

     <img width="1506" height="603" alt="Screenshot 2025-10-20 at 7 35 08 PM" src="https://github.com/user-attachments/assets/9dd5a03b-b04b-440e-95a5-3ee3009d6ab5" />


     

  
