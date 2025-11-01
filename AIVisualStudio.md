# Develop an AI agent with VS Code extension

In this exercise, we’ll use the Azure AI Foundry VS Code extension to create an agent that can use Model Context Protocol (MCP) server tools to access external data sources and APIs. 
The agent will be able to retrieve up-to-date information and interact with various services through MCP tools.

## Install the Azure AI Foundry VS Code extension

1. Open Visual Studio Code.

2. Select Extensions from the left pane (or press Ctrl+Shift+X).

3. In the search bar, type Azure AI Foundry and press Enter.

4. Select the Azure AI Foundry extension from Microsoft and click Install.

5. After installation is complete, verify the extension appears in the primary navigation bar on the left side of Visual Studio Code.

   <img width="1512" height="947" alt="Screenshot 2025-10-31 at 11 39 32 AM" src="https://github.com/user-attachments/assets/8c949c1d-3135-4adc-b2ec-a55b4ad4a464" />

## Sign in to Azure and create a project

1. In the VS Code sidebar, select the Azure AI Foundry extension icon.

2. In the Azure Resources view, select Sign in to Azure… and follow the authentication prompts.

3. After signing in, select your Azure subscription from the dropdown.

4. Create a new Azure AI Foundry project by selecting the + (plus) icon next to Resources in the Azure AI Foundry Extension view.

5. Choose whether to create a new resource group or use an existing one:

    To create a new resource group:
    
        Select Create new resource group and press Enter
          Enter a name for your resource group (e.g., “rg-ai-agents-lab”) and press Enter
          Select a location from the available options and press Enter
   
     <img width="1136" height="518" alt="Screenshot 2025-10-31 at 11 45 25 AM" src="https://github.com/user-attachments/assets/4b20a2e3-e302-4c1b-8f3f-c7d0d4508f1b" />

   To use an existing resource group:

        Select the resource group we want to use from the list and press Enter

7. Enter a name for your Azure AI Foundry project (e.g., “ai-agents-project”) in the textbox and press Enter.

8. Wait for the project deployment to complete. A popup will appear with the message “Project deployed successfully.”

## Deploy a model

1. Select the Deploy a model button when the Azure AI Foundry project creation is complete. This opens the Model Catalog. We can also access the Model Catalog by selecting the + icon next
   to Models in the Resources section, or by pressing F1 and running the command Azure AI Foundry: Open Model Catalog.

2. Search and Deploy `gpt-4o` model
   
    <img width="1508" height="945" alt="Screenshot 2025-10-31 at 11 57 23 AM" src="https://github.com/user-attachments/assets/77e6faec-ab1c-4cab-8c6c-3790b63fc167" />

3. Use the below configuration and deploy the model.

    <img width="1512" height="901" alt="Screenshot 2025-10-31 at 11 59 55 AM" src="https://github.com/user-attachments/assets/534ae333-a4e9-45ec-a5a7-08776f68dd8e" />

## Create an AI agent with the designer view

1. In the Azure AI Foundry extension view, find the Resources section.

2. Select the + (plus) icon next to the Agents subsection to create a new AI Agent.

3. Choose a location to save your agent files when prompted.

4. The agent designer view will open along with a .yaml configuration file.

    <img width="1501" height="918" alt="Screenshot 2025-10-31 at 12 03 35 PM" src="https://github.com/user-attachments/assets/f669136c-3d28-4d1e-97d4-d21e5f301a43" />

### Configure the agent in the designer

Instructions: 

    ```You are an AI agent that helps users research information from various sources. Use the available tools to access up-to-date information and provide comprehensive 
    responses based on external data sources.
    ```
  
  <img width="550" height="540" alt="Screenshot 2025-10-31 at 12 06 34 PM" src="https://github.com/user-attachments/assets/468be43b-3413-469b-83d4-ce07a405d26e" />

## Add an MCP Server tool to your agent

