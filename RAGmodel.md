## Retrieval Augmented Generation (RAG)


RAG is a technique that one can use to ground a language model. In other words, it's a process for retrieving information that is relevant to the user's initial prompt. By retrieving context 
from a specified data source, you ensure that the language model uses relevant information when responding, instead of relying on its training data. [Code](/rag-app.py)

### Using a Vector index

An embedding is a special format of data representation that a search engine can use to easily find the relevant information. More specifically, an embedding is a vector of floating-point numbers.
By representing words and their meanings with vectors, you can extract relevant context from your data source even when your data is stored in different formats (text or image) and languages.
You can add your data to Azure AI Foundry, after which you can use Azure AI Search to create an index in the Azure AI Foundry portal using an embedding model. The index asset is stored 
in Azure AI Search and queried by Azure AI Foundry when used in a chat flow

### Searching an Index

- Keyword search: Identifies relevant documents or passages based on specific keywords or terms provided as input.
- Semantic search: Retrieves documents or passages by understanding the meaning of the query and matching it with semantically related content rather than relying solely on exact keyword matches.
- Vector search: Uses mathematical representations of text (vectors) to find similar documents or passages based on their semantic meaning or context.
- Hybrid search: Combines any or all of the other search techniques. Queries are executed in parallel and are returned in a unified result set.

When including RAG in your chat flow, our goal is to ground the chatbot's responses. Next to retrieving relevant context from your data source, one can also influence the groundedness of the 
chatbot's response by instructing it to use the context and aim to be factual.

## Create a generative AI app that uses your own data (Exercise)

Retrieval Augmented Generation (RAG) is a technique used to build applications that integrate data from custom data sources into a prompt for a generative AI model.

### Create an Azure AI Foundry hub and project

1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using the Azure credentials.

2. Navigate to `https://ai.azure.com/managementCenter/allResources` and select Create new. Then choose the option to create a new AI hub resource.

3. In the Create a project wizard, enter a valid name for your project, and select the option to create a new hub. Then use the Rename hub link to specify a valid name for your new hub,
   expand Advanced options, and specify the following settings for your project:
   
      - Subscription: Your Azure subscription
      - Resource group: Create or select a resource group
      - Region: East US 2 or Sweden Central (In the event of a quota limit being exceeded later in the exercise, we may need to create another resource in a different region.)

     <img width="634" height="710" alt="Screenshot 2025-10-25 at 10 37 19 AM" src="https://github.com/user-attachments/assets/d9b1e669-0b75-498a-a67c-bb2c5e429554" />

### Deploy models

  We need two models to implement your solution:
  
  - An embedding model to vectorize text data for efficient indexing and processing.
  - A model that can generate natural language responses to questions based on your data.

1. In the Azure AI Foundry portal, in the project, in the navigation pane on the left, under `My assets`, select the `Models + endpoints` page.

2. Create a new deployment of the `text-embedding-ada-002` model with the following settings by selecting Customize in the Deploy model wizard:

    - Deployment name: A valid name for your model deployment
    - Deployment type: Global Standard
    - Model version: Select the default version
    - Connected AI resource: Select the resource created previously
    - Tokens per Minute Rate Limit (thousands): 50K (or the maximum available in your subscription if less than 50K)
    - Content filter: DefaultV2
  
   <img width="1030" height="773" alt="Screenshot 2025-10-25 at 10 46 22 AM" src="https://github.com/user-attachments/assets/9e6d2953-cf77-4705-95b9-4d48ad6a5a3b" />

   <img width="632" height="846" alt="Screenshot 2025-10-25 at 10 48 37 AM" src="https://github.com/user-attachments/assets/c9ea7e1d-af84-420b-8a1d-9cb19bc09c1c" />

3. Return to the Models + endpoints page and repeat the previous steps to deploy a gpt-4o model using a Global Standard deployment of the most
     recent version with a TPM rate limit of 50K (or the maximum available in your subscription if less than 50K).

    <img width="634" height="647" alt="Screenshot 2025-10-25 at 10 52 16 AM" src="https://github.com/user-attachments/assets/d7a51b5e-e43d-4105-a084-71a10108f61a" />

### Add data to the project

The data for your app consists of a set of travel brochures in PDF format from the fictitious travel agency Margie’s Travel. Let’s add them to the project.

1. In a new browser tab, download the zipped archive of brochures from `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip`
     and extract it to a folder named brochures on your local file system.
2. In Azure AI Foundry portal, in your project, in the navigation pane on the left, under My assets, select the Data + indexes page.
3. Select + New data.
4. In the Add your data wizard, expand the drop-down menu to select Upload files/folders.
5. Select Upload folder and upload the brochures folder. Wait until all the files in the folder are listed.
6. Select Next and set the data name to `brochures`.
7. Wait for the folder to be uploaded and note that it contains several .pdf files.

  <img width="1007" height="735" alt="Screenshot 2025-10-25 at 11 09 53 AM" src="https://github.com/user-attachments/assets/3602e90d-e471-4baf-a64a-0b49ee36b5f4" />

