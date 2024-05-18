# Lab 3: Understand HR Copilot Demo Application

   ![](../media/copilot-overview.png "Technical design")

**Smart Agent: At the heart of the solution is the Python object Smart_Agent. The agent has the following components:**

  - **Goals/Tasks:** Smart_Agent is given a persona and instructions to follow to achieve certain goals; for example, HR Copilot is about helping answer HR/Payroll questions and update employees' personal information. This is done using instructions specified in the system message.

  - **NLP interaction and tool execution:** For the ability to use multiple tools and functions to accomplish business tasks, the function calling capability of the 0613 version is utilized to intelligently select the right function (validate identity, search the knowledge base, update address, create ticket) based on the agent's judgment of what needs to be done. The agent is also able to engage with users by following the instructions and goals defined in the system message.

  - **Memory:** The agent maintains a memory of the conversation history. The memory is backed by Streamlit's session state.
  - **LLM:** The agent is linked to a 0613 GPT-4 model to power its intelligence.

    
### Task 1: Build your own HR/Payroll copilot locally

1. In the LabVM, open File Explorer, navigate to the below-mentioned path, right-click on the `secrets.env` file, and select open with  **Visual Studio Code**.

   ```
   C:\Labfiles\OpenAIWorkshop\scenarios\incubations\copilot
   ```

    ![](../media/img38.png)

1. Navigate back to the **Azure portal** and search for **AI Search (1)** in the top search box, then select **AI Search (2)** under services.

   ![](../media/hr-1.png)
   
1. From the **Azure AI Services | AI Search** pane, select **copilot-openAI-<inject key="Deployment ID" enableCopy="false"/>**.

   ![](../media/h-2.png)

1. On the Overview page, copy the **Cognitive Search Endpoint** and store it in a text file for later use.

   ![](../media/hr-3.png)

1. Click on **Keys (1)** from the left menu, copy the **Primary Admin Key (2)** and store it in a text file for later use.

   ![](../media/hr-4.png)
   
1. The Visual Studio code is opened on the desktop. Edit the below code and update the **Azure OpenAI Key**, **Embedding Model name and GPT Deployment name**, **Azure OpenAI Endpoint**, **Cognitive Search Endpoint**,and **AZURE_SEARCH_ADMIN_KEY** values that you have copied and stored in the text file earlier.

   | **Variables**                     | **Values**                                                    |
   | --------------------------------- |---------------------------------------------------------------|
   | **AZURE_OPENAI_API_KEY**          | **<inject key="OpenAIKey" enableCopy="true"/>**               |
   | **AZURE_OPENAI_ENDPOINT**         | **<inject key="OpenAIEndpoint" enableCopy="true"/>**          |
   | **AZURE_OPENAI_EMB_DEPLOYMENT**   | **<inject key="EmbeddingModel" enableCopy="true"/>**          |
   | **AZURE_OPENAI_CHAT_DEPLOYMENT**  | Replace with the name of your Open AI Chat Deployment         |
   | **AZURE_SEARCH_SERVICE_ENDPOINT** | **<inject key="SearchServiceuri" enableCopy="true"/>**        |
   | **AZURE_SEARCH_ADMIN_KEY**        | **<inject key="SearchAPIkey" enableCopy="true"/>**            |         

1. After updating values, the `secrets.env` file should be as shown in the below screenshot. Next, Click **File->Save** to save the file.

    ![](../media/img39.png)

1. In the LabVM, navigate to Desktop and search for `cmd` in the search box, then click on **Command Prompt**.

1. To run the application from the command line, run the below commands:

   > **Note**: Here, you can enter your email address below to get notifications. Otherwise, leave this field blank and click on **Enter**.

   ```
   cd C:\Labfiles\OpenAIWorkshop\scenarios\incubations\copilot\employee_support
   ```
   ```
   streamlit run hr_copilot.py
   ```

1. Once the execution of `streamlit run hr_copilot.py` is completed, a locally hosted HR Copliot application will be opened in the web browser. 

   ![](../media/img17.png)

   ![](../media/img18.png)

1. Run the following query to validate the identity of the employee:

   ```
   John 1234
   ```

   ![](../media/hr-11.png)

   >**Note**: Copilot Chat may not respond with the exact output as shown in the screenshots. Following are the examples of what you'll most likely to see in this exercise, but the response may vary.

