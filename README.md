# Prompt Engineering Workshop

Prompt engineering is a tedious process that involves a lot tasks and components.  Developments have next determine what the input or prompts are going to be and what the actions we want in return.  In order to achieve, there are a lot of parts.  For instance, the prompts are responses need to be tokenize.  Next, depending on that the action that will be the output, we need to identify where that information is coming from.  Is the information coming from an API, or an LLM model?  When data is returned, does it need preprocessing?  How is the best response identify?

![](/img/vector-token-embed.png)

That’s where Azure Prompt Flow, is valuable if providing a user-friendly logical flow to structure the different tasks involves and their dependencies.  To understand how to utilize Prompt Flow to expedite process of using an LLM that takes input and generates.  We are going to use a dental clinic’s virtual chat agent takes input from users and provides an answer.  Since using OpenAI or any other LLM model is not going to know specific information about our Contoso dental client, we are going to use data for our clinic.

![](/img/rag-pattern.png)

After the workshop, you learn how to:

-	Chat flow that takes input and produces output while keeping a dialog history.
-	Take custom data (in csv file) and convert the data into tokenized embeddings with vector indexes.
-	Use the LLM tool to create prompts and the response
-	Use the embedding tool to the trained embeddings model to search the vector index
-	Use the Python tool to create custom functions to preprocess data or call an API 
-	Use the Prompt tool to format the output response.

## Prerequisites:
-	Login or Signup for a Free Azure account

## Launch GitHub code spaces to create Azure resources
-	Create Azure OpenAI
-	Add deployment to OpenAI instance
-	Create Azure Content Safety
-	Create Azure ML workspace
-	Create Azure ML compute
-	Create Azure ML custom environment
-	Launch AzureML studio

## Enable Prompt Flow and Access Connection data
Before we can do this, we’ll need to retrieve details on the Azure OpenAI API instance provisioned in your Azure account.

Azure OpenAI
1.	Open the Azure portal, in the search box type “Azure OpenAI”, then press enter to search your resource.
2.	Click on Azure OpenAI.  You should see your OpenAI instance list on the Azure AI Services page for Azure OpenAI

![](/img/azure-open-ai.png)

3.	Click on your OpenAI instance.
4.	Under Resource Management, select the Keys and Endpoint on the left-hand side of the navigation bar
5.	Copy **Key 1** and the **Language APIs URL**.  Store both values in a clipboard for later use

![](/img/azure-open-ai.png)
 
6.	Click on Overview on the left-hand side of the navigation bar.
7.	On the Overview page, click on the Explore button
8.	Click on Deployments on the left side of the navigation
9.	Copy both the deployment name for the gpt-35-turbo model and text-embedding-ada-002

![](/img/openai-deployment.png)
	  
11.	 Close the browse tab for the Azure OpenAI Studio

*Azure Content Safety*

1.	Open the browser tab for the Azure portal, and in the search box type “Content Safety”, then press enter to search your resource.
2.	Select Content Safety.  You should see your Content Safety instance list on the Azure AI Services page for Content Safety

![](/img/content-safety.png)
 
3.	Click on your content safety instance.  Copy the name to be  used later
4.	Under Resource Management, select the Keys and Endpoint on the left-hand side of the navigation bar
5.	Copy Key 1 and the Endpoint URL.  Store both values in a clipboard for later use
 
 ![](/img/cs-key-endpt.png)

*Enable Prompt Flow* 

