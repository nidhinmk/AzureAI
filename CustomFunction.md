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

