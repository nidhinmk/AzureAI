## Use a prompt flow to manage conversation in a chat app

In this exercise, we’ll use Azure AI Foundry portal’s prompt flow to create a custom chat app that uses a user prompt and chat history as inputs, 
and uses a GPT model from Azure OpenAI to generate an output.

### Create an Azure AI Foundry hub and project

1. In a web browser, open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using your Azure credentials.

2. In the browser, navigate to `https://ai.azure.com/managementCenter/allResources` and select Create new. Then choose the option to create a new AI hub resource.

<img width="1502" height="808" alt="Screenshot 2025-10-24 at 10 30 20 AM" src="https://github.com/user-attachments/assets/bf3d3de8-c070-4a85-89ed-0cc239e3df7d" />

3. In the Create a project wizard, enter a valid name for your project, and select the option to create a new hub. Then use the Rename hub link to specify a valid name for your new hub, expand Advanced options, and specify the following settings for your project:

<img width="638" height="418" alt="Screenshot 2025-10-24 at 10 32 28 AM" src="https://github.com/user-attachments/assets/7f588464-8f67-4eff-bf7f-2c169ed1f060" />

<img width="644" height="700" alt="Screenshot 2025-10-24 at 10 36 00 AM" src="https://github.com/user-attachments/assets/f7bf7441-d467-4004-a896-edac921f0b53" />

### Configure resource authorization

The prompt flow tools in Azure AI Foundry create file-based assets that define the prompt flow in a folder in blob storage. Before exploring prompt flow, let’s ensure that your Azure AI Foundry resource has the required access to the blob store so it can read them.

1. In a new browser tab, open the Azure portal at `https://portal.azure.com`

2. Select the Azure AI Foundry resource for your hub to open it. Then expand its Resource Management section and select the Identity page:

<img width="1512" height="366" alt="Screenshot 2025-10-24 at 10 54 53 AM" src="https://github.com/user-attachments/assets/25280cc7-385d-41bb-b184-a35410bd6e8c" />

3. If the status of the system assigned identity is Off, switch it On and save your changes.

<img width="1503" height="784" alt="Screenshot 2025-10-24 at 11 02 12 AM" src="https://github.com/user-attachments/assets/eb506dea-e191-43ea-a97e-37a08daafa79" />

4. Return to the resource group page, and then select the Storage account resource for your hub and view its Access Control (IAM) page:

<img width="1505" height="783" alt="Screenshot 2025-10-24 at 11 04 28 AM" src="https://github.com/user-attachments/assets/8d637798-13f3-41bd-8664-aea347601e71" />

5. Add a role assignment to the `Storage blob data reader` role for the managed identity used by your Azure AI Foundry project resource:

<img width="1501" height="864" alt="Screenshot 2025-10-24 at 11 08 25 AM" src="https://github.com/user-attachments/assets/553ce7f7-9326-43d9-88c8-ca8b6d01b33b" />

6. When you’ve reviewed and assigned the role access to allow the Azure AI Foundry managed identity to read blobs in the storage account, close the Azure portal tab and return to the Azure AI Foundry portal.

### Deploy a generative AI model

Now we’re ready to deploy a generative AI language model to support your prompt flow application.

1. In the pane on the left for your project, in the My assets section, select the Models + endpoints page.

<img width="1504" height="909" alt="Screenshot 2025-10-24 at 11 13 34 AM" src="https://github.com/user-attachments/assets/d05129c5-2edc-4c38-b85f-22a048cef506" />

2. In the Models + endpoints page, in the Model deployments tab, in the + Deploy model menu, select Deploy base model.

<img width="739" height="278" alt="Screenshot 2025-10-24 at 11 14 51 AM" src="https://github.com/user-attachments/assets/2f18a34c-4996-4861-8d63-b411fcb94cb5" />

3. Search for the gpt-4o model in the list, and then select and confirm it.

<img width="1019" height="773" alt="Screenshot 2025-10-24 at 11 16 38 AM" src="https://github.com/user-attachments/assets/71f69b1a-097f-4dde-b40b-59cd56d6d3a8" />

4. Deploy the model with the following settings by selecting Customize in the deployment details:

<img width="636" height="658" alt="Screenshot 2025-10-24 at 11 18 56 AM" src="https://github.com/user-attachments/assets/8be82225-f050-491a-bb62-10446690d751" />

### Create a prompt flow
A prompt flow provides a way to orchestrate prompts and other activities to define an interaction with a generative AI model. In this exercise, you’ll use a template to create a basic chat flow for an AI assistant in a travel agency.

1. In the Azure AI Foundry portal navigation bar, in the Build and customize section, select Prompt flow.

<img width="1374" height="671" alt="Screenshot 2025-10-24 at 11 26 16 AM" src="https://github.com/user-attachments/assets/10fbb85c-907a-44df-b397-cb04c7d5e9f5" />

2. Create a new flow based on the Chat flow template, specifying Travel-Chat as the folder name.

<img width="1079" height="319" alt="Screenshot 2025-10-24 at 11 29 39 AM" src="https://github.com/user-attachments/assets/b24d213f-eca5-458f-99ff-2603e884c283" />

