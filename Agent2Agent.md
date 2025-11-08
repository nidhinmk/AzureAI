# Discover Azure AI Agents with A2A

In this module, we will configure a routing agent, register remote agents, and build a coordinated workflow that allows multiple agents to collaborate effectively.

Before an A2A agent can participate in multi-agent workflows, it needs to explain what it can do. Agent Skills and how other agents or clients can discover those capabilities are exposed through an Agent Card.

An Agent Skill describes a specific capability or function that the agent can perform. The Agent Card is like a digital business card for the agent.

An Agent Executor defines how the agent processes incoming requests, generates responses, and communicates with clients or other agents.

## Exercise - Connect to remote agents with A2A protocol

In this exercise, we’ll use Azure AI Agent Service with the A2A protocol to create simple remote agents that interact with one another. These agents will assist technical writers with preparing their developer blog posts. A title agent will generate a headline, and an outline agent will use the title to develop a concise outline for the article. 

[a2a_agent.py](/a2a_agent.py)

[a2a_agent_executor.py](/a2a_agent_executor.py)

[a2a_routing_agent.py](a2a_routing_agent.py)

[a2a_server.py](/a2a_server.py)

### Create an Azure AI Foundry project

1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using the Azure credentials.

   <img width="642" height="682" alt="Screenshot 2025-11-07 at 5 34 58 PM" src="https://github.com/user-attachments/assets/4d28711a-5094-4929-8725-1866bb1683dc" />

2. Deploy a gpt-4o model using either the Global Standard or Standard deployment option.

   <img width="642" height="616" alt="Screenshot 2025-11-07 at 5 39 44 PM" src="https://github.com/user-attachments/assets/4ea396b9-0fb4-40af-8129-ed7e00d11bc1" />

3. Copy the Azure AI Foundry project endpoint values to a notepad, as we’ll use them to connect to your project in a client application.

### Create an A2A application

#### Clone the repo containing the application code

1. Browse to the Azure portal at `https://portal.azure.com` signing in with the Azure credentials.

2. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment. in the Settings menu, select Go to Classic version.

3. Clone the GitHub repo containing the code files for this exercise

        rm -r ai-agents -f
        git clone https://github.com/MicrosoftLearning/mslearn-ai-agents ai-agents

4. Change the working directory to the folder containing the code files and list them all.

        cd ai-agents/Labfiles/06-build-remote-agents-with-a2a/python
        ls -a -l

5. The provided files include the below. Each agent folder contains the Azure AI agent code and a server to host the agent. The routing agent is responsible for discovering and communicating with the title and outline agents. The client allows users to submit prompts to the routing agent. run_all.py launches all the servers and runs the client.

        python
         ├── outline_agent/
         │   ├── agent.py
         │   ├── agent_executor.py
         │   └── server.py
         ├── routing_agent/
         │   ├── agent.py
         │   └── server.py
         ├── title_agent/
         │   ├── agent.py
         |   ├── agent_executor.py
         │   └── server.py
         ├── client.py
         └── run_all.py

   <img width="850" height="436" alt="Screenshot 2025-11-07 at 5 43 37 PM" src="https://github.com/user-attachments/assets/77192d0a-0fd2-4570-8ff8-aea94747599d" />

#### Configure the application settings

1. Enter the following command to install the libraries.

        python -m venv labenv
        ./labenv/bin/Activate.ps1
        pip install -r requirements.txt azure-ai-projects a2a-sdk

2. Edit the configuration file.

        code .env
3.  In the code file, replace the your_project_endpoint placeholder with the endpoint for the project (copied from the project Overview page in the Azure AI Foundry portal) and ensure that the MODEL_DEPLOYMENT_NAME variable is set to the model deployment name (which should be gpt-4o).

4. After the changes, use the CTRL+S command to save your changes and then use the CTRL+Q command to close the code editor

#### Create a discoverable agent

In this task, we create the title agent that helps writers create trendy headlines for their articles. We also define the agent’s skills and card required by the A2A protocol to make the agent discoverable.

1. Navigate to the title_agent directory:

        cd title_agent

2. Edit the code file.

        code agent.py
   
3. Create the agents client and add the following code to connect to the Azure AI project:

        # Create the agents client
        self.client = AgentsClient(
            endpoint=os.environ['PROJECT_ENDPOINT'],
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            )
        )
   
4. Create the title agent

        # Create the title agent
        self.agent = self.client.create_agent(
            model=os.environ['MODEL_DEPLOYMENT_NAME'],
            name='title-agent',
            instructions="""
            You are a helpful writing assistant.
            Given a topic the user wants to write about, suggest a single clear and catchy blog post title.
            """,
        )

5. Create a thread for the chat session to create the chat thread.

        # Create a thread for the chat session
        thread = self.client.threads.create()

6. Send user message to submit the user’s prompt.

        # Send user message
        self.client.messages.create(thread_id=thread.id, role=MessageRole.USER, content=user_message)

7. Create and run the agent to initiate the agent’s response generation

        # Create and run the agent
        run = self.client.runs.create_and_process(thread_id=thread.id, agent_id=self.agent.id)

8. Save the code file (CTRL+S). Now we’re ready to share the agent’s skills and card with the A2A protocol.

9. Edit the title agent’s `server.py` file

        code server.py
   
10. Define agent skills to specify the agent’s functionality.

        # Define agent skills
        skills = [
            AgentSkill(
                id='generate_blog_title',
                name='Generate Blog Title',
                description='Generates a blog title based on a topic',
                tags=['title'],
                examples=[
                    'Can you give me a title for this article?',
                ],
            ),
        ]

