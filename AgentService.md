# Develop AI agents with Azure AI Foundry Agent Service

## What is an AI Agent
An AI agent is a software service that uses generative AI to understand and perform tasks on behalf of a user or another program. 
These agents use advanced AI models to understand context, make decisions, utilize grounding data, and take actions to achieve specific goals. 

## Develop an AI Agent (Exercise)

In this exercise, we’ll use Azure AI Agent Service to create a simple agent that analyzes data and creates charts. 
The agent can use the built-in Code Interpreter tool to dynamically generate any code required to analyze data.

### Create an Azure AI Foundry project

1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using your Azure credentials

2. Select Create an agent.

    <img width="517" height="545" alt="Screenshot 2025-10-30 at 2 42 56 PM" src="https://github.com/user-attachments/assets/fdc15470-bcfc-447e-954d-bad33eed8d1b" />

3. Deploy a gpt-4o model using either the Global Standard or Standard deployment option (depending on your quota availability).

    <img width="510" height="730" alt="Screenshot 2025-10-30 at 2 44 45 PM" src="https://github.com/user-attachments/assets/a54e35ea-e7cb-4684-8f25-acfa9a2c8895" />

4. When your project is created, the Agents playground will be opened.

    <img width="1508" height="862" alt="Screenshot 2025-10-30 at 2 45 49 PM" src="https://github.com/user-attachments/assets/95960585-ab5d-431b-8aaf-879ecf8265a2" />

5. Select Overview and Copy the Azure AI Foundry project endpoint values to a notepad, as we’ll use them to connect to your project in a client application.

### Create an agent client app

1. Open Azure portal at `https://portal.azure.com` signing in with your Azure credentials if prompted.

2. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal,
   selecting a PowerShell environment with no storage in your subscription.

3. In the Settings menu, select Go to Classic version (this is required to use the code editor).

4. Enter the following commands to clone the GitHub repo containing the code files for this exercise

    ```
    rm -r ai-agents -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-agents ai-agents
    ```

   <img width="1102" height="399" alt="Screenshot 2025-10-30 at 2 55 18 PM" src="https://github.com/user-attachments/assets/15494e29-df6a-4536-bdd4-381c80c4933a" />

6. Enter the following command to change the working directory to the folder containing the code files and list them all.

    ```
    cd ai-agents/Labfiles/02-build-ai-agent/Python
    ls -a -l
    ```
### Configure the application settings

