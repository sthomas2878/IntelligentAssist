# Use case: Intelligent AI Assistant

## Table of Contents

- [Use case: Intelligent AI Assistant](#use-case-intelligent-ai-assistant)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
    - [Pre-requisites](#pre-requisites)
  - [watsonx.ai](#watsonxai)
    - [The watsonx.ai console](#the-watsonxai-console)
    - [The Weather Agent](#the-weather-agent)
    - [The Surplus Agent](#the-surplus-agent)
  - [watsonx Orchestrate](#watsonx-orchestrate)
    - [The watsonx Orchestrate console](#the-watsonx-orchestrate-console)
    - [AI Agent Configuration](#ai-agent-configuration)
    - [The Secretary Agent](#the-secretary-agent)
    - [The Traffic Agent](#the-traffic-agent)
    - [The Warehouse Manager Agent](#the-warehouse-manager-agent)
  - [Summary](#summary)

## Introduction
This use case describes a scenario where a user leverages an AI agent via chat / natural language interface, to help with the execution of tasks that require the selection of the right agent to satisfy each request.
Agents can be configured in the system to address specific needs of the organization. Based on the descriptions given, these agents will be selected if they satisfy the task at hand.
Each agent, in turn, is connected with a Large Language Model (LLM) that supports function calling, so that it can leverage one or more tools, again based on each tool's description.

In our scenario, we will build no-code, low-code, and pro-code agents for Weather information, Surplus Handling, Secretary functions and finally Traffic information, all connected to a "routing agent" that will take requests from the end user and then select the appropriate agent for excecution before returning the answer. The flow of interacion we are simulating is one where a Warehouse Manager is requesting recommendations on the handling of any surplus product, notifies the right set of stakeholders and also keeps an eye on the weather and traffic around the warehouse.

There is an argument to be made that a truly agentic solution would show a high degree of autonomy. To address a particular problem, or ask, an agentic solution will make a plan, execute this plan, check its effectiveness towards a good outcome, and possibly revise the plan as needed, all in an automated fashion and without human intervention. Applying that to the flow described above, we can simply take out the "human in the loop", by letting the system decide what to do with surplus product, notify stakeholders of the decision (say, return surplus to original warehouse), and update systems of record. At the same time, we can have a "human in the loop", whereby the Warehouse Manager invokes every step along the way, checking the response from the respective agent to ensure the right progression.

<div style="border: 2px solid black; padding: 10px;">
Even though we will take you through a complete and working example, you should also consider making changes that fit your desired use case, and only take this description as a reference point that guides you along your own implementation.
</div>

### Pre-requisites

- Check with your instructor to make sure **all systems** are up and running before you continue.
- If you're an instructor running this lab, check the **Instructor's guides** to set up all environments and systems.

## watsonx.ai

In this lab, we will build and deploy 2 agents using the Agent Builder within watsonx.ai.

To get to the watsonx.ai console, go to the [Resources list on the IBM Cloud homepage](https://cloud.ibm.com/resources).

![alt text](images/image33.png)

Expand the `AI / Machine Learning` section and select the resource that has `watsonx.ai Studio` in the Product column, as shown above. Next, click on the downward facing arrow next to the `Launch in` button. Then, select the `IBM watsonx` option.

![alt text](images/image34.png)

### The watsonx.ai console

> When opening the console for the very first time, you may be greeted by some pop-up windows offering a tour. Click on `Close` for both windows.

![alt text](images/image35.png)
![alt text](images/image36.png)

The first step to creating any assets within watsonx.ai is to create a Project. A Project is a collaborative workspace where you work with data and other assets to accomplish a particular goal. 

To create a project, scroll down to the section named `Projects`. Then, click on the plus button located at the top right of this section.

![alt text](images/image37.png)

A new window should pop up titled `Create a project`. We will create a new project with the following parameters:
- Name: Intelligent Assistant Project
- Storage: This should be prefilled with a storage. If not, please select one of the options

Once all of the information is filled in, click `Create`.

![alt text](images/image38.png)

Now we see the homeview of the new project we have created. First, we will need to associate a watsonx.ai Runtime service in order to create and run any agents. To do so, first click on the `Manage` tab at the top. After, click on the `Services & integrations` tab on the left side.

![alt text](images/image39.png)

Next, click on the blue `Associate service` tab. A new window will pop up, and you should see a single item with `Type` being watsonx.ai Runtime. Click on the checkbox on the left next to the name of the service. Afterwards, click on the blue `Associate` button at the bottom right of the screen. You have now properly set up your project to create your first agents!

![alt text](images/image40.png)

## The Weather Agent

We will first create a Weather Agent to give us live weather information about any city. This agent will use a pre-built tool within the watsonx.ai Agent Builder that calls external services.

First, click on the `Assets` tab within your project. You will see a blue `New asset` button on the top right of the screen. Click on this button to open a new screen.

![alt text](images/image41.png)

You will now be presented with a variety of different asset types you can create within watsonx.ai. Today, we will select `Build an AI agent to automate tasks` under the `Work with models` section of this page.

![alt text](images/image42.png)

A new screen will now pop up. This is the Agent Builder in watsonx.ai. This builder allows for you to build fully-customizable no-code and pro-code agents within watsonx.ai. For our Weather Agent, we will need to configure some things in this screen before testing.

First, click on the `Setup` drop down menu at the top of the screen. Here, you will give the agent the following details:
- Name: Weather Agent
- Description: 
```
Retrieves weather information for any city
```

![alt text](images/image43.png)

Next, go to the `Configuration` drop down. Note that the `Instructions` field is very important as it defines the purpose of an agent. The instructions can include using a specific language, date or time format, user greeting, or an external tool as an information source instead of a foundation model's knowledge base.
- Framework: LangGraph
- Architecture: ReAct
- Instructions: 
```
You are an agent that retrieves weather information for any city
```

![alt text](images/image44.png)

Agents can rely on `Knowledge` and/or a `Toolset` that consists of one or more `Tools`, and we can define all of those elements here. 
- `Knowledge` represents information that is stored in the form of "embeddings" in a so-called Vector Store. Whenever the agent answers a request, it can choose to run a search against the connected Knowledge repository (i.e. the Vector Store) to search for information that can assist in answering the request. You can either upload documents directly here, or connect the agent to an already existing repository. Note that once again, the "Description" field is key, because it will help the agent decide whether to run a search against the knowledge.
- `Tools` are functions an agent can call. It can be either an API call, an invocation of custom code, or another agent. This allows to extend the agent's capabilitiy beyond what the LLM has been trained with.

We will skip the `Knowledge` section for now.

Then in the `Tools` drop down, you should see a `Google search` tool prepopulated as an Added tool. Delete this tool by clicking on the Trash Can icon on the right side of the tool.

![alt text](images/image45.png)

Next, click on the `Add a tool` button. A new screen should pop up allowing you to Select a tool. Here, we want to click on the slider for the `Weather` tool. Then, close the window.

![alt text](images/image46.png)

You have now created your first agent in watsonx.ai! We can now test our new agent in the `Agent preview` section on the right side of the screen. Enter a question about the weather in a city. For example, "What is the weather in New York City?". Furthermore, the agent will remember previous parts of the conversation, so you can ask for some slightly more advanced queries as well. For example, you may now ask, "Can you give that to me in the fahrenheit?". Additionally, it is possible to see the reasoning the agent uses to get the answer. For example, during the first query asking for the weather in New York City, the agent first calls the Weather tool with New York City as the input. Then, upon receiving the response from the tool, will generate a natural language answer that includes the output from the Weather tool.

![alt text](images/image47.png)

Next, we must save the agent. In the top right corner, click on the save icon. Then, click `Save as`.

![alt text](images/image48.png)

You are now presented with a new screen. We have 3 different options of ways we can save our agent: `Agent`, `Standard notebook`, and `Deployment notebook`. These 3 different options give users flexibility during the development process of how they would like to work with their newly created agents. For this lab, we will save our work as an `Agent`. The details should be prefilled. Then, uncheck the `View in project after saving` and click on `Save` at the bottom right corner of the screen.

![alt text](images/image49.png)

Next, we must deploy our agent so that we can access it outside of our watsonx.ai Project. To do so, click on the `Deploy` button next to the `Save` icon. A new screen will pop up. Here, you will see options to create a userAPI key and New Deployment Space. First, create a user API key by clicking the clue Create button at the top right.

![alt text](images/image72.png)

This will open a new tab. In this tab, click on the blue `Create a key` button on the right. 

![alt text](images/image73.png)

This will create a new key. Now, you can close this tab and navigate back to the `Deploy as an AI Service` tab. Here, click on the `Reload` button at the top right.

![alt text](images/image74.png)

Now, you can create your New Deployment Space by clicking on the `New deployment space` button.

![alt text](images/image50.png)

![alt text](images/image51.png)

A new tab will open to set up your deployment space. A deployment space is a place where you can deploy assets and manage those deployments. Fill in the following information:
- Name: AgentDeploymentSpace
- Deployment Stage: Development
- Storage: Keep the prefilled option
- Watson Machine Learning: Keep the prefilled option

Then, click on `Create`. You have now created your deployment space

![alt text](images/image52b.png)

Once you see the confirmation that "The space is ready", you may return to the previous tab to deploy your Weather Agent. Now, click the `Reload` button and select your newly-created AgentDeploymentSpace under the `Target deployment space` dropdown. Uncheck the `View in space after deploying` box, and click the blue `Deploy` button at the bottom right corner. Your deployment has now started. This will take a few minutes to complete.

![alt text](images/image53.png)

## The Surplus Agent

This agent analyzes options for the handling of surplus product, and makes recommendations for how to handle it. Surplus product can be routed to a variety of places, all of which incur different cost. Just like in the previous example, a real system would be connected to a separate application that handls this analysis, and here we are just going to simulate it. We will use a small Python code chunk to simulate the low-code options available to connect this agent to other applications.

First, navigate back to our project by clicking on the Project name, `Intelligent Assistant Project` at the top of the screen.

![alt text](images/image54.png)

Once again, click on `New Asset` and then `Build an AI agent to automate tasks`. In the `Setup` section, enter:
- Name: Surplus Agent
- Description:
```
Provides recommendations about the handling of surplus data 
```

![alt text](images/image55.png)

In the `Configuration` drop down, set the following values:
- Framework: LangGraph
- Architecture: ReAct
- Instructions: 
```
Persona: 
- Your purpose is to provide information about the surplus data. 
Context: 
- Use the Surplus data to create answers 
```

![alt text](images/image56.png)

In the `Tools` section, get rid of the `Google search` tool. Then click on `Create custom tool`. 

![alt text](images/image57.png)

When you click on this page, you will see a message that you must `Deploy Python engine`. Accept this message to deploy the engine. You will now see the option to select a `Deployment space`. Please select the `AgentDeploymentSpace` that you created earlier in the dropdown menu.

![alt text](images/image58.png)
![alt text](images/image59.png)

Once finished, you will notice a `Create custom tool` screen with `Name`, `Tool description`, `Input JSON Schema`, and `Python code` sections. This screen allows you to define a custom tool with specific inputs and outputs using Python code which your agent may utilize. For this example, we will simulate the agent grabbing information from a database by having the agent call a Python function that returns a dataframe. Then, the agent will be able to synthesize and provide answers to our questions based on the information in that dataframe.

In the custom tool, fill in the following information:
- Name: Surplus Tool
- Tool Description:
```
This tool fetches all of the relevant data that is necessary to answer queries related to surplus
```
- Input JSON Schema: 
```
{}
```
- Python code:
```
import numpy as np
import pandas as pd

def get_data():
    surplus_data = pd.DataFrame(
    {"truck_id":["T004","T001"],
    "SKU":["102209199","199464599"],
    "total_surplus":[15,50],
    "destination_1":["Marketing","Holding"],
    "units_1":[12,15],
    "destination_2":["Relocation","Marketing"],
    "units_2":[3,19],
    "destination_3":[np.nan,"Relocation"],
    "units_3":[np.nan,12],
    "destination_4":[np.nan,"Dropship"],
    "units_4":[np.nan,4],
    "total_cost":[69,684]}
    )

    surplus_json = surplus_data.to_json(orient='index')

    return surplus_json
```

Then, click on `Create`.

![alt text](images/image60.png)

You have now created your first custom tool in watsonx.ai! We can now test our new agent in the `Agent preview` section on the right side of the screen. Use the following queries as examples:
* Give me the surplus data
* Return the surplus data for SKU 199464599 in a table
* What is the total surplus and where is it going for that SKU? 

> When doing this in your own instance, you may see answers that differ from the ones shown in the screenshot above. Moreover, the agent will often ask follow-up questions before offering an answer. This is due to the undeterministic nature of the AI models involved. Feel free to experiment with different types of questions to see how the agent reacts. The same equally applies to all the agents described further down.

![alt text](images/image61.png)
![alt text](images/image62.png)

Once again, we will save the agent using the `Save as` button. Please the save it as an `Agent`.

![alt text](images/image63.png)

Then, `Deploy` the agent in the `AgentDeploymentSpace`. This time, ensure that you check the box that states "View in space after deploying".

![alt text](images/image64.png)

Now, you should be in the `Deployment` page of your `AgentDeploymentSpace`. You will see three different deployments: (1) Python Interpreter Service, (2) Surplus Agent, (3) Weather Agent. 

![alt text](images/image65.png)

You will need to grab your credentials from the Surplus Agent and Weather Agent you have created to bring your agents into watsonx Orchestrate. To do so, first click on the Surplus Agent. Then, you will see 4 endpoints. You will want to save the streaming endpoint in a text editor.

![alt text](images/image66.png)

Repeat the same process, grabbing the streaming URL from the Weather Agent.

You have now completed the first part of the lab. You have learned how to navigate the watsonx.ai environment and develop both no-code and low-code agents to solve different types of tasks.

## watsonx Orchestrate

As shown in the [Solution Architecture](./images/UpdatedIAArchitecture.png), we will build and deploy the majority of the agents for the solution in watsonx Orchestrate.

To get to the watsonx Orchestrate console, go to the [Resources list on the IBM Cloud homepage](https://cloud.ibm.com/resources).

![alt text](images/image52.png)

Expand the `AI / Machine Learning` section and select the resource that has `watsonx Orchestrate` in the Product column, as shown above. Next, click on the `Launch watsonx Orchestrate` button.

![alt text](images/image1.png)

This opens the watsonx Orchestrate console.

![alt text](images/image2.png)

### The watsonx Orchestrate console

> When opening the console for the very first time, you may be greeted by a pop-up window offering that you create your first agent. Click on `Skip for now`.

![alt text](images/image31.png)

In the console, it shows that no agents have been deployed yet. Thus, if you interact with watsonx Orchestrate at this point, not much will happen, since the system has no agents available to route any request to.

However, you can already interact with the Large Language Model (LLM) that works behind the scenes, and ask general questions, like "How are you today?" or "What is the capital of France?". 

![alt text](images/image3.png)

Go ahead and chat with watsonx Orchestrate to explore what type of answers it gives to your questions.

### AI Agent Configuration
We are now ready to build the first agent. In the watsonx Orchestrate console, click on either `Create or Deploy` or `Create new agent` (either will goet you to he same place).

![alt text](images/image4.png)

### The Secretary Agent

Now, we want an agent that handles communication with stakeholder, including sending out notifications about surplus handling. The process will be similar as with the previous two agents, and we will provide output examples in the `Instructions` field.

Click on Create agent and enter the following:
- Name: Secretary Agent
- Description: 
```
The Secretary Agent specializes in creating emails related to warehouse topics. 
```

Then click on Create and add the following text to the `Instructions` field in the Behavior section:
```
Persona:
- Your persona is that of a secretary that drafts emails. I will ask you to create an email about a topic, and you will return a textual draft of that email.

Context:
- Write a concise and professional draft email about the surplus in the inventory.   The email should directly begin with the subject line, followed by the email body without any introductory statements or preambles. 
- Use your knowledge of email writing as a guide to structure and tone, but do not limit yourself to specific teams or predefined examples.  
- Assume the audience and content are general unless specified otherwise.  
- Avoid mentioning any knowledge limitations or referencing specific teams unless explicitly required. 
- Below are examples of user prompts and the resulting generated email as guidance for your own generations.  

Examples:
Example1:
Input: 
Generate a notification email for the marketing team for item 223456789 for 25 units
Output:
Subject: Notification of Surplus Units for SKU# 223456789 

Marketing Team,
This email is to inform you that there are 25 surplus units of item 223456789 available. Please review and coordinate any necessary marketing efforts for these additional units.

Warehouse Management

Example2:
Input:  Generate a notification email for the holding team for item 112334343 for  10 units
Output:

Subject: Notification of Surplus Units for SKU#112334343

Holding Team,
This email is to notify you that there are 10 units of item 112334343 in surplus which need to be stored in the inventory. Please take necessary actions.

Warehouse Management

Example3:
Input:  Generate a notification email for the dropship team for SKU: 88245464599 of 10 units
Output:
Subject: Notification of Surplus Units for SKU#88245464599

Dropship Team,
This email is to notify you that there are 10 units of item 88245464599 in surplus. Please review and adjust shipping schedules as needed to accommodate these additional units.

Warehouse Management


Example 4:
Input:   Generate a notification email for the relocation team for SKU: 765004599 of 9 units
Output: 
Subject: Notification of Surplus Units for SKU#765004599

Relocation Team,
This email is to notify you that there are 9 units of item 765004599 in surplus. Please review and coordinate any necessary relocation efforts for these additional units.

Warehouse Management
```

Uncheck `Show agent`. Then test the new agent with the Preview, for example, enter "Generate a notification email for the marketing team for SKU: 8932464599 of 10 units". The result should look like in the picture below.

![alt text](images/image15.png)

Make sure you click the `Deploy` button again and return to the agent overview window by clicking the `Manage agents` link at the top left of the window.

### The Traffic Agent

Almost there! For this agent, we need to retrieve up-to-date traffic information about a given location. And for this, we will delegate the work to an agent that was developed using Python code and the LangGraph framework, deployed in watsonx.ai. The creation of this agent is not subject of this lab, but will be covered in a separate lab in the future. 
Here, you will be given the details for the agent by your instructor, who has pre-deployed the agent for you. So there is nothing else to do here for now.

### The Warehouse Manager Agent

We are finally ready to create the agent that acts as the orchestrator for all other agents and is the agent the end user interacts with. 

Click on `Create agent` once more. 

![alt text](images/image16.png)

Like the other agents you created already, this one will be created from scratch. The name is "Warehouse Manager Agent". The description differs from the previous agents, indicating that this one is an 'orchestrating', or 'supervising', or 'routing' agent: 
```
The Warehouse Manager Agent is in charge of routing user requests to the most relevant agent working under it.
``` 

After you have entered the information, click on `Create`.

![alt text](images/image17.png)

We had mentioned above that an agent can collaborate with other agents to fulfill a certain task. You enter those collaborator agents in the `Agents` section under `Toolset` in the agent definition window.

![alt text](images/image18.png)

Click on the `Add agent` button. Since we want to add the agents you created above as collaborators to this agent, select the `Add from local instance` option.

![alt text](images/image19.png)

Here you see the Secretary Agent listed that you have created. We want the Warehouse Manager Agent to use it, so check it and click on `Add to agent`. Note that it is possible that you will see more than the three agents covered in this lab (you may have created agents from a different lab, or created some of your own), so make sure you are selecting the right agent.

![alt text](images/image67.png)

But we are not done yet. Click on `Add agent` again. This time, select the `Import` option. This will allow us to import our three agents created in watsonx.ai.

![alt text](images/image21.png)

On the next screen, select the `External agent` option and click on `Next`.

![alt text](images/image22.png)

On the following screen, enter the details about the Traffic Agent:
- Agent details
  - Provider: `watsonx.ai`
  - Authentication type: leave as `API key`
  - API key: enter the key provided to you by your instructor
  - Service instance URL: enter the value provided to you by your instructor
- Define new agent
  - Display name: `TrafficAgent` (the name cannot contain a space)
  - Description of agent capabilities: `The TrafficAgent agent provides information about traffic in any given location.`

![alt text](images/image23.png)

Now click on `Import agent`. You should now see the `TrafficAgent` listed in the Toolset section of the Warehouse Manager Agent.

Repeat the same process, now entering details about the Weather Agent. Make sure you are using the Weather Agent credentials:
- Agent details
  - Provider: `watsonx.ai`
  - Authentication type: leave as `API key`
  - API key: enter the key provided to you by your instructor
  - Service instance URL: enter the URL value you saved in the previous steps
- Define new agent
  - Display name: `WeatherAgent` (the name cannot contain a space)
  - Description of agent capabilities: `The WeatherAgent agent retrieves weather information for any city.`

![alt text](images/image68.png)

Repeat the process one more time, but for our Surplus Agent. Make sure you are using the Surplus Agent credentials:
- Agent details
  - Provider: `watsonx.ai`
  - Authentication type: leave as `API key`
  - API key: enter the key provided to you by your instructor
  - Service instance URL: enter the URL value you saved in the previous steps
- Define new agent
  - Display name: `SurplusAgent` (the name cannot contain a space)
  - Description of agent capabilities: `This SurplusAgent agent fetches all of the relevant data that is necessary to answer queries related to surplus.`

![alt text](images/image69.png)

Now click on `Import agent`. You should now see all four agents listed in the Toolset section of the Warehouse Manager Agent.

![alt text](images/image70.png)

Finally, we give this agent instructions about how to use the collaborator agents we defined earlier. Enter the folllowing text in the `Instructions` field under Behavior.
```
Reasoning: 
- Use the SurplusAgent for tasks related to surplus. 
- Use the SecretaryAgent for drafting of emails. 
- Use the TrafficAgent to find traffic information about a location. 
- User the WeatherAgent to get weather information about a location. 
```

Before we test this agent, scroll all the to the bottom and make sure the `Show agent` checkbox is actually checked! This is the agent we want to use in the main chat window and make it available to end users.

![alt text](images/image71.png)

Let's test it. Since we haven't touched it before, let's start with the externale TrafficAgent agent. We can trigger it by asking about traffic in a given location, say, Sydney Australia. Enter the following into the Preview text input: "Please tell me about traffic around central Sydney, Australia." (You can obviously pick any other location, too.)

![alt text](images/image26.png)

Note that the agent was "reasoning", in other words, determining how to answer this request. It decided that routing the request to the TrafficAgent was the best option. You can expand the `Show reasoning` section in the Preview and see which steps the agent took. It should list one step, which you can expand as well.

![alt text](images/image27.png)

Note how it selected the Traffic agent to answer the request, which was obviously the right choice in this case.


## NOTE: This is a pre-GA version of watsonx Orchestrate

At the current moment, there are some known issues regarding the orchestration agents within watsonx Orchestrate. A major update to fix these issues will go live on June 30, 2025. At the current moment, you can ask the Warehouse Manager Agent to do a single task requiring one agent. The Warehouse Manager Agent should properly route the task.

However, in order to receive a proper answer for a task requiring a different agent, the user must refresh the session (either reload the page or press the circle arrow at the top left of the preview). 

Additionally, multi-agent queries are not working at the moment. The instructions/screenshots below follow an earlier version of this lab where the Warehouse Manager Agent is able to properly orchestrate multiple agents in one query.

### Older lab showing multi-agent orchestration

You can now test the routing to the other agents, possibly using the same or similar prompts as what you used earlier to test them individually:
- "What is the status of the warehouse docks?"
- "How do we handle surplus on truck T001?"
- "Please create a notification email ..."
  
![alt text](images/image28.png)

Once you are satisfied with the answer, deploy this agent using the `Deploy` button as with the other agents before. Since we left the `Show agent` option checked, we can now go back to the home screen to see it there. Simply click on `watsonx Orchestrate` at the very top of the window.

![alt text](images/image29.png)

Now you can enter your questions and requests in the main chat window. Note how the Warehouse Manager agent is already preselected under the Agents list on the left (it is the only agent appearing in that list anyway). You can use the same questions you asked when testing the individual agents above.
 
![alt text](images/image30.png)

We encourage you to explore the behavior of the solution further, by asking more "loaded" questions, which involve more than one agent to answer. For example, you could ask "Please tell me about the status of the warehouse docks and let me know what to do with surplus if there is any." Note how you may get asked follow-up clarification questions by the agent, to retrieve more specific information, for example, which exact truck ID you are asking about.
What this is trying to show is how you can send fairly detailed questions and instructions to an agent, but you can also give it more autonomy in how to address a request, by involving multiple agents and tools.

![alt text](images/image32.png)

Congratulations! You have create a complete agentic AI solution, without writing a single line of code! 

## Summary

In this lab, we went through the use case of an operational manager at a warehouse, which uses an agentic solution to handle arriving and possibly surplus products. We started by creating a number of agents for tasks like weather retrieval or surplus analysis. For one aspect, we imported an external agent running in watsonx.ai. Finally, all of it came together in the orchestrating agent that serves as the main frontend.

After we had all configured to be used by the AI Agent chat, we can interact with the solution through the main chat interface, and the system will execute and delegate to the appropriate agent.

Note that the intention of this exercise is to provide you with a starting point. Some parts of this solution are simulated, and would have to be fully implemented for a real solution. Moreover, a true agentic solution would add a reasoning layer on top of what you have built here, which allows coming up with a plan for handling, in this case, warehouse situation autonomously, making its own decisions along the way. We believe that those solutions are yet to become real.

But hopefully it triggered some ideas for you about how to leverage AI agents in your business environment.
