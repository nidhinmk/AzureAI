# Use a custom function in an AI agent

In this exercise we’ll explore creating an agent that can use custom functions as a tool to complete tasks. 
We’ll build a simple technical support agent that can collect details of a technical problem and generate a support ticket.

## Create an Azure AI Foundry project
1. Open the Azure AI Foundry portal at https://ai.azure.com and sign in using your Azure credentials.

2. Select Create an agent.

3. Create a project, enter a valid name for your project and expand Advanced options.

     <img width="517" height="549" alt="Screenshot 2025-11-01 at 8 14 49 AM" src="https://github.com/user-attachments/assets/79f07807-0732-4beb-8ab2-6d5adf69f4e0" />

4. Confirm the following settings for your project:
      - Azure AI Foundry resource: A valid name for your Azure AI Foundry resource
      - Subscription: Your Azure subscription
      - Resource group: Create or select a resource group
      - Region: Select any AI Foundry recommended

5. Deploy a gpt-4o model using either the Global Standard or Standard deployment option

     <img width="1489" height="856" alt="Screenshot 2025-11-01 at 8 16 29 AM" src="https://github.com/user-attachments/assets/6c0f5a72-ace2-4adc-b959-582bf5a8043c" />

6. Copy the Azure AI Foundry project endpoint values to a notepad, as we’ll use them to connect to your project in a client application.

   <img width="1505" height="828" alt="Screenshot 2025-11-01 at 8 18 42 AM" src="https://github.com/user-attachments/assets/834b0d6d-54e0-4186-b2ab-529f51ff2aef" />

     
## Develop an agent that uses function tools

We'll develop an app that implements an agent using custom function tools.

### Clone the repo containing the application code

1. Browse to the Azure portal at https://portal.azure.com; signing in with the Azure credentials if prompted.

2. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment with no
   storage in the subscription.

3. In the cloud shell toolbar, in the Settings menu, select Go to Classic version (this is required to use the code editor).

4. Enter the following commands to clone the GitHub repo containing the code files for this exercise.

    ```
    rm -r ai-agents -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-agents ai-agents
    ```
5. Change the working directory to the folder containing the code files and list them all.

    ```
    cd ai-agents/Labfiles/03-ai-agent-functions/Python
    ls -a -l
    ```
    
### Configure the application settings

1. Enter the following command to install the libraries we’ll use:

    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt azure-ai-projects
    ```
2. Edit the configuration file that has been provided:

      `code .env`
   
3. In the code file, replace the your_project_endpoint placeholder with the endpoint for the project (copied from the project Overview page in the Azure AI Foundry portal) and
   ensure that the MODEL_DEPLOYMENT_NAME variable is set to your model deployment name (which should be gpt-4o). Save (Ctrl+S) and Quit(Ctrl_Q)

### Define a custom function

1. Edit the code file that has been provided for the function code:

    `code user_functions.py`
   
2. Create a function to submit a support ticket and add the following code, which generates a ticket number and saves a support ticket as a text file.

    ```
    # Create a function to submit a support ticket
    def submit_support_ticket(email_address: str, description: str) -> str:
         script_dir = Path(__file__).parent  # Get the directory of the script
         ticket_number = str(uuid.uuid4()).replace('-', '')[:6]
         file_name = f"ticket-{ticket_number}.txt"
         file_path = script_dir / file_name
         text = f"Support ticket: {ticket_number}\nSubmitted by: {email_address}\nDescription:\n{description}"
         file_path.write_text(text)
        
         message_json = json.dumps({"message": f"Support ticket {ticket_number} submitted. The ticket file is saved as {file_name}"})
         return message_json
    ```
3. Define a set of callable functions and add the following code, which statically defines a set of callable functions in this code file
   (in this case, there’s only one - but in a real solution you may have multiple functions that your agent can call):

    ````
    # Define a set of callable functions
    user_functions: Set[Callable[..., Any]] = {
         submit_support_ticket
     }
    ```

4. Ctrl+S the file

### Write code to implement an agent that can use the custom function

1. Edit the agent code.

     `code agent.py`
2. Review the existing code, which retrieves the application configuration settings and sets up a loop in which the user can enter prompts for the agent. The rest of the file includes comments      where we’ll add the necessary code to implement the technical support agent.

3. Add code to import the classes you’ll need to build an Azure AI agent that uses your function code as a tool:

     ```
     # Add references
     from azure.identity import DefaultAzureCredential
     from azure.ai.agents import AgentsClient
     from azure.ai.agents.models import FunctionTool, ToolSet, ListSortOrder, MessageRole
     from user_functions import user_functions
     ```