<img width="647" height="798" alt="Screenshot 2025-10-24 at 11 30 18 AM" src="https://github.com/user-attachments/assets/963f2988-376d-4588-a87e-3bace4b42132" />

3. Resolve the error by following the on-screen instructions

<img width="640" height="796" alt="Screenshot 2025-10-24 at 11 32 32 AM" src="https://github.com/user-attachments/assets/9707c2a6-2e99-4928-b6d2-db2cb18d8feb" />

4. To be able to test the flow, we need compute, and it can take a while to start; so select Start compute session to get it started while you explore and modify the default flow.

<img width="1492" height="828" alt="Screenshot 2025-10-24 at 11 44 03 AM" src="https://github.com/user-attachments/assets/3c9df378-e5bf-4437-bb9a-a811558d4c0c" />

5. In the Inputs pane, and note that there are two inputs (chat history and the user’s question)
   
6. View the Outputs pane and note that there’s an output to reflect the model’s answer.
   
7. View the Chat LLM tool pane, which contains the information needed to submit a prompt to the model.

<img width="1301" height="802" alt="Screenshot 2025-10-24 at 11 49 00 AM" src="https://github.com/user-attachments/assets/453b551d-4e4e-4a5e-91b0-62db5c079dad" />

8. In the Chat LLM tool pane, for Connection, select the connection for the Azure OpenAI service resource in your AI hub. Then configure the following connection properties:

<img width="767" height="541" alt="Screenshot 2025-10-24 at 11 52 08 AM" src="https://github.com/user-attachments/assets/4648c1c4-2e63-4f03-92bb-f9ea2c58eacd" />

9. Modify the Prompt field as follows: It consists of a system message (which includes an objective, a definition of its capabilities, and some instructions), and the chat history (ordered to show each user question input and each previous assistant answer output)

```
# system:
**Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

**Capabilities**:
- Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
- Offer personalized travel suggestions based on user preferences, budget, and travel dates.
- Share tips on packing, safety, and navigating travel disruptions.
- Help with itinerary planning, including optimal routes and must-see landmarks.
- Answer common travel questions and provide solutions to potential travel issues.

**Instructions**:
1. Engage with the user in a friendly and professional manner, as a travel agent would.
2. Use available resources to provide accurate and relevant travel information.
3. Tailor responses to the user's specific travel needs and interests.
4. Ensure recommendations are practical and consider the user's safety and comfort.
5. Encourage the user to ask follow-up questions for further assistance.

{% for item in chat_history %}
# user:
{{item.inputs.question}}
# assistant:
{{item.outputs.answer}}
{% endfor %}

# user:
{{question}}
```

10. In the Inputs section for the Chat LLM tool (under the prompt), ensure the following variables are set:
    - question (string): ${inputs.question}
    - chat_history (string): ${inputs.chat_history}
   
  <img width="781" height="214" alt="Screenshot 2025-10-24 at 12 07 40 PM" src="https://github.com/user-attachments/assets/9aae6e73-91ee-43c8-9dfb-26d264820327" />


<img width="826" height="282" alt="Screenshot 2025-10-24 at 11 58 05 AM" src="https://github.com/user-attachments/assets/2344d9ad-0d05-48e4-860b-67539bde20aa" />

11. Save the changes

### Test the flow

1. Ensure the compute session is running. If not, wait for it to start.
   
2. On the toolbar, select Chat to open the Chat pane, and wait for the chat to initialize.

3. Enter the query: `I have one day in Vancouver, what should I do?` and review the output. The Chat pane should look similar to this:

<img width="1512" height="848" alt="Screenshot 2025-10-24 at 12 14 22 PM" src="https://github.com/user-attachments/assets/cf655550-3094-45f9-bb8d-41265835ba74" />

### Deploy the flow

1. On the toolbar, select Deploy and deploy the flow with the following settings

Basic settings:
  - Endpoint: New
  - Endpoint name: Enter a unique name
  - Deployment name: Enter a unique name
  - Virtual machine: Standard_DS3_v2
  - Instance count: 1
  - Inferencing data collection: Disabled
    
<img width="1459" height="804" alt="Screenshot 2025-10-24 at 12 19 38 PM" src="https://github.com/user-attachments/assets/17c0ea61-3663-41c5-ad29-e335a14a9d98" />

Advanced settings:
  - Use the default settings

<img width="1453" height="819" alt="Screenshot 2025-10-24 at 12 20 14 PM" src="https://github.com/user-attachments/assets/5162e9f6-e23e-4e62-990a-891285ad5bd1" />

2. In Azure AI Foundry portal, in the navigation pane, in the My assets section, select the Models + endpoints page.

3. Enter the prompt `What is there to do in San Francisco?` and review the response.

4. Enter the prompt `Tell me something about the history of the city.` and review the response.

<img width="844" height="626" alt="Screenshot 2025-10-24 at 12 32 51 PM" src="https://github.com/user-attachments/assets/9b41a7f8-3522-4b9a-9f92-47210d2ac415" />

5. Clean up by deleting resource groups