We'll add a Model Context Protocol (MCP) server tool that allows our agent to access external APIs and data sources.

1. In the TOOL section of the designer, select the Add tool button in the top-right corner; choose MCP server

   <img width="944" height="656" alt="Screenshot 2025-10-31 at 12 09 30 PM" src="https://github.com/user-attachments/assets/a5f5425b-5c05-424f-bf32-c9243a2f5dcd" />

2. Configure the MCP Server tool with the following information:
      Server URL: Enter the URL of an MCP server (e.g., https://gitmcp.io/Azure/azure-rest-api-specs)
      Server Label: Enter a unique identifier (e.g., “github_docs_server”)
   
4. Leave the Allowed tools dropdown empty to allow all tools from the MCP server.

5. Select the Create tool button to add the tool to your agent.

    <img width="574" height="565" alt="Screenshot 2025-10-31 at 12 11 52 PM" src="https://github.com/user-attachments/assets/24d51df5-4388-4090-add8-293b22bf6888" />

## Deploy the agent to Azure AI Foundry   

1. In the designer view, select the Create on Azure AI Foundry button in the bottom-left corner.

2. In the VS Code navbar, refresh the Azure Resources view. Your deployed agent should now appear under the Agents subsection.

   <img width="972" height="622" alt="Screenshot 2025-10-31 at 12 13 29 PM" src="https://github.com/user-attachments/assets/90ab7ddc-ec4c-4053-ac59-6b527546a77d" />
   
## Test your agent in the playground

1. Right-click on your deployed agent in the Agents subsection.

2. Select Open Playground from the context menu.

3. The Agents Playground will open in a new tab within VS Code.

4. Type a test prompt such as:

  ```
  Can you help me find documentation about Azure Container Apps and provide an example of how to create one?
  ```
5. Send the message and observe the authentication and approval prompts for the MCP Server tool:
    For this exercise, select No Authentication when prompted.
    For the MCP Tools approval preference, we can select Always approve.

6. Review the agent’s response and note how it uses the MCP server tool to retrieve external information.

7. Check the Agent Annotations section to see the sources of information used by the agent.

## Generate sample code for your agent

1. Right-click on the deployed agent and select Open Code File, or select the Open Code File button in the Agent Preferences page.

2. Choose your preferred SDK from the dropdown (Python, .NET, JavaScript, or Java).

3. Select your preferred programming language.

4. Choose your preferred authentication method.

5. Review the generated sample code that demonstrates how to interact with your agent programmatically.

   <img width="1497" height="731" alt="Screenshot 2025-10-31 at 12 50 36 PM" src="https://github.com/user-attachments/assets/152e9efe-6c09-4e6c-bc57-67435aee30bc" />

## View conversation history and threads

1. In the Azure Resources view, expand the Threads subsection to see conversations created during your agent interactions.

2. Select a thread to view the Thread Details page, which shows:
    Individual messages in the conversation
    Run information and execution details
    Agent responses and tool usage
   
4. Select View run info to see detailed JSON information about each run.

## Summary
In this exercise, you used the Azure AI Foundry VS Code extension to create an AI agent with MCP server tools. The agent can access external data sources and APIs 
through the Model Context Protocol, enabling it to provide up-to-date information and interact with various services. You also learned how to test the agent in the 
playground and generate sample code for programmatic interaction.

## Clean up
When we’ve finished exploring the Azure AI Foundry VS Code extension, we should clean up the resources to avoid incurring unnecessary Azure costs.

### Delete the agents
  In the Azure AI Foundry portal, select Agents from the navigation menu.
  
  Select the agent and then select the Delete button.

### Delete your models
  In VS Code, refresh the Azure Resources view.
  
  Expand the Models subsection.
  
  Right-click on your deployed model and select Delete.

### Delete other resources
  Open the Azure portal.
  
  Navigate to the resource group containing your AI Foundry resources.
  
  Select Delete resource group and confirm the deletion.
