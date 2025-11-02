# Develop a multi-agent solution with Azure AI Foundry Agent Service

## What are connected agents?
Connected agents are a feature in the Azure AI Foundry Agent Service that allows us to break large tasks into smaller, 
specialized roles without building a custom orchestrator or hardcoding routing logic. Instead of relying on one agent 
to do everything, we can create multiple agents with clearly defined responsibilities that collaborate to accomplish tasks.
At the center of this system, there's a main agent that interprets user input and delegates tasks to connected sub-agents.

## Set up a multi-agent solution with connected agents
1. Initialize the agents client
2. Create an agent to connect to the main agent
3. Initialize the connected agent tool
4. Create the main agent
5. Create a thread and send a message
6. Run the agent workflow
7. Handle the results

## Exercise - Develop a multi-agent app with Azure AI Foundry
In this exercise, we’ll create a project that orchestrates multiple AI agents using Azure AI Foundry Agent Service. 
We’ll design an AI solution that assists with ticket triage. The connected agents will assess the ticket’s priority, 
suggest a team assignment, and determine the level of effort required to complete the ticket.

### Create an Azure AI Foundry project
1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using your Azure credentials.

2. Select Create an agent.  Create a project, enter a valid name for the project

3. Confirm the following settings for your project:

    <img width="683" height="695" alt="Screenshot 2025-11-01 at 4 24 03 PM" src="https://github.com/user-attachments/assets/3d7a060f-8cb3-46f8-ab93-5d7c8c0da0c1" />

4. Deploy a gpt-4o model using either the Global Standard or Standard deployment option.

    <img width="768" height="685" alt="Screenshot 2025-11-01 at 4 26 11 PM" src="https://github.com/user-attachments/assets/49873b68-a938-43c4-8213-58a51c0dd0c2" />

5. Copy the Azure AI Foundry project endpoint from Overview page values to a notepad, as we’ll use them to connect to
   your project in a client application.

### Create an AI Agent client app

#### Prepare the environment

1. Browse to the Azure portal at https://portal.azure.com; signing in with your Azure credentials if prompted.

2. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the
   Azure portal, selecting a PowerShell environment

3. In the cloud shell toolbar, in the Settings menu, select Go to Classic version

4. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for
   this exercise

     ```
     rm -r ai-agents -f
     git clone https://github.com/MicrosoftLearning/mslearn-ai-agents ai-agents
     ```
5. Change the working directory to the folder containing the code files and list them all.

    ```
    cd ai-agents/Labfiles/03b-build-multi-agent-solution/Python
    ls -a -l
    ```
    <img width="981" height="826" alt="Screenshot 2025-11-01 at 4 43 15 PM" src="https://github.com/user-attachments/assets/17ecfce9-e87d-4f55-867e-591c3bfa5b92" />

#### Configure the application settings