### Create an index for your data

After adding a data source to your project, we can use it to create an index in your Azure AI Search resource.

1. In Azure AI Foundry portal, in the project, in the navigation pane on the left, under `My assets`, select the `Data + indexes` page.

2. In the Indexes tab, add a new index with the following settings:
    - Source location:
        Data source: Data in Azure AI Foundry
        Select the brochures data source
      
       <img width="1017" height="730" alt="Screenshot 2025-10-25 at 11 17 15 AM" src="https://github.com/user-attachments/assets/5e51a861-8eee-468c-8853-386a81ce851c" />

    - Index Configuration
        Select Azure AI Search service: Create a new Azure AI Search resource with the following settings:
          Subscription: You Azure subscription
          Resource group: The same resource group as your AI hub
          Service name: A valid name for your AI Search Resource
          Location: The same location as your AI hub
          Pricing tier: Basic
  
      <img width="861" height="889" alt="Screenshot 2025-10-25 at 11 26 03 AM" src="https://github.com/user-attachments/assets/3a18d8bf-0b2d-4307-96d7-92352fbba4e5" />

   Wait for the AI Search resource to be created. Then return to the Azure AI Foundry and finish configuring the index by selecting Connect other Azure AI Search resource
   and adding a connection to the AI Search resource you just created.

      Vector index: brochures-index
      Virtual machine: Auto select

     <img width="1014" height="740" alt="Screenshot 2025-10-25 at 11 29 12 AM" src="https://github.com/user-attachments/assets/9e410c7f-cfed-4c55-b706-420b99758069" />

   - Search settings:
        Vector settings: Add vector search to this search resource
        Azure OpenAI connection: Select the default Azure OpenAI resource for your hub.
        Embedding model: text-embedding-ada-002
        Embedding model deployment: Your deployment of the text-embedding-ada-002 model

    <img width="1016" height="740" alt="Screenshot 2025-10-25 at 11 35 20 AM" src="https://github.com/user-attachments/assets/d2f1ab8f-d7bd-4222-9ca7-94ffb76e64b0" />

  3. Create the vector index and wait for the indexing process to be completed, which can take a while depending on available compute resources in your subscription.

      The index creation operation consists of the following jobs:
      
        Crack, chunk, and embed the text tokens in your brochures data.
        Create the Azure AI Search index.
        Register the index asset.

### Test the index in the playground

We can now use the Azure OpenAI SDK to implement the RAG pattern in a client application.
- Prepare the application configuration

    1. In the Azure Portal, Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell
         selecting a PowerShell environment with no storage in your subscription.
    2. In the cloud shell toolbar, in the Settings menu, select Go to Classic version
    3. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for this exercise
 
      rm -r mslearn-ai-foundry -f
      git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    4. Navigate to the folder containing the chat application code files
  
      cd mslearn-ai-foundry/labfiles/rag-app/python

    5. Enter the following command to install the OpenAI SDK library:

           python -m venv labenv
           ./labenv/bin/Activate.ps1
           pip install -r requirements.txt openai
       
    7. Edit the configuration file that has been provided:
       
            code .env
    8. In the configuration file, replace the following placeholders
       
        <img width="1101" height="489" alt="Screenshot 2025-10-25 at 11 58 25 AM" src="https://github.com/user-attachments/assets/aca249a3-e8db-43dd-8ee7-d9dd96da36c4" />

- Explore code to implement the RAG pattern

  1. Enter the following command to edit the code file that has been provided:
  
          code rag-app.py

  2. Review the code in the file, noting that it:
  
      - Creates an Azure OpenAI client using the endpoint, key, and chat model.
      - Creates a suitable system message for a travel-related chat solution.
      - Submits a prompt (including the system and a user message based on the user input) to the Azure OpenAI client, adding:
      - Connection details for the Azure AI Search index to be queried.
      - Details of the embedding model to be used to vectorize the query*.
      - Displays the response from the grounded prompt.
      - Adds the response to the chat history.
       
- Run the chat application

  1. Enter the following command to run the app:

      `python rag-app.py`
  2. Enter a question, such as Where should I go on vacation to see architecture? and review the response from your generative AI model.

     <img width="1504" height="864" alt="Screenshot 2025-10-25 at 12 22 14 PM" src="https://github.com/user-attachments/assets/6e8237cb-4a0c-4373-bb77-0c998459b6d8" />

  3. Try a follow-up question, for example Where can I stay there?
 
     <img width="1505" height="815" alt="Screenshot 2025-10-25 at 12 23 52 PM" src="https://github.com/user-attachments/assets/13e6abf4-f6b9-4da3-a1dd-14705c9fbcd4" />


    