1. Enter an example question such as `Can you explain what are deducted from my paycheck?`. The questions are answered by the Copilot by searching a knowledge base.

   ![](../media/hr-7.png)


1. Copilot can help update employee information, like address updates. For other information update requests, Copilot will log a ticket to the HR team to update the information. Enter `I moved to 123 Main St., San Jose, CA 95112, please update my address` in the HR Copilot app.

    ![](../media/L3-T1-S8.png)

1. Navigate back to **CMD** and stop the terminal by typing **ctrl + C**.

### Task 2: Integrate Azure Cognitive Search with your Application

1. In the **Azure Portal**, search and select **Storage accounts**. 

    ![](../media/img74.png)

2. From the **Storage account** page, select **copilotstorage<inject key="Deployment ID" enableCopy="false"/>**.

    ![](../media/img75.png)

3. From the left menu, select **Access keys** under **Security + networking** section. Copy the **Connection string** and store it in a text file for later use.

    ![](../media/img76.png)

4. Navigate back to the **Azure portal** and search for **AI Search (1)** in the top search box, then select **AI Search (2)** under services.

   ![](../media/hr-1.png)

5. Next, navigate to **Azure AI services**, select **AI search (1)** from the left menu, and click on **copilot-openai-<inject key="Deployment ID" enableCopy="false"/> (2)**.

   ![](../media/l1-t2-s6.png "Azure OpenAI")

6. On the **Overview (1)** page, click on **Import data (2)**.

    ![](../media/img77.png)

7.  Select **Azure Blob storage** as the **Data source**.

    ![](../media/img78.png)

8. On the **Connect to your data** tab, provide the following details and click on **Next: Add cognitive skills (Optional) (7)**.

   | Settings| value|
   |---|---|
   |Data source name| **copilotstorage<inject key="Deployment ID" enableCopy="false"/>** **(1)**|
   |Data to extract| **Content and metadata** **(2)**|
   |Parsing mode| **JSON array** **(3)**|
   |Connection string | **YOUR_STORAGE_ACCOUNT_CONNECTIONSTRING (4)**|
   |Container name| **data (5)**|
   |Blob folder| **data (6)**|

   ![](../media/img80.png)

9. On the **Add cognitive skills (optional)** tab, leave the default and click on **Skip to: Customize target index**.

10. Next, on the **Customize target index**  tab, enter the **Index name** as **payroll-hr (1)**. Set the values as provided in the below image (make sure you select `Collection(Edm.Single)` as the type for the content vector field).

      ![](../media/img81.png)

11. Next, on the **contentVector** field, click on the **Ellipsis** button in the right corner and select **Configure vector field**.

      ![](../media/img82.png)

12. On the **Configure vector field** tab, set the **Dimensions** property to `1536` **(1)** and Click on **Create** **(2)** under No vector search profiles.

      ![](../media/l3-t2-s11.png)

13. On the **Vector profile** tab, Click on **Create** under No algorithm configurations.

      ![](../media/l3-t2-s12.png)

14. On the **Vector algorithm** tab, leave the default and click on **Save**.

      ![](../media/l3-t2-s13.png)

15. On the **Vector profile** tab, select the algorithm created in the previous step and Click on **Create** under No vectorizers.

      ![](../media/l3-t2-s14.png)

16. On the **Vector algorithm** tab, leave the default and select the Azure OpenAI service as **Copilot-OpenAI-<inject key="Deployment ID" enableCopy="false"/>** and model deployment as **text-embedding-ada-002** . Click on **Save**.

      ![](../media/l3-t2-s15.png)

17. On the **Vector profile** tab, select the Vectorizers created in the previous step and Click on **Save**.

      ![](../media/l3-t2-s16.png)

18. On the **Configure vector field** tab, keep the **Dimensions** property to `1536` and **Vector profile** created in previous step and Click on **Save**. Click on **Next: Create an indexer**.

      ![](../media/l3-t2-s17.png)
    
19. Enter the **Indexer name** as **payroll-hr**, and click on **Submit**.

      ![](../media/img84.png)

20. From the **Overview (1)** page, click on **Import data (2)** again.

       ![](../media/img77.png)