1. Enter the following command to install the libraries

    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt azure-ai-projects
    ```
2. Edit the configuration file that is provided:

    `code .env`
3. In the code file, replace the your_project_endpoint placeholder with the endpoint for your project
   (copied from the project Overview page in the Azure AI Foundry portal), and the your_model_deployment
   placeholder with the name you assigned to your gpt-4o model deployment

4. Use the CTRL+S command to save your changes and then use the CTRL+Q command to close the code editor

#### Create AI agents

1. Edit the agent_triage.py file:

    `code agent_triage.py`
   
3. Import the classes we’ll need:

    ```
    # Add references
    from azure.ai.agents import AgentsClient
    from azure.ai.agents.models import ConnectedAgentTool, MessageRole, ListSortOrder, ToolSet, FunctionTool
    from azure.identity import DefaultAzureCredential
    ```
4. Create an AgentsClient connected to the project:

    ```
    # Connect to the agents client
    agents_client = AgentsClient(
         endpoint=project_endpoint,
         credential=DefaultAzureCredential(
             exclude_environment_credential=True, 
             exclude_managed_identity_credential=True
         ),
    )
    ```
5. Add code for Create an agent to prioritize support tickets

    ```
    # Create an agent to prioritize support tickets
    priority_agent_name = "priority_agent"
    priority_agent_instructions = """
    Assess how urgent a ticket is based on its description.
    
    Respond with one of the following levels:
    - High: User-facing or blocking issues
    - Medium: Time-sensitive but not breaking anything
    - Low: Cosmetic or non-urgent tasks
    
    Only output the urgency level and a very brief explanation.
    """
    
    priority_agent = agents_client.create_agent(
         model=model_deployment,
         name=priority_agent_name,
         instructions=priority_agent_instructions
    )
    ```
6. Create an agent to assign tickets to the appropriate team

    ```
    # Create an agent to assign tickets to the appropriate team
    team_agent_name = "team_agent"
    team_agent_instructions = """
    Decide which team should own each ticket.
    
    Choose from the following teams:
    - Frontend
    - Backend
    - Infrastructure
    - Marketing
    
    Base your answer on the content of the ticket. Respond with the team name and a very brief explanation.
    """
    
    team_agent = agents_client.create_agent(
         model=model_deployment,
         name=team_agent_name,
         instructions=team_agent_instructions
    )
    ```
7. Create an agent to estimate effort for a support ticket.

    ```
    # Create an agent to estimate effort for a support ticket
    effort_agent_name = "effort_agent"
    effort_agent_instructions = """
    Estimate how much work each ticket will require.
    
    Use the following scale:
    - Small: Can be completed in a day
    - Medium: 2-3 days of work
    - Large: Multi-day or cross-team effort
    
    Base your estimate on the complexity implied by the ticket. Respond with the effort level and a brief justification.
    """
    
    effort_agent = agents_client.create_agent(
         model=model_deployment,
         name=effort_agent_name,
         instructions=effort_agent_instructions
    )
    ```
8. So far, we’ve created three agents; each of which has a specific role in triaging a support ticket.
   Now we'll create ConnectedAgentTool objects for each of these agents so they can be used by other agents.

9. Create connected agent tools for the support agents

    ```
    # Create connected agent tools for the support agents
    priority_agent_tool = ConnectedAgentTool(
         id=priority_agent.id, 
         name=priority_agent_name, 
         description="Assess the priority of a ticket"
    )
        
    team_agent_tool = ConnectedAgentTool(
         id=team_agent.id, 
         name=team_agent_name, 
         description="Determines which team should take the ticket"
    )
        
    effort_agent_tool = ConnectedAgentTool(
         id=effort_agent.id, 
         name=effort_agent_name, 
         description="Determines the effort required to complete the ticket"
    )
    ```
    Now, create a primary agent that will coordinate the ticket triage process, using the connected agents
    as required.
   
11. Create an agent to triage support ticket processing by using connected agents

      ```
      # Create an agent to triage support ticket processing by using connected agents
    triage_agent_name = "triage-agent"
    triage_agent_instructions = """
    Triage the given ticket. Use the connected tools to determine the ticket's priority, 
    which team it should be assigned to, and how much effort it may take.
    """
    
    triage_agent = agents_client.create_agent(
         model=model_deployment,
         name=triage_agent_name,
         instructions=triage_agent_instructions,
         tools=[
             priority_agent_tool.definitions[0],
             team_agent_tool.definitions[0],
             effort_agent_tool.definitions[0]
         ]
    )
      ```
12. Use the agents to triage a support issue

      ```
      # Use the agents to triage a support issue
      print("Creating agent thread.")
      thread = agents_client.threads.create()  
      
      # Create the ticket prompt
      prompt = input("\nWhat's the support problem you need to resolve?: ")
          
      # Send a prompt to the agent
      message = agents_client.messages.create(
           thread_id=thread.id,
           role=MessageRole.USER,
           content=prompt,
      )   
          
      # Run the thread usng the primary agent
      print("\nProcessing agent thread. Please wait.")
      run = agents_client.runs.create_and_process(thread_id=thread.id, agent_id=triage_agent.id)
              
      if run.status == "failed":
           print(f"Run failed: {run.last_error}")
      
      # Fetch and display messages
      messages = agents_client.messages.list(thread_id=thread.id, order=ListSortOrder.ASCENDING)
      for message in messages:
           if message.text_messages:
               last_msg = message.text_messages[-1]
               print(f"{message.role}:\n{last_msg.text.value}\n")
         
      ```
13. Clean up to delete the agents when they are no longer required:

      ```
      # Clean up
    print("Cleaning up agents:")
    agents_client.delete_agent(triage_agent.id)
    print("Deleted triage agent.")
    agents_client.delete_agent(priority_agent.id)
    print("Deleted priority agent.")
    agents_client.delete_agent(team_agent.id)
    print("Deleted team agent.")
    agents_client.delete_agent(effort_agent.id)
    print("Deleted effort agent.")
      ```
14. Save and Quit the code.

#### Sign into Azure and run the app

1. Enter the following command to sign into Azure.

    `az login`
   You must sign into Azure - even though the cloud shell session is already authenticated.
   
2. Run the application:

    `python agent_triage.py`
3. Enter the prompt as

    `Users can't reset their password from the mobile app.`
   
5. See the output generated by Agent below

   <img width="1922" height="629" alt="Screenshot 2025-11-01 at 5 28 30 PM" src="https://github.com/user-attachments/assets/9b0ac492-d5d7-4b47-9a3f-62a4ee1b32db" />

   <img width="974" height="628" alt="Screenshot 2025-11-01 at 5 31 52 PM" src="https://github.com/user-attachments/assets/5f71f2ae-a154-44bc-b572-35b727efc3d6" />

#### Clean up

Delete resource group from Azure portal `https://portal.azure.com`
