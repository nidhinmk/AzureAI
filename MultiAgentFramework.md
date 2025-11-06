# Orchestrate a multi-agent solution using the Microsoft Agent Framework

The Microsoft Agent Framework SDK's agent orchestration framework makes it possible to design, manage, and scale complex multi-agent workflows without having to manually handle the details of agent coordination. Instead of relying on a single agent to manage every aspect of a task, we can combine multiple specialized agents.

## Supported orchestration patterns
Microsoft Agent Framework provides several orchestration patterns directly in the SDK, each offering a different approach to coordinating agents. 

Concurrent orchestration - Broadcast the same task to multiple agents at once and collect their results independently. Useful for parallel analysis, independent subtasks, or ensemble decision making.
Sequential orchestration - Pass the output from one agent to the next in a fixed order. Ideal for step-by-step workflows, pipelines, and progressive refinement.
Handoff orchestration - Dynamically transfer control between agents based on context or rules. Great for escalation, fallback, and expert routing where one agent works at a time.
Group chat orchestration - Coordinate a shared conversation among multiple agents (and optionally a human), managed by a chat manager that chooses who speaks next. Best for brainstorming, collaborative problem solving, and building consensus.
Magentic orchestration - A manager-driven approach that plans, delegates, and adapts across specialized agents. Suited to complex, open-ended problems where the solution path evolves.

## Exercise - Develop a multi-agent solution
In this exercise, we create an application that automatically triages and resolves issues presented in log files of a system. Using Azure AI Agents, we create an incident manager agent and a devops agent that collaborates to fix the issues.

We’ll practice using the sequential orchestration pattern in the Microsoft Agent Framework SDK. We’ll create a simple pipeline of three agents that work together to process customer feedback and suggest next steps. We’ll create the following agents:

- The Summarizer agent will condense raw feedback into a short, neutral sentence.
- The Classifier agent will categorize the feedback as Positive, Negative, or a Feature request.
- Finally, the Recommended Action agent will recommend an appropriate follow-up step.

### Deploy a model in an Azure AI Foundry project

1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using your Azure credentials.

2. Explore models and capabilities section, search and select the gpt-4o model.

3. In the navigation pane on the left, select Models and endpoints and select the gpt-4o deployment.

### Create an AI Agent client app

1. Browse to the Azure portal at `https://portal.azure.com`; signing in with your Azure credentials

2. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment.

3. In the Settings menu, select Go to Classic version.

4. Enter the following commands to clone the GitHub repo containing the code files for this exercise

    ```
    rm -r ai-agents -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-agents ai-agents
    ```
    
5. Change the working directory to the folder containing the code files and list them all.

    ```
    cd ai-agents/Labfiles/05-agent-orchestration/Python
    ls -a -l
    ```

### Configure the application settings

1. Enter the following command to install the libraries

        python -m venv labenv
        ./labenv/bin/Activate.ps1
        pip install azure-identity agent-framework
   
2. Edit the configuration file

        code .env
3. Replace the `your_openai_endpoint` placeholder with the endpoint for the project. Replace the `your_model_deployment` placeholder with the name we assigned to our gpt-4o model deployment;
    Save Ctrl+S and Quit Ctrl+Q.
   
### Create AI agents

1. edit the agents.py file

        code agents.py

2. Add the following code to reference the namespaces in the libraries we’ll need to implement the agent.

        # Add references
        import asyncio
        from typing import cast
        from agent_framework import ChatMessage, Role, SequentialBuilder, WorkflowOutputEvent
        from agent_framework.azure import AzureAIAgentClient
        from azure.identity import AzureCliCredential

3. Create the chat client. AzureCliCredential object will allow the code to authenticate to our Azure account. The AzureAIAgentClient object will automatically include the Azure AI Foundry project settings from the .env configuration.

        # Create the chat client
        credential = AzureCliCredential()
        async with (
            AzureAIAgentClient(async_credential=credential) as chat_client,
        ):

4. Create agents

        # Create agents
        summarizer = chat_client.create_agent(
            instructions=summarizer_instructions,
            name="summarizer",
        )
        
        classifier = chat_client.create_agent(
            instructions=classifier_instructions,
            name="classifier",
        )
        
        action = chat_client.create_agent(
            instructions=action_instructions,
            name="action",
        )
    
### Create a sequential orchestration

1. Initialize the current feedback

        # Initialize the current feedback
        feedback="""
        I use the dashboard every day to monitor metrics, and it works well overall. 
        But when I'm working late at night, the bright screen is really harsh on my eyes. 
        If you added a dark mode option, it would make the experience much more comfortable.
        """

2. Build a sequential orchestration. The agents will process the feedback in the order they are added to the orchestration.

        # Build sequential orchestration
        workflow = SequentialBuilder().participants([summarizer, classifier, action]).build()

3. Run and collect outputs. This code runs the orchestration and collects the output from each of the participating agents.

        # Run and collect outputs
        outputs: list[list[ChatMessage]] = []
        async for event in workflow.run_stream(f"Customer feedback: {feedback}"):
            if isinstance(event, WorkflowOutputEvent):
                outputs.append(cast(list[ChatMessage], event.data))

4. Display outputs. This code formats and displays the messages from the workflow outputs you collected from the orchestration.

        # Display outputs
        if outputs:
            for i, msg in enumerate(outputs[-1], start=1):
                name = msg.author_name or ("assistant" if msg.role == Role.ASSISTANT else "user")
                print(f"{'-' * 60}\n{i:02d} [{name}]\n{msg.text}")
    
5. Save and Quit

### Sign into Azure and run the app

1. Enter the following command to sign into Azure.

        az login
2. Run the application

        python agents.py
    
3. The output is as follows

      ------------------------------------------------------------
       01 [user]
       Customer feedback:
           I use the dashboard every day to monitor metrics, and it works well overall.
           But when I'm working late at night, the bright screen is really harsh on my eyes.
           If you added a dark mode option, it would make the experience much more comfortable.
      
       ------------------------------------------------------------
       02 [summarizer]
       User requests a dark mode for better nighttime usability.
       ------------------------------------------------------------
       03 [classifier]
       Feature request
       ------------------------------------------------------------
       04 [action]
       Log as enhancement request for product backlog.
 
4. Running the code using different feedback inputs, such as:

   ```I use the dashboard every day to monitor metrics, and it works well overall. But when I'm working late at night, the bright screen is really harsh on my eyes. If you added a dark mode option, it would make the experience much more comfortable.```


   ```I reached out to your customer support yesterday because I couldn't access my account. The representative responded almost immediately, was polite and professional, and fixed the issue within minutes. Honestly, it was one of the best support experiences I've ever had.```

### Summary

In this exercise, we practiced sequential orchestration with the Microsoft Agent Framework SDK, combining multiple agents into a single, streamlined workflow. 

### Cleanp the resources

Delete resource group from Azure portal. 