21. On the **Connect to your data** tab, select the existing data source and select the storage account then, click **Next: Add cognitive skills (optional)**.

      ![](../media/img85.png)

22. On the **Add cognitive skills (optional)** tab leave the default and click on **Skip to: Customize target index**.

23. Next, on the **Customize target index**  tab, enter the **Index name** as **payroll-hr-cache (1)**. Click on **+ Add field**, and create **id, search_query, search_query_vector, gpt_response** fields with the configurations as provided in the below image (make sure you select `Collection(Edm.Single)` as the type for the search_query_vector field).

      ![](../media/img86.png)

24. In the **search_query_vector** field, click on the **Ellipsis** button in the right corner and select **Configure vector field**.

      ![](../media/img87.png)

25. On the **Configure vector field** tab, set the **Dimensions** property to `1536` **(1)** and Click on **Create** **(2)** under No vector search profiles.

      ![](../media/l3-t2-s11.png)

26. On the **Vector profile** tab, Click on **Create** under No algorithm configurations.

      ![](../media/l3-t2-s12.png)

27. On the **Vector algorithm** tab, leave the default and click on **Save**.

      ![](../media/l3-t2-s13.png)

28. On the **Vector profile** tab, select the algorithm created in the previous step and Click on **Create** under No vectorizers.

      ![](../media/l3-t2-s14.png)

29. On the **Vector algorithm** tab, leave the default and select the Azure OpenAI service as **Copilot-OpenAI-<inject key="Deployment ID" enableCopy="false"/>** and model deployment as **text-embedding-ada-002** . Click on **Save**.

      ![](../media/l3-t2-s15.png)

30. On the **Vector profile** tab, select the Vectorizers created in the previous step and Click on **Save**.

      ![](../media/l3-t2-s16.png)

31. On the **Configure vector field** tab, keep the **Dimensions** property to `1536` and **Vector profile** created in previous step and Click on **Save**. Click on **Next: Create an indexer**.

      ![](../media/l3-t2-s17.png)

32. Enter the **Indexer name** as **payroll-hr-cache**, and click on **Submit**.

      ![](../media/img89.png)

33. Navigate to the **Indexes** tab under the **Search management** section to view the newly created indexes, copy the index names, and save them in a text editor for later use.

      ![](../media/img90.png)

34. Click on **Keys** from the left menu, copy the **Primary admin key**, and store them in a text file for later use.

    ![](../media/img63.png)

35. In the LabVM, open File Explorer, navigate to the below-mentioned path, right-click on the `secrets.env` file, and select open with  **Visual Studio Code**.

    ```
    C:\Labfiles\OpenAIWorkshop\scenarios\incubations\copilot
    ```
   
    ![](../media/img38.png)

36. The Visual Studio code is opened on the desktop. Replace the following values and press **CTRL + S** to save the file.

   | **Variables**                     | **Values**                                                    |
   | --------------------------------- |---------------------------------------------------------------|
   | **USE_AZCS**                      | True                                                          |
   | **AZURE_SEARCH_INDEX_NAME**       | YOUR_SEARCH_INDEX_NAME                                        |
   | **CACHE_INDEX_NAME**              | YOUR_SEARCH_INDEX_NAME                                        |
   | **AZURE_SEARCH_SERVICE_ENDPOINT** | **<inject key="SearchServiceuri" enableCopy="true"/>**        |
   | **AZURE_SEARCH_ADMIN_KEY**        | **<inject key="SearchAPIkey" enableCopy="true"/>**            |  
     
   ![](../media/image_14.png)     
 
37. In the LabVM, navigate to Desktop and search for `cmd` in the search box, then click on **Command Prompt**.

38. Run the below commands to change the directory in CMD.

    ```
    cd C:\Labfiles\OpenAIWorkshop\scenarios\incubations\copilot\employee_support
    ```

39. Run the below commands to upgrade and install the Azure Search service python pacakages.

    ```
    pip install --upgrade azure-search-documents
    pip install azure-search-documents==11.4.0b9
    ```

40. Run the below command to run the HR Copilot application using the search service.

    > **Note**: Here, you can enter your email address below to get notifications. Otherwise, leave this field blank and click on **Enter**.

    ```bash
    streamlit run hr_copilot.py
    ```