11. Create agent card to define the metadata that makes the agent discoverable.

        # Create agent card
        agent_card = AgentCard(
            name='AI Foundry Title Agent',
            description='An intelligent title generator agent powered by Azure AI Foundry. '
            'I can help you generate catchy titles for your articles.',
            url=f'http://{host}:{port}/',
            version='1.0.0',
            default_input_modes=['text'],
            default_output_modes=['text'],
            capabilities=AgentCapabilities(),
            skills=skills,
        )

12. Create agent executor to initialize the agent executor using the agent card. The agent executor will act as a wrapper for the title agent we created.

        # Create agent executor
        agent_executor = create_foundry_agent_executor(agent_card)

13. Create request handler to handle incoming requests using the executor.

        # Create request handler
        request_handler = DefaultRequestHandler(
            agent_executor=agent_executor, task_store=InMemoryTaskStore()
        )

14. Create A2A application to create the A2A-compatible application instance. This code creates an A2A server that will share the title agent’s information and handle incoming requests for this agent using the title agent executor. Ctrl + S to save the file. 

        # Create A2A application
        a2a_app = A2AStarletteApplication(
            agent_card=agent_card, http_handler=request_handler
        )

#### Enable messages between the agents

In this task, we use the A2A protocol to enable the routing agent to send messages to the other agents. We also allow the title agent to receive messages by implementing the agent executor class.

1. Navigate to the `routing_agent` directory.

        cd ../routing_agent
   
2. Edit the code file.

       code agent.py

   The routing agent acts as an orchestrator that handles user messages and determines which remote agent should process the request.

  When a user message is received, the routing agent:
  
  - Starts a conversation thread.
  - Uses the create_and_process method to evaluate the best-matching agent for the user’s message.
  - The message is routed to the appropriate agent over HTTP using the send_message function.
  - The remote agent processes the message and returns a response.
  
  The routing agent finally captures the response and returns it to the user through the thread. The send_message method is async and must be awaited for the agent run to complete successfully.

4. Retrieve the remote agent’s A2A client using the agent name:

        # Retrieve the remote agent's A2A client using the agent name 
        client = self.remote_agent_connections[agent_name]

5. Construct the payload to send to the remote agent

        # Construct the payload to send to the remote agent
        payload: dict[str, Any] = {
            'message': {
                'role': 'user',
                'parts': [{'kind': 'text', 'text': task}],
                'messageId': message_id,
            },
        }

6. Wrap the payload in a SendMessageRequest object

        # Wrap the payload in a SendMessageRequest object
        message_request = SendMessageRequest(id=message_id, params=MessageSendParams.model_validate(payload))

7. Send the message to the remote agent client and await the response

        # Send the message to the remote agent client and await the response
        send_response: SendMessageResponse = await client.send_message(message_request=message_request)
   
8. Save the code file (CTRL+S) when we have finished. Now the routing agent is able to discover and send messages to the title agent. Next, we will create the agent executor code to handle those incoming messages from the routing agent.

9. Navigate to the `title_agent` directory.

        cd ../title_agent
   
10. Edit the code file. The AgentExecutor class implemenation must contain the methods execute and cancel. The cancel method has been provided. The execute method includes a TaskUpdater object that manages events and signals to the caller when the task is complete

        code agent_executor.py
    
11. In the execute method, add the following code to Process the request.

        # Process the request
        await self._process_request(context.message.parts, context.context_id, updater)

12. In the _process_request method, add the following code to Get the title agent.

        # Get the title agent
        agent = await self._get_or_create_agent()

13. Update the task status.

        # Update the task status
        await task_updater.update_status(
            TaskState.working,
            message=new_agent_text_message('Title Agent is processing your request...', context_id=context_id),
        )

14. Run the agent conversation

        # Run the agent conversation
        responses = await agent.run_conversation(user_message)

15. Update the task with the responses

        # Update the task with the responses
        for response in responses:
            await task_updater.update_status(
                TaskState.working,
                message=new_agent_text_message(response, context_id=context_id),
            )
    
16. Mark the task as complete. Now the title agent has been wrapped with an agent executor that the A2A protocol will use to handle messages.

        # Mark the task as complete
        final_message = responses[-1] if responses else 'Task completed.'
        await task_updater.complete(
            message=new_agent_text_message(final_message, context_id=context_id)
        )

#### Sign into Azure and run the app

1. In the cloud shell command-line pane, enter the following command to sign into Azure.

       az login
 
2. When prompted, follow the instructions to open the sign-in page in a new tab and enter the authentication code provided and our Azure credentials. Then complete the sign in process in the command line, selecting the subscription containing our Azure AI Foundry hub if prompted.

3. Enter the following command to run the application

       cd ..
       python run_all.py
 
4. Enter a prompt such as.

        Create a title and outline for an article about React programming.

        Create a title for Azure Agentic AI
   
6. We should see a response from the agent with the results.

   <img width="1420" height="883" alt="Screenshot 2025-11-07 at 6 48 53 PM" src="https://github.com/user-attachments/assets/348c6b1e-369d-4ac8-884f-5e051ab35c97" />


#### Summary
In this exercise, we used the Azure AI Agent Service SDK and the A2A Python SDK to create a remote multi-agent solution. We created a discoverable A2A-compatible agent and set up a routing agent to access the agent’s skills. We also implemented an agent executor to process incoming A2A messages and manage tasks.

#### Clean up

Delete resource group from Azure portal at `https://portal.azure.com`
