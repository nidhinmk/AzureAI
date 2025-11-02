# Integrate MCP Tools with Azure Agents

Dynamic tool discovery is a mechanism that allows an AI agent to discover available external tools without needing hardcoded knowledge of each one. Instead of manually adding or updating every tool the agent can use, the agent queries a centralized Model Context Protocol (MCP) server. [Code](/mcp_client.py)

To dynamically connect tools to the Azure AI Agent, we first need a functioning Model Context Protocol (MCP) setup. This includes both the MCP server, which hosts your tool catalog, and the MCP client, which fetches those tools and makes them usable by the agent. The MCP server acts as a registry for tools the agent can use. A standard MCP client acts as a bridge between the MCP server and the Azure AI Agent Service. 

## Connect AI agents to tools using Model Context Protocol (MCP)

In this exercise, we’ll build an agent that connects to a cloud-hosted MCP server.

### Create an Azure AI Foundry project
1. Open the Azure AI Foundry portal at https://ai.azure.com and sign in using your Azure credentials

2. Select Create an agent.

3. Confirm the following settings for your project:

    <img width="831" height="707" alt="Screenshot 2025-11-02 at 9 01 10 AM" src="https://github.com/user-attachments/assets/2bd74582-94e2-44af-ab18-1080ec3cf809" />

4. Deploy a gpt-4o model using either the Global Standard or Standard deployment option

    <img width="597" height="809" alt="Screenshot 2025-11-02 at 9 03 07 AM" src="https://github.com/user-attachments/assets/018abd58-840f-4916-bfbf-a8b5898a10a8" />

5. Copy the Azure AI Foundry project endpoint value from Overview section

   <img width="958" height="590" alt="Screenshot 2025-11-02 at 9 03 48 AM" src="https://github.com/user-attachments/assets/bcad12b5-6dce-4c30-9c0c-1ebf23203520" />


### Develop an agent that uses MCP function tools

#### Clone the repo containing the application code

1. Browse to the Azure portal at https://portal.azure.com; signing in with your Azure credentials

2. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment with no storage in your subscription.

3. In the cloud shell toolbar, in the Settings menu, select Go to Classic version

4. Enter the following commands to clone the GitHub repo containing the code files for this exercise

    ```
    rm -r ai-agents -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-agents ai-agents
    ```
5. Change the working directory to the folder containing the code files and list them all.

    ```
    cd ai-agents/Labfiles/03c-use-agent-tools-with-mcp/Python
    ls -a -l
    ```
#### Configure the application settings

1. Install the libraries we’ll use:

    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt --pre azure-ai-projects mcp
    ```
    Update to the latest version
   
    <img width="967" height="565" alt="Screenshot 2025-11-02 at 9 09 34 AM" src="https://github.com/user-attachments/assets/f0fa7213-56d0-41b0-a112-b0884eeda77e" />

3. Edit the configuration file

    `code .env`
4. Replace the your_project_endpoint placeholder with the endpoint for the project. MODEL_DEPLOYMENT_NAME variable is set to your model deployment name (which should be gpt-4o).

5. Save and Quit. Ctrl+S and Ctrl+Q.

#### Connect an Azure AI Agent to a remote MCP server
In this task, we’ll connect to a remote MCP server, prepare the AI agent, and run a user prompt.

1. Edit the code file that has been provided:

    `code client.py`
2. import the classes

    ```
    # Add references
    from azure.identity import DefaultAzureCredential
    from azure.ai.agents import AgentsClient
    from azure.ai.agents.models import McpTool, ToolSet, ListSortOrder
    ```
3. Connect to the Azure AI project using the current Azure credentials.

    ```
    # Connect to the agents client
    agents_client = AgentsClient(
         endpoint=project_endpoint,
         credential=DefaultAzureCredential(
             exclude_environment_credential=True,
             exclude_managed_identity_credential=True
         )
    )
    ```
4. Initialize agent MCP tool. This code will connect to the Microsft Learn Docs remote MCP server. This is a cloud-hosted service that enables clients to access trusted and up-to-date information directly from Microsoft’s official documentation.

    ```
    # Initialize agent MCP tool
    mcp_tool = McpTool(
         server_label=mcp_server_label,
         server_url=mcp_server_url,
    )
        
    mcp_tool.set_approval_mode("never")
        
    toolset = ToolSet()
    toolset.add(mcp_tool)
    ```
5. Create a new agent. Provide instructions for the agent and provide it with the MCO tool definitions.

    ```
    # Create a new agent
    agent = agents_client.create_agent(
         model=model_deployment,
         name="my-mcp-agent",
         instructions="""
         You have access to an MCP server called `microsoft.docs.mcp` - this tool allows you to 
         search through Microsoft's latest official documentation. Use the available MCP tools 
         to answer questions and perform tasks."""
    )
    ```
6. Create thread for communication

    ```
    # Create thread for communication
    thread = agents_client.threads.create()
    print(f"Created thread, ID: {thread.id}")
    ```
7. Create a message on the thread

    ```
    # Create a message on the thread
    prompt = input("\nHow can I help?: ")
    message = agents_client.messages.create(
         thread_id=thread.id,
         role="user",
         content=prompt,
    )
    print(f"Created message, ID: {message.id}")
    ```
8. Set approval mode. This allows the agent to automatically invoke the MCP tools without requiring user approval. If we want to require approval, we must supply a header value using mcp_tool.update_headers.

    ```
     # Set approval mode
     mcp_tool.set_approval_mode("never")
    ```
9. Create and process agent run in thread with MCP tools. The AI Agent automatically invokes the connected MCP tools to process the prompt request.

    ```
    # Create and process agent run in thread with MCP tools
    run = agents_client.runs.create_and_process(thread_id=thread.id, agent_id=agent.id, toolset=toolset)
    print(f"Created run, ID: {run.id}")
    ```
    
10. Save and Quit. Ctrl+s and Ctrl+Q

### Sign into Azure and run the app

1. Sign into Azure. You must sign into Azure - even though the cloud shell session is already authenticated.

    `az login`
2. Run the application

    `python client.py`

3. When prompted, enter a request for technical information such as:
   
    ` Give me the Azure CLI commands to create an Azure Container App with a managed identity.`
   
4. Using the MCP server to find a suitable tool to retrieve the requested information. We should see some output similar to the following:
   
   <img width="973" height="815" alt="Screenshot 2025-11-02 at 9 38 24 AM" src="https://github.com/user-attachments/assets/37107874-bb4a-4c15-99c2-4b27c6d042ee" />

5. Another user request

    <img width="1912" height="803" alt="Screenshot 2025-11-02 at 9 43 54 AM" src="https://github.com/user-attachments/assets/ffc0737b-ac3d-4861-9841-fd4bc85877b3" />

6. The agent was able to invoke the MCP tool microsoft_docs_search automatically to fulfill the request.

#### Cleaup up
Open the Azure portal at https://portal.azure.com and Delete Resource Group
