# Create question answering solutions with Azure AI Language 

In this module, we will see how to use Azure AI Language to create a knowledge base of question and answer pairs that can support an application or bot. [Code](/qna-app.py)

## Exercise - Create a Question Answering Solution

Azure AI Language includes a question answering capability that enables us to create a knowledge base of question and answer pairs that can be queried using natural language input, and is most commonly used as a resource that a bot can use to look up answers to questions submitted by users. In this exercise, we'll use the Azure AI Language Python SDK for text analytics to implement a simple question answering application.

## Provision an Azure AI Language resource

1. Open the Azure portal at `https://portal.azure.com`, and sign in using the Microsoft account

2. Create a resource and search for Language service. Then, in the results, select Create under Language Service.

  

3. Select Create + review, then select Create. View the Keys and Endpoint page in the Resource Management section

## Create a question answering project

We use the Language Studio portal to create a question answering project. In this case, you'll create a knowledge base containing questions and answers about [Microsoft Learn](https://learn.microsoft.com/en-us/training/)

1. Open the Language Studio portal at `https://language.cognitive.azure.com/` and sign in using the Microsoft account.

  <img width="1303" height="695" alt="Screenshot 2025-11-16 at 7 49 21 AM" src="https://github.com/user-attachments/assets/f63bd9cc-a942-4049-a6ba-91640a276a6a" />

  <img width="973" height="809" alt="Screenshot 2025-11-16 at 7 50 29 AM" src="https://github.com/user-attachments/assets/ed2afb23-5741-4dd1-b359-d94ff2a4d373" />

2. Select the following settings

  <img width="790" height="804" alt="Screenshot 2025-11-16 at 7 51 54 AM" src="https://github.com/user-attachments/assets/868cce5d-8795-4de4-b18a-e3fed2bf3723" />

3. In the Create new menu, select Custom question answering.

4. Select English as the language in Choose language setting page

5. On the Enter basic information page, enter the following details.

  <img width="607" height="703" alt="Screenshot 2025-11-16 at 7 56 15 AM" src="https://github.com/user-attachments/assets/d3738385-b4f2-49e2-aec2-69cb5d9383df" />

  <img width="846" height="655" alt="Screenshot 2025-11-16 at 8 00 12 AM" src="https://github.com/user-attachments/assets/bc320772-1632-47f5-9603-9308bb9f5a4e" />


6. On the Review and finish page, select Create project.

   <img width="662" height="387" alt="Screenshot 2025-11-16 at 7 58 30 AM" src="https://github.com/user-attachments/assets/9c6e59f2-17d4-4a7d-a544-6f9862fdea97" />

   <img width="853" height="644" alt="Screenshot 2025-11-16 at 8 01 22 AM" src="https://github.com/user-attachments/assets/f47105a9-511c-4498-9ccc-ef2541cc5d2b" />

  
  
## Add sources to the knowledge base

1. On the Manage sources page for your question answering project, in the Add source list, select URLs. Then in the Add URLs dialog box, select Add url and set the following name and URL before we select Add all to add it to the knowledge base:

        Name: Learn FAQ Page
        URL: https://learn.microsoft.com/en-us/training/support/faq?pivots=general

  <img width="1194" height="566" alt="Screenshot 2025-11-16 at 8 02 54 AM" src="https://github.com/user-attachments/assets/43ca9abf-6acf-44f3-b33c-03c5dbad4d3c" />

2. On the Manage sources page for the question answering project, in the Add source list, select Chitchat. The in the Add chit chat dialog box, select Friendly and select Add chit chat.

  <img width="908" height="578" alt="Screenshot 2025-11-16 at 8 05 29 AM" src="https://github.com/user-attachments/assets/b1669eb1-db6a-4f47-a6e4-427a4a5d3e9f" />

  
## Edit the knowledge base

1. In the LearnFAQ project in Language Studio, select the Edit knowledge base

  <img width="1047" height="805" alt="Screenshot 2025-11-16 at 8 06 32 AM" src="https://github.com/user-attachments/assets/7ec6e43b-d66b-4f4d-a931-bf39800104d3" />

2. On the Question answer pairs tab, select ＋, and create a new question answer pair with the following settings:

        Source: https://learn.microsoft.com/en-us/training/support/faq?pivots=general
        Question: What are the different types of modules on Microsoft Learn?
        Answer: Microsoft Learn offers various types of training modules, including role-based learning paths, product-specific modules, and hands-on labs. Each module contains units with lessons and knowledge checks to help you learn at your own pace.

  <img width="606" height="595" alt="Screenshot 2025-11-16 at 8 07 35 AM" src="https://github.com/user-attachments/assets/937b7e3e-657d-4cec-9890-a073906da9bd" />

3. In the page for the What are the different types of modules on Microsoft Learn? question that is created, expand Alternate questions. Then add the alternate question How are training modules organized?.