1. Enter the following command to install the libraries we’ll use:

    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt azure-ai-projects
    ```
2. Enter the following command to edit the configuration file that has been provided:
    `code .env`
3. In the code file, replace the your_project_endpoint placeholder with the endpoint for your project
   (copied from the project Overview page in the Azure AI Foundry portal) and ensure that the
   MODEL_DEPLOYMENT_NAME variable is set to your model deployment name (which should be gpt-4o).

4. Use the CTRL+S command to save your changes and then use the CTRL+Q command to close the code editor
   while keeping the cloud shell command line open.

### Write code for an agent app

1. Enter the following command to edit the code file that has been provided:
    `code agent.py`
   
2. Add the following code to import the classes we’ll need to build an Azure AI agent
   that uses the built-in code interpreter tool:

   ```
   # Add references
    from azure.identity import DefaultAzureCredential
    from azure.ai.agents import AgentsClient
    from azure.ai.agents.models import FilePurpose, CodeInterpreterTool, ListSortOrder, MessageRole
   ```

3. Add the following code to connect to the Azure AI project.

    ```
    # Connect to the Agent client
    agent_client = AgentsClient(
        endpoint=project_endpoint,
        credential=DefaultAzureCredential
            (exclude_environment_credential=True,
             exclude_managed_identity_credential=True)
    )
    with agent_client:
    ```
    The code connects to the Azure AI Foundry project using the current Azure credentials.
    The final with agent_client statement starts a code block that defines the scope of the client,
    ensuring it’s cleaned up when the code within the block is finished.
   
4. Add the following code to upload the data file to the project and create a CodeInterpreterTool that
   can access the data in it:

    ```
    # Upload the data file and create a CodeInterpreterTool
    file = agent_client.files.upload_and_poll(
         file_path=file_path, purpose=FilePurpose.AGENTS
    )
    print(f"Uploaded {file.filename}")
    
    code_interpreter = CodeInterpreterTool(file_ids=[file.id])
    ```
5. Add the following code to define an AI agent that analyzes data and can use the code interpreter
   tool you defined previously:

    ```
    # Define an agent that uses the CodeInterpreterTool
    agent = agent_client.create_agent(
         model=model_deployment,
         name="data-agent",
         instructions="You are an AI agent that analyzes the data in the file that has been uploaded. Use Python to calculate statistical metrics as necessary.",
         tools=code_interpreter.definitions,
         tool_resources=code_interpreter.resources,
    )
    print(f"Using agent: {agent.name}")
    ```
6. Add the following code to start a thread on which the chat session with the agent will run:

    ```
    # Create a thread for the conversation
    thread = agent_client.threads.create()
    ```
7. The next section of code sets up a loop for a user to enter a prompt, ending when the user enters “quit”.

8. Add the following code to add a user message to the prompt (along with the data from the file that was loaded
   previously), and then run thread with the agent.

   ```
   # Send a prompt to the agent
    message = agent_client.messages.create(
         thread_id=thread.id,
         role="user",
         content=user_prompt,
     )
    
    run = agent_client.runs.create_and_process(thread_id=thread.id, agent_id=agent.id)
   ```
9. Add the following code to check for any errors.

    ```
    # Check the run status for failures
    if run.status == "failed":
         print(f"Run failed: {run.last_error}")
    ```
10. Add the following code to retrieve the messages from the completed thread and display the last one that
    was sent by the agent.

    ```
    # Show the latest response from the agent
    last_msg = agent_client.messages.get_last_message_text_by_role(
        thread_id=thread.id,
        role=MessageRole.AGENT,
    )
    if last_msg:
        print(f"Last Message: {last_msg.text.value}")
    ```
11. Add the following code to print out the messages from the conversation thread; reversing the order to show
    them in chronological sequence

    ```
    # Get the conversation history
    print("\nConversation Log:\n")
    messages = agent_client.messages.list(thread_id=thread.id, order=ListSortOrder.ASCENDING)
    for message in messages:
        if message.text_messages:
            last_msg = message.text_messages[-1]
            print(f"{message.role}: {last_msg.text.value}\n")
    ```
12. Add the following code to delete the agent and thread when no longer needed.
    ```
    # Clean up
    agent_client.delete_agent(agent.id)
    ```
13. Review the code, using the comments to understand how it:
      - Connects to the AI Foundry project.
      - Uploads the data file and creates a code interpreter tool that can access it.
      - Creates a new agent that uses the code interpreter tool and has explicit instructions to use Python as necessary for statistical analysis.
      - Runs a thread with a prompt message from the user along with the data to be analyzed.
      - Checks the status of the run in case there’s a failure
      - Retrieves the messages from the completed thread and displays the last one sent by the agent.
      - Displays the conversation history
      - Deletes the agent and thread when they’re no longer required.
        
14. Save and Quit the code editor

### Sign into Azure and run the app

1. Enter the following command to sign into Azure.
    `az login`

2. When prompted, follow the instructions to open the sign-in page in a new tab and enter the authentication code provided
   and our Azure credentials. Then complete the sign in process in the command line, selecting the subscription
   containing our Azure AI Foundry hub if prompted.

3. Enter the following command to run the application:
    ```
    cd ai-agents/Labfiles/02-build-ai-agent/Python
     python agent.py```
  The application runs using the credentials for your authenticated Azure session to connect to your project and 
  create and run the agent.
  
4. View the data that the app has loaded from the data.txt text file. Then enter a prompt such as:
    ```
    What's the category with the highest cost?
    ```

   <img width="1367" height="620" alt="Screenshot 2025-10-30 at 6 13 51 PM" src="https://github.com/user-attachments/assets/a27b13ab-decb-4a9b-b2b5-02266214bb2c" />

6. The thread is stateful, so it retains the conversation history - meaning that the agent has the full context for each response. Enter quit when you’re done.
   
7. Review the conversation messages that were retrieved from the thread - which may include messages the agent generated to explain its steps when using the code interpreter tool.

### Summary
In this exercise, we used the Azure AI Agent Service SDK to create a client application that uses an AI agent. 
The agent can use the built-in Code Interpreter tool to run dynamic Python code to perform statistical analyses.

Clean up the resources from Azure portal at `https://portal.azure.com`