1.	Open [Azure ML studio](https://ml.azure.com/), and click on the bullhorn icon on the upper right corner of the page.
 
2.	Locate **Build AI solutions with Prompt Flow**, switch it to Enabled the feature.
3.	Close the features blade.

## Add Flow connections

As you work on creating Flows, it may have dependencies, services or external resources that you would need to connect to; such as OpenAI, Content Safety AI or your custom LLM models.  It enables users to add and manage connection to these resources as well as a their connection secrets.  Once a resource is connected, your Flow nodes have access to the resources metadata (e.g. name, api key, api_endpoint, or type).  In this workshop, we’ll be using the Azure OpenAI API and Azure Content Safety.

First, we’ll add the connection for Azure OpenAI API.  

1.	Open the [Azure ML studio](https://ml.azure.com/),  and select **Prompt Flow** on the left-hand side of the navigation bar
2.	Click on the **Connections** tab on the Prompt Flow page
3.	Click on the **Create** button, then select **Azure OpenAI** option in the drop-down menu
4.	Enter a **Name** 
5.	The Azure OpenAI option should be selected for **Provider**.
6.	Select your subscription under **Subscription id**.
7.	Select your OpenAI instance name under **Azure OpenAI Account Names** drop-down menu.
8.	Paste the Key 1 value for Azure OpenAI you copied earlier in the **API key** textbox.
9.	Paste the Language API URL you copied earlier in the **API base** textbox.
10.	The **API type** should be set to the default value (e.g. azure)
11.	The **API version** should be set to the default value (e.g. 2023-07-01-preview)
12.	 Click **Save**

Next, we’ll add the connection for Content Safety.   

1.	Click on the **Create** button, then select **Azure Content Safety** option in the drop-down menu
2.	Enter a **Name**
3.	The **Provider** should be set to Azure content safety
4.	Paste the Key 1 value for content safety you copied earlier in the **API key** textbox.
5.	Paste the Endpoint URL you copied earlier in the **Endpoint** textbox.
6.	The API version should be set to the default value (e.g. *2023-04-30-preview*)
7.	The **API type** should be set to the default value (e.g. *Content Safety*)
8.	Click **Save**

## Create a Runtime 

To run the Prompt Flow nodes, you need to create a Runtime.  Runtime serve as the compute with a docker environment for executing the flows.  The Compute instance are the VMs and the environment are create from a docker image that contains the Python packages and dependencies need to run the Flow.  When creating a runtime, you have the option of choosing a default curated environment; or you can create your own custom environment.

To create runtime environment, complete the following steps:

1.	Click on the **Runtime** tab; than click **Create** button
2.	Enter a **Runtime name**
3.	Select on existing compute under the **Select Azure ML compute instance** drop-down menu.  If there’s no existing compute instance, click on **Create Azure ML compute instance**

4.	Under **Custom Application**, select the **New** option

5.	Under **Environment**, select **Use customized environment**.

6.	Under **Environment type**, select “xyz”

7.	Click on **Create**

## Exercise 1: Bring your own data

Open AI and most LLM models are training from various publicly available data.  However, there are instances where we need to use our own data and narrow the actions and data search of our LLM prompts to focus only on the scope of our data or expand the data from LLM model to include our data as well.  To use your own data in a LLM, you need to convert you data into numeric values.  Each word mapping to a specific number (token).  Then you train a model to find similarities, collations, or word association, the model creates vector indexes to the word associations.   The good thing is the Prompt Flow service provide an easy-to-use process your to upload dataset and it generates model and the Vector indexes.

To upload custom data for this lab, you need to use the Contoso Dentist clinic data located in *data/contoso_dental.xls*.

1.	Click on the **Vector index** tab.  
2.	Click on the **Create** button.
3.	Enter an index name in the **New vector index name**
4.	Select **Local folders** under the **Data source type** drop-down menu
5.	Click on the + icon for **Click to Update Folders**.  Then navigate to **/data** folder in the local directory. 
6.	Select the **data** folder.  Confirm that the blade displays the dental_office.xls file
7.	Select **Faiss Index** for the **Vector store**.
8.	Click **Next**
9.	Select your OpenAI connection name from the **choose connection** drop-down menu
10.	Click **Next**
11.	Select **Compute instance** option under the **Select compute type** drop-down menu
12.	Select the name of your runtime under the **Select Azure ML compute instance**
13.	Click **Next**
14.	Review the selections and click on **Create**
15.	Use the **Status** section of the page, to monitor the Vectior index creation job status.  
 
![](/img/vector-index-pipeline.png)

16.	Wait till all the job nodes complete successfully.

![](/img/vector-index-output.png)
 
17.	Azure Machine Learning automatically creates an example Flow for you.

## Exercise 2:  Create Chat Flow

We will learn how to create a basic chat agent that interacts with prompts power by an OpenAI model.

1.	In Azure ML studio, click on Prompt Flow.  
2.	On the Prompt flow page, select the **Flow** tag.   Then click on **Create** button.
This displays a gallery of different types of flows and evaluation templates you can clone.  
3.	In this lab, under **Chat Flow**, click on the Create button.
4.	Enter a **Folder name** on the Create a new file blade.
5.	Press **Create**.

*Input Node*

On flow page, Prompt generates the Input fields need for the chat input node.  The right side of the page displays a pipeline containing action nodes with logic needed to build the flow.

Add LLM model for the Chat prompt

1.	Under the **Connection** drop-down menu, select the name for OpenAI connection created earlier.
2.	Make sure Chat is select for **Api**
3.	Select **deployment_name**
4.	Skip the **Advanced** and **Function** calling section.
5.	The **Prompt** section is already prepopulated for you:
* System: Contains a rule for the agent.  You can enter instructions on how to handle use inquiries.
* Since the Chat flow keeps the context and dialogue of the conversation, the prompt loops through the chat history to display what the assistant and user entered.
* At the end, the prompt creates a prompt for the user to enter a question.

*Output Node*

If you scroll back to the Output section, you’ll see that the answer is linked to the Chat nodes output.

Lab1: Run the Chat

1.	To test the Chat flow, click on the **Chat** icon

![](/img/new-chat.png)
 
2.	Enter “what is prompt engineering” in the text box and click submit icon.

![](/img/what-is-prompt.png)

3.	Next, enter “My molar tooth is aching so bad.  What could be the cause?”
4.	Finally, enter “What is the address of your dental clinic?”

![](/img/dental-address.png)
 
As you can see the Chat is not able to answer specific questions about a business or dental clinic.   This makes some of the answers not reliable or available.  In the next exercise, you learn how to bring your custom data into the chat to provide response that are relevant to your data.

## Exercise 3: Create Chat agent to use custom data.
In the precise exercise you create a vector index and train to search for your vector embeddings.  In the exercise, you’ll be expanding the Chat pipeline logic to take the user question and convert to numeric embeddings.  Then we’ll use the numeric embedding to search the numeric vector.  Next, we’ll use the prompt to set rules with restrictions and how to display the data to the user.

We'll be using the following tools:
-	**Embedding**: converts text to number tokens.  Store to token in vector arrays based on then relation to each other.
-	**Vector index lookup**: Takes user input question and queries the vector index with the closest answers to the question.
-	**Prompt**: enters user to add rules on the response show be sent to user
-	**LLM**: provides the LLM prompt or LLM model response to user
 
1.	Open the Flow page, by clicking **Prompt Flow**.
2.	Click you the Chat flow you created in Exercise 1.
3.	On the Flow toolbar, click on **More tools**.  Then select the **Embedding tool**.

![](/img/flow-tools.png)
 
4.	Enter **Name** for the node (e.g. embed_question).
5.	Click the **Add** button.
6.	Select the **AzureOpenAIconnection** name you created earlier.
7.	Select **Text-embedding-ada-002** deployment name you created earlier
8.	For **Input**, select *${inputs.question}*.  This should create a node under the input node.

![](/img/search-vector.png)
 
9.	Click the Save button
10.	On the Flow toolbar, click on **More tools**.  Then select the Vector Index Lookup tool.  This will generate a new **Vector Index Lookup** section at the bottom of the flow.
11.	Enter **Name** for the node (e.g. search_vector_index).
12.	Click the **Add** button
13.	For **Path**, copy and paste the Datastore URI you retrieve earlier for the vector index.
14.	Select the embedding output as the **query** field (e.g. *${embed_question.output}*).
15.	Leave default value for **top_k**.

![](/img/search-vector.png)
 
16.	Click the **Save** button
17.	On the Flow toolbar, click on **Prompt** tool. This will generate a new Prompt section at the bottom of the flow.
18.	Enter a **Name** for the node (e.g. generate_prompt).  Then click the **Add** button.
19.	Copy the following text in the Prompt textbox:
```
system:
You are an AI system designed to answer questions. When presented with a scenario, you must reply with accuracy to inquirers' inquiries.  If there is ever a situation where you are unsure of the potential answers, simply respond with "I don't know.  

context: {{contexts}}

{% for item in chat_history %}
user:
{{item.inputs.question}}
assistant:
{{item.outputs.answer}}
{% endfor %}

user:
{{question}}
```
20.	Click the **Validate and parse input** button to generate the input field for the prompt.
21.	Select the ${Search_Vector_Index.output} for **chat_history**
22.	For **contexts**, select ${inputs.chat_history}.
23.	Select ${inputs.question} for the **question** field.
 
![](/img/output_prompt.png)

24.	Click on the **Save** button
25.	Click on the **chat** node and drag it below the **generate_prompt** node

![](/img/chat-node.png)
 
26.	Under the **Flow** pane, scroll up to the **chat** section
 
![](/img/chat-node-input.png)

27.	Copy and paste the following text in the Prompt textbox.  This specifies the output to display to the user:
```
{{prompt_response}}
```
28.	Click on the Validate and parse input button to regenerate a new input field. Prompt Flow will generate the text metadata you specified in the Prompt textbox.
29.	In the Prompt_response value, select ${generate_prompt.output}.
30.	Under the Flow pane, scroll up to the output section.
31.	Replace the answer to ${chat.output}
32.	Click on the Save button

## Exercise 4: Test Chat with your own data

Now that you have updated the prompt flow logic to you use your own data and process the output, let’s see if the Chat will generate more relevant information pertaining to our Contoso dental data.  First let clear the chat history, so the question is not base not the prior responses from our OpenAI model.

1.	Click on the edit icon for the chat history field on the Inputs flow section.  This will open an Edit text window.
 
2.	Select all the text with the open and close brackets “[ ]”
3.	Click on the Chat button to test the new flow
4.	Enter the following question:

what is your dental clinic address?

5.	You should get the following response:

![](/img/dental-clinic-address.png)
 
6.	Next, enter the following question:

What is the clinic's phone number?

7.	You should get the following response:

![](/img/dental-clinic-phone.png)
 
8.	Finally, enter the following what questions:

My tooth is aching really bad.  What could be the cause?

9.	You should get the following response:
 
![](/img/toothache.png)

## Exercise 5:  Handle Groundedness & Hallucinations

Always an LLM model may be eager to provide the user with a response.  It’s important to make sure that the model is not providing response to questions that are out of scope with subject domain of your data.  Another issue is the response may provide information that is not factual and, in some cases, even provide reference to the answer that appears legitimate.  This is a risk, because the information provided to the user can have negative or harmful consequences.

Grounding test
1.	On generate_prompt section of the Flow pane, click on Generate variants button. 
2.	Select Connection for your Azure OpenAI 
3.	Next, select the Deployment name.
 
4.	Click Submit button.  These will generate a Variant_1 prompt section.
5.	In the prompt textbox, copy and paste the following text:

```
System: 
As an AI Assistant Prompt Engineer, I need you to generate a response to the user's question.  Please cite your sources.

context: {{contexts}}

{% for item in chat_history %}
user:
{{item.inputs.question}}
assistant:
{{item.outputs.answer}}
{% endfor %}

user:
{{question}}
```
6.	Click the Save button
7.	Now, enter the following question:
Which supplements are good for teeth?
8.	As you can see, our chat produces a response that is factual button not in our Contoso dental data.
 

## Exercise 6:  Evaluate your Flow
You can unit test your Flow.  However, Prompt flow provides a gallery of sample evaluation flows your can use to test you Flow in bulk.  For example, classification accuracy, QnA Groundedness, QnA Relevant, QnA Similarity, QnA F1 Score etc.  This enables you to test how well your LLM is performing.  In addition, you have the ability to examine which of your variant prompts are performing better.   In this example, we’ll use the QnA Groundedness evaluation template to test our flow.

1.	Click on the **Evaluate** on the top right-side of the screen.

![](/img/evaluate.png)
 
2.	On the Basic settings page, select the **Use default variant for all nodes** radio button.
3.	Click the **Next** button
4.	On the Batch run settings page, click on **Add new data** link for the **Data** field.
5.	Enter **Name** on the Add new data pane (e.g. Contoso-Dental)
6.	**Browse** to the workshop repo directory and select the **contoso-dental.csv** file.   
7.	Click on the **Add** button.   A preview of the top 5 rows of the data should be displayed at the bottom of the page.
8.	Under Input mapping, enter the open and close brackets **[]** for the value of **chat_history**.
9.	Click in the Value textbox for the **question** field and enter ${data.question}.

![](/img/evaluate-input-flow.png)
 
10.	Click the **Next** button.
11.	On the **Select evaluation** page, select the checkbox for the **QnA Groundedness Evaluation**.

![](/img/evaluation-gallery.png)
 
12.	Click the **Next** button.
13.	Click on the right arrow **“>”** to expand the **QnA Groundedness Evaluation** settings.

![](/img/evaluate-qna-fields.png)
 
14.	Click on the Data Source textbox and enter ${data.question} for the question field. 
15.	Enter ${run.inputs.contexts} for the context field.
16.	Enter ${run.outputs.answer} for the answer field.
17.	On the right-hand side of the page, scroll down to the bottom of the page.
18.	Select your AzureOpenAI connection name for the Connection.
19.	 For Deployment name / Model, select your AzureOpenAI deployment name.
 
 ![](/img/evaluate-connection.png)

20.	Click the Next button. 
21.	Finally, click on the Submit button.
22.	Click on View run list to monitor the run progress.

![](/img/start-evaluate.png)
 
23.	Click the Refresh button to update the run status. The run should take ~15 minutes.
24.	Click on the Display name of the run to view the run results.
25.	Click on View outputs.  Then select the run name from the Append related results option.
26.	The results will include a column for gpt_groundedness score.

 ![](/img/evaluate-results.png)

27.	The score will range from 1 to 5, where 1 is the worst and 5 is the best performance.