4. Expand Follow-up prompts and add the following follow-up prompt:
        
        Text displayed in the prompt to the user: Learn more about training.
        Select the Create link to new pair tab, and enter this text: You can explore modules and learning paths on the [Microsoft Learn training page](https://learn.microsoft.com/training/).
        Select Show in contextual flow only. This option ensures that the answer is only ever returned in the context of a follow-up question from the original module types question.

5. Select Add prompt.

  <img width="827" height="759" alt="Screenshot 2025-11-16 at 8 10 55 AM" src="https://github.com/user-attachments/assets/c0dbfec0-6774-4b65-88cb-849467e37fe4" />

## Train and test the knowledge base

1. Save the changes to your knowledge base by selecting the Save button under the Question answer pairs tab on the left.
2. After the changes have been saved, select the Test button to open the test pane.
3. In the test pane, at the top, deselect Include short answer response (if not already unselected). Then at the bottom enter the message Hello. A suitable response should be returned.
4. In the test pane, at the bottom enter the message What is Microsoft Learn?. An appropriate response from the FAQ should be returned.
5. Enter the message Thanks! An appropriate chit-chat response should be returned.
6. Enter the message What are the different types of modules on Microsoft Learn?. The answer we created should be returned along with a follow-up prompt link.
7. Select the Learn more about training follow-up link. The follow-up answer with a link to the training page should be returned.

  <img width="1505" height="860" alt="Screenshot 2025-11-16 at 8 12 32 AM" src="https://github.com/user-attachments/assets/8dd981da-8e9a-4290-821b-aa610f23e950" />


## Deploy the knowledge base

1. In the LearnFAQ project in Language Studio, select the Deploy knowledge base page from the navigation menu on the left.
2. At the top of the page, select Deploy. Then select Deploy to confirm you want to deploy the knowledge base.

  <img width="1013" height="547" alt="Screenshot 2025-11-16 at 8 14 24 AM" src="https://github.com/user-attachments/assets/79cb9657-ceb0-4f34-a37d-802a48dd7c59" />

3. When deployment is complete, select Get prediction URL to view the REST endpoint for your knowledge base and note that the sample request includes parameters for:

        projectName: The name of your project (which should be LearnFAQ)
        deploymentName: The name of your deployment (which should be production)

## Develop an app in Cloud Shell

1. use the [>_] button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a PowerShell environment. in the Settings menu, select Go to Classic version

2. Enter the following commands to clone the GitHub repo for this exercise

        rm -r mslearn-ai-language -f
        git clone https://github.com/microsoftlearning/mslearn-ai-language

3. Navigate to the folder containing the application code files:

        cd mslearn-ai-language/Labfiles/02-qna/Python/qna-app

## Configure your application

1. Run the following command to view the code files

        ls -a -l

   <img width="1127" height="550" alt="Screenshot 2025-11-16 at 8 18 57 AM" src="https://github.com/user-attachments/assets/ad563385-4101-4624-bd45-b40ce3d9696c" />

3. Create a Python virtual environment and install the Azure AI Language Question Answering SDK package

        python -m venv labenv
        ./labenv/bin/Activate.ps1
        pip install -r requirements.txt azure-ai-language-questionanswering

4. Edit the configuration file to reflect the endpoint and an authentication key

        code .env

## Add code to user your knowledge base

1. Edit the application code file

        code qna-app.py

2. Add the following language-specific code to import the namespaces we will need to use the Question Answering SDK:

        # import namespaces
        from azure.core.credentials import AzureKeyCredential
        from azure.ai.language.questionanswering import QuestionAnsweringClient

3. Add the following code to create a question answering client

        # Create client using endpoint and key
        credential = AzureKeyCredential(ai_key)
        ai_client = QuestionAnsweringClient(endpoint=ai_endpoint, credential=credential)

4. Add the following code to repeatedly read questions from the command line, submit them to the service, and display details of the answers:

        # Submit a question and display the answer
        user_question = ''
        while True:
            user_question = input('\nQuestion:\n')
            if user_question.lower() == "quit":                
                break
            response = ai_client.get_answers(question=user_question,
                                            project_name=ai_project_name,
                                            deployment_name=ai_deployment_name)
            for candidate in response.answers:
                print(candidate.answer)
                print("Confidence: {}".format(candidate.confidence))
                print("Source: {}".format(candidate.source))
        
5. Save the changes and run the file

        python qna-app.py
   
6. Enter a question to be submitted to your question answering project; for example What is a learning path?

7. Review the answer that is returned

    <img width="1512" height="414" alt="Screenshot 2025-11-16 at 8 22 36 AM" src="https://github.com/user-attachments/assets/18ef7e74-3b19-48f0-b4b9-7721833120e8" />


## CLean up resources

1. On the resource page, select Delete and follow the instructions to delete the resource.