41. Run the following query to validate the identity of the employee:
   
      ```
      Rebecca 1234
      ```

    ![](../media/hr-10.png)

42. Enter an example question such as `Can you explain what are deducted from my paycheck?`. The questions are now answered by the Copilot by searching a knowledge base. You can review this by navigating back to the command prompt and viewing the output.

    >**Note**: Copilot Chat may not respond with the exact output as shown in the screenshots. Following are the examples of what you'll most likely to see in this exercise, but the response may vary.

    ![](../media/hr-7.png)

    ![](../media/hr-9.png)

43. Navigate back to **CMD** and stop the terminal by typing **ctrl + C**.

### Task 3: Deploy the HR/Payroll Copilot application to Azure

1. In the LabVM, open File Explorer, navigate to the below-mentioned path, right-click on the `main.bicep` file, and select open with  **Visual Studio Code**.

      ```
      C:\LabFiles\OpenAIWorkshop\infra
      ```

    ![](../media/img41.png)

2. In the **appsettings** section of the `main.bicep` file, replace the values below with the ones you copied previously in the text editor. Next, press **CTRL + S** to save the file.

   | **Variables**                     | **Values**                                                    |
   | --------------------------------- |---------------------------------------------------------------|
   | **AZURE_OPENAI_API_KEY**          | **<inject key="OpenAIKey" enableCopy="true"/>**               |
   | **AZURE_OPENAI_ENDPOINT**         | **<inject key="OpenAIEndpoint" enableCopy="true"/>**          |
   | **AZURE_OPENAI_EMB_DEPLOYMENT**   | **<inject key="EmbeddingModel" enableCopy="true"/>**          |
   | **AZURE_OPENAI_CHAT_DEPLOYMENT**  | Replace with the name of your Open AI Chat Deployment         |
   | **AZURE_SEARCH_SERVICE_ENDPOINT** | **<inject key="SearchServiceuri" enableCopy="true"/>**        |
   | **AZURE_SEARCH_ADMIN_KEY**        | **<inject key="SearchAPIkey" enableCopy="true"/>**            |  

     ![](../media/hr-8.png)

3. In the LabVM, navigate to Desktop and search for `cmd` in the search box, then click on **Command Prompt**.

4. Run the below command to change the directory.

   ```bash
   cd C:\LabFiles\OpenAIWorkshop
   ```

5. Run the below command to **Authenticate with Azure**. It will redirect you to the Azure-authorized website. Next, select your account.

   ```bash
   azd auth login
   ```

6. Run the below command to set up the resource group deployment and **Create a new environment**. Make sure to replace `{DeploymentId}` with **<inject key="Deployment ID" enableCopy="true"/>** in the below command.

   ```bash
   azd config set alpha.resourceGroupDeployments on
   ```
   
   ```bash
   azd env new copilot-{DeploymentId}
   ```

7. Run the below command to provision Azure resources and deploy your project with a single command.

   ```bash
   azd up
   ```
   
8. Please select your Azure subscription to use, enter `1`, and click on the **Enter** button.

   ![](../media/img29.png)

9. Please select an Azure location to use, select the location as **<inject key="Region" enableCopy="false"/>** location, and click on the **Enter** button. You can change the location using the up and down arrows.

    ![](../media/img301.png)

10. Next, select **copilot-openai-<inject key="Deployment ID" enableCopy="False"/>** resource group and hit **ENTER**.

    ![](../media/img43.png)

11. Once the deployment succeeds, you will see the following message **SUCCESS: Your application was provisioned and deployed to Azure**. The deployment might take 5-10 minutes. It is producing a web package file, then creating the resource and publishing the package to the app service.


12. Navigate back to the Azure portal, search, and select **App service**. Select the available web app that you have deployed in the previous step.

    ![](../media/img44.png)

13. Next, click on **Browse** to open your Web application.

    ![](../media/img45.png)

    ![](../media/img46.png)

    > **Note**: If an issue occurs when you try to launch the app service, please restart the app service and wait five minutes before trying to launch the app again.

  > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
	
  - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
  - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
  - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="e563f609-c163-48c7-816f-e11985cba271" />

14. Click the **Next** button located in the bottom right corner of this lab guide to continue with the next exercise.