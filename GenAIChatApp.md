## Create a generative AI chat app

In this exercise, we use the Azure AI Foundry Python SDK to create a simple chat app that connects to a project and chats with a language model. [Code](/chat-app.py)

# Deploy a model in an Azure AI Foundry project

1. Open the Azure AI Foundry portal at `https://ai.azure.com` and sign in using your Azure credentials.

2. Search for the `gpt-4o` model and select that model

<img width="1498" height="857" alt="Screenshot 2025-10-20 at 9 09 56 PM" src="https://github.com/user-attachments/assets/81c93a25-c602-4b97-bec9-54d7bb47c15e" />

<img width="640" height="666" alt="Screenshot 2025-10-20 at 9 11 41 PM" src="https://github.com/user-attachments/assets/cd5e2e57-4e74-4bb4-9965-250be98f9c51" />
  
3. Create a client application to chat with the model
   
    One can use the Azure AI Foundry and Azure OpenAI SDKs to develop an application that chats with it.
   
5. In the `Overview` page of the project, in the Endpoints and keys area, ensure that the Azure AI Foundry library is selected and view the Azure AI Foundry project endpoint.

    <img width="1511" height="724" alt="Screenshot 2025-10-20 at 9 20 06 PM" src="https://github.com/user-attachments/assets/1082ebc5-a4f8-40bf-a982-95d0279ae501" />

6. Open a new browser tab (keeping the Azure AI Foundry portal open in the existing tab). Then in the new tab, browse to the Azure portal at `https://portal.azure.com`

7. Use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal selecting a PowerShell environment with no storage in your subscription.

8. In the cloud shell toolbar, in the Settings menu, select Go to Classic version

9. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for this exercise
    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    <img width="1113" height="392" alt="Screenshot 2025-10-20 at 9 24 38 PM" src="https://github.com/user-attachments/assets/4ac08ea5-716c-41ea-86b5-e4f190e908b7" />

10. After the repo has been cloned, navigate to the folder containing the chat application code files and view them:

    ```
    cd mslearn-ai-foundry/labfiles/chat-app/python
    ls -a -l
    ```
    <img width="694" height="195" alt="Screenshot 2025-10-20 at 9 26 10 PM" src="https://github.com/user-attachments/assets/a2a60c14-4b92-4bfb-9da9-175207b20d13" />

11. In the cloud shell command-line pane, enter the following command to install the libraries you’ll use:

    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt azure-identity azure-ai-projects openai
    ```
    <img width="972" height="499" alt="Screenshot 2025-10-20 at 9 27 57 PM" src="https://github.com/user-attachments/assets/adad7005-7a53-48ad-9ac5-1461b07f45cd" />

12. Enter the following command to edit the configuration file that has been provided:

    ```
    code .env
    ```
13. In the code file, replace the your_project_endpoint placeholder with the appropriate endpoint for your model,
      and the your_model_deployment placeholder with the name assigned to your gpt-4o model deployment. Save and Quit
    
15. Enter the following command to edit the code file that has been provided:
    ```
    code chat-app.py
    ```
16. Add the following code to reference the namespaces in the libraries you installed previously:

    ```
    # Add references
    from azure.identity import DefaultAzureCredential
    from azure.ai.projects import AIProjectClient
    from openai import AzureOpenAI
    ```
17. Add the following code to connect to your Azure AI Foundry project:

    ```
    # Initialize the project client
    project_client = AIProjectClient(            
             credential=DefaultAzureCredential(
                 exclude_environment_credential=True,
                 exclude_managed_identity_credential=True
             ),
             endpoint=project_endpoint,
         )
    ```
18. Add the following code to create a client object for chatting with a model:

    ```
    # Get a chat client
    openai_client = project_client.get_openai_client(api_version="2024-10-21")
    ```
    
19. Add the following code to initialize a collection of messages with a system prompt.

    ```
    # Initialize prompt with system message
    prompt = [
             {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
         ]
    ```
20. Add the following code to add the user input to the prompt, retrieve the completion from your model, and add the completion to the prompt
      (so that you retain chat history for future iterations): Save the changes

    ```
    # Get a chat completion
    prompt.append({"role": "user", "content": input_text})
    response = openai_client.chat.completions.create(
             model=model_deployment,
             messages=prompt)
    completion = response.choices[0].message.content
    print(completion)
    prompt.append({"role": "assistant", "content": completion})
    ```
22. Sign into Azure and run the app

    ```
    az login
    ```

23. After you have signed in, enter the following command to run the application:

    ```
    python chat-app.py
    ```
24. Below are the user prompts. Enter `quit` to exit the program. 

    <img width="1507" height="766" alt="Screenshot 2025-10-20 at 9 51 47 PM" src="https://github.com/user-attachments/assets/14a9fb6e-3c24-4cb1-8a28-422ed133ef9c" />

25. In this exercise, you used the Azure AI Foundry SDK to create a client application for a generative AI model that you deployed in an Azure AI Foundry project.

27. Clean up the resources - Delete resource group

    
