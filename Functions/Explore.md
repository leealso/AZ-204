# Explore Azure Functions
Azure Functions lets you develop serverless applications on Microsoft Azure. You can write just the code you need for the problem at hand, without worrying about a whole application or the infrastructure to run it.

Azure Functions are a great solution for processing data, integrating systems, working with the internet-of-things (IoT), and building simple APIs and microservices. Consider Functions for tasks like image or order processing, file maintenance, or for any tasks that you want to run on a schedule. 

Azure Functions supports _triggers_, which are ways to start execution of your code, and _bindings_, which are ways to simplify coding for input and output data. They can all define input, actions, conditions, and output.

## Compare Azure Functions and Azure Logic Apps
Both Functions and Logic Apps enable serverless workloads. Azure Functions is a serverless compute service, whereas Azure Logic Apps provides serverless workflows. Both can create complex orchestrations. An orchestration is a collection of functions or steps, called actions in Logic Apps, that are executed to accomplish a complex task.

For Azure Functions, you develop orchestrations by writing code and using the Durable Functions extension. For Logic Apps, you create orchestrations by using a GUI or editing configuration files.

You can mix and match services when you build an orchestration, calling functions from logic apps and calling logic apps from functions. The following table lists some of the key differences between these:
 | Azure Functions | Logic Apps
 --- | --- | --- 
Development | Code-first (imperative) | Designer-first (declarative)
Connectivity | About a dozen built-in binding types, write code for custom bindings | Large collection of connectors, Enterprise Integration Pack for B2B scenarios, build custom connectors
Actions | Each activity is an Azure function; write code for activity functions | Large collection of ready-made actions
Monitoring | Azure Application Insights | Azure portal, Azure Monitor logs
Management | REST API, Visual Studio | Azure portal, REST API, PowerShell, Visual Studio
Execution context | Can run locally or in the cloud | Supports run-anywhere scenarios

### Compare Functions and WebJobs
Like Azure Functions, Azure App Service WebJobs with the WebJobs SDK is a code-first integration service that is designed for developers. Both are built on Azure App Service and support features such as source control integration, authentication, and monitoring with Application Insights integration.

Azure Functions is built on the WebJobs SDK, so it shares many of the same event triggers and connections to other Azure services. Here are some factors to consider when you're choosing between Azure Functions and WebJobs with the WebJobs SDK:
 | Functions | WebJobs with WebJobs SDK
 --- | --- | ---
Serverless app model with automatic scaling | Yes | No
Develop and test in browser | Yes | No
Pay-per-use pricing | Yes | No
Integration with Logic Apps | Yes | No
Trigger events | Timer
Azure Storage queues and blobs
Azure Service Bus queues and topics
Azure Cosmos DB
Azure Event Hubs
HTTP/WebHook (GitHub
Slack)
Azure Event Grid | Timer
Azure Storage queues and blobs
Azure Service Bus queues and topics
Azure Cosmos DB
Azure Event Hubs
File system