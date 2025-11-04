# Develop an AI Agent with Microsoft Agent Framework

In this exercise, we’ll use Azure AI Agent Service and Microsoft Agent Framework to create an AI agent that processes expense claims.

## Deploy a model in an Azure AI Foundry project

1. Open the Azure AI Foundry portal at `https://ai.azure.com `and sign in using your Azure credentials.

2. Select the `gpt-4o` model.

3. Enter a valid name for your project and confirm the following settings.

## Create an agent client app
### Prepare the environment

1. Browse to the Azure portal at https://portal.azure.com; signing in with your Azure credentials

2. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment with no storage in your subscription.

3. In the Settings menu, select Go to Classic version

4. Enter the following commands to clone the GitHub repo containing the code files for this exercise

    ```
    rm -r ai-agents -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-agents ai-agents
    ```
5. Change the working directory to the folder containing the code files and list them all.

    ```
    cd ai-agents/Labfiles/04-agent-framework/python
    ls -a -l
    ```
### Configure the application settings

1. Install the libraries you’ll use
    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install azure-identity agent-framework
    ```
2. Edit the configuration file
   
    `code .env`
   
4. Replace the your_project_endpoint placeholder with the endpoint for the project (copied from the project Overview page in the Azure AI Foundry portal), and the your_model_deployment placeholder with the name we assigned to our gpt-4o model deployment.

### Write code for an agent app

1. Edit the agent code file that has been provided

    `code agent-framework.py`
2. Add the following code to reference the namespaces in the libraries you’ll need to implement the agent.

    ```
    # Add references
    from agent_framework import AgentThread, ChatAgent
    from agent_framework.azure import AzureAIAgentClient
    from azure.identity.aio import AzureCliCredential
    from pydantic import Field
    from typing import Annotated
    ```
3. Create a tool function for the email functionality, and add the following code to define a function that the agent will use to send email (tools are a way to add custom functionality to agents)

    ```
    # Create a tool function for the email functionality
    def send_email(
     to: Annotated[str, Field(description="Who to send the email to")],
     subject: Annotated[str, Field(description="The subject of the email.")],
     body: Annotated[str, Field(description="The text body of the email.")]):
         print("\nTo:", to)
         print("Subject:", subject)
         print(body, "\n")
    ```
4. Create a ChatAgent object with the tools and instructions. AzureCliCredential object will allow the code to authenticate to our Azure account. The AzureAIAgentClient object will automatically include the Azure AI Foundry project settings from the .env configuration.

    ```
    # Create a chat agent
    async with (
        AzureCliCredential() as credential,
        ChatAgent(
            chat_client=AzureAIAgentClient(async_credential=credential),
            name="expenses_agent",
            instructions="""You are an AI assistant for expense claim submission.
                            When a user submits expenses data and requests an expense claim, use the plug-in function to send an email to expenses@contoso.com with the subject 'Expense Claim`and a body that contains itemized expenses with a total.
                            Then confirm to the user that you've done so.""",
            tools=send_email,
        ) as agent,
    ):
    ```
5. Use the agent to process the expenses data

    ```
    # Use the agent to process the expenses data
    try:
        # Add the input prompt to a list of messages to be submitted
        prompt_messages = [f"{prompt}: {expenses_data}"]
        # Invoke the agent for the specified thread with the messages
        response = await agent.run(prompt_messages)
        # Display the response
        print(f"\n# Agent:\n{response}")
    except Exception as e:
        # Something went wrong
        print (e)
    ```
    
### Sign into Azure and run the app

1. Sign into Azure
    `az login`
2. Run the application
    `python agent-framework.py`
3. When asked what to do with the expenses data, enter the following prompt:
    `Submit an expense claim`
4. The agent should have composed an email for an expenses claim based on the data that was provided.

### Summary
In this exercise, we used the Microsoft Agent Framework SDK to create an agent with a custom tool.

### Clean up the resources

1. Delete resource group from the Azure portal `https://portal.azure.com`