4. Connect to the Azure AI project using the current Azure credentials.

     ```
     # Connect to the Agent client
     agent_client = AgentsClient(
         endpoint=project_endpoint,
         credential=DefaultAzureCredential
             (exclude_environment_credential=True,
              exclude_managed_identity_credential=True)
     )
     ```
5. Add the function code to a toolset, and then create an agent that can use the toolset and a thread on which to run the chat session.

     ```
     # Define an agent that can use the custom functions
     with agent_client:
     
          functions = FunctionTool(user_functions)
          toolset = ToolSet()
          toolset.add(functions)
          agent_client.enable_auto_function_calls(toolset)
                 
          agent = agent_client.create_agent(
              model=model_deployment,
              name="support-agent",
              instructions="""You are a technical support agent.
                              When a user has a technical issue, you get their email address and a description of the issue.
                              Then you use those values to submit a support ticket using the function available to you.
                              If a file is saved, tell the user the file name.
                           """,
              toolset=toolset
          )
     
          thread = agent_client.threads.create()
          print(f"You're chatting with: {agent.name} ({agent.id})")

     ```
6. Add the user’s prompt as a message and run the thread.

     ```
     # Send a prompt to the agent
     message = agent_client.messages.create(
          thread_id=thread.id,
          role="user",
          content=user_prompt
     )
     run = agent_client.runs.create_and_process(thread_id=thread.id, agent_id=agent.id)
     ```
7. Add the following code to show any errors that occur.

     ```
     # Check the run status for failures
     if run.status == "failed":
          print(f"Run failed: {run.last_error}")
     ```
8. Retrieve the messages from the completed thread and display the last one that was sent by the agent.

     ```
     # Show the latest response from the agent
     last_msg = agent_client.messages.get_last_message_text_by_role(
         thread_id=thread.id,
         role=MessageRole.AGENT,
     )
     if last_msg:
          print(f"Last Message: {last_msg.text.value}")
     ```
9. Print out the messages from the conversation thread; ordering them in chronological sequence

     ```
     # Get the conversation history
     print("\nConversation Log:\n")
     messages = agent_client.messages.list(thread_id=thread.id, order=ListSortOrder.ASCENDING)
     for message in messages:
          if message.text_messages:
             last_msg = message.text_messages[-1]
             print(f"{message.role}: {last_msg.text.value}\n")
     ```
10. Delete the agent and thread when no longer needed.

    ```
    # Clean up
     agent_client.delete_agent(agent.id)
     print("Deleted agent")
    ```
11. Review the code, using the comments to understand how it:
     - Adds your set of custom functions to a toolset
     - Creates an agent that uses the toolset.
     - Runs a thread with a prompt message from the user.
     - Checks the status of the run in case there’s a failure
     - Retrieves the messages from the completed thread and displays the last one sent by the agent.
     - Displays the conversation history
     - Deletes the agent and thread when they’re no longer required.
   
12. Save the code file (CTRL+S) when you have finished. You can also close the code editor (CTRL+Q);

### Sign into Azure and run the app

1. Enter the following command to sign into Azure.

     `az login`
2. After signing in, enter the following command to run the application:

     `python agent.py`
3. Enter a prompt such as:

     `I have a technical problem`
4. The agent may ask for your email address and a description of the issue. We can use any email address (for example, nidhin@azure.com) and any issue description (for example my computer           won't start)

     <img width="1507" height="321" alt="Screenshot 2025-11-01 at 9 24 24 AM" src="https://github.com/user-attachments/assets/5e8ac209-e462-4788-9697-1bcdcdd080ba" />

5. We can continue the conversation if you like. The thread is stateful, so it retains the conversation history - meaning that the agent has the full context for each response. Enter quit when      we’re done.

     <img width="1511" height="611" alt="Screenshot 2025-11-01 at 9 25 30 AM" src="https://github.com/user-attachments/assets/aef2360e-4d97-422f-8622-378df40ea4ce" />

6. The tool should have saved support tickets in the app folder. We can use the `ls` command to check, and then use the `cat` command to view the file contents, like this:

    `cat ticket-<ticket_num>.txt`

     <img width="983" height="201" alt="Screenshot 2025-11-01 at 9 26 25 AM" src="https://github.com/user-attachments/assets/bf0b1dca-bd02-43b8-b1a3-46bbc9cccb50" />

### Clean up

Open the Azure portal at https://portal.azure.com and view the contents of the resource group and delete the same. 
