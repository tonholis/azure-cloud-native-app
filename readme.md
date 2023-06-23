# Cloud Native 

This is not a real app.

It's a proof-of-concept and over-engineered app to understand "cloud-native" resources in Azure to compose a distributed system.

## What does it do?

It gets an image from the end user in the website to be resized/processed in the backend. The user gets notified (via SignalR) when the image is processed and ready to download.

## Project

```
src
  CloudNativeApp.sln	--> Solution file
  Website				--> A Blazor client/website
  Backend
	Functions			--> Azure Functions to handle requests from the frontend
	API					--> A .NET API as an alternative to Azure Functions
	Resizer				--> A .NET Worker Service to resize images
  Shared				--> A Shared library
```

## Stack

* .NET 7 and C#
* MassTransit to integrate with service bus.
* Azure Service Bus
* Azure Functions to run a lightweight version of the RESTful API.
* Azure Container Instance to run the Resizer Service
* Azure Cosmos DB to store transaction data.
* Azure SignalR to connect backend with frontend via websockets.
* Azure Storage Account to store images.
* Azure App Service to host the RESTful API and the website.

## Workflow

1. The user uploads an image on the website.
2. The website calls the RESTful API (or Azure Functions) to:
2.1 Store the image on Azure Storage Account.
2.2 Store transaction details in Cosmos DB.
2.3 Publish ImageUploaded message in Azure Service Bus.
3. The Resizer Service consumes the message ImageUploaded, and:
3.1 Gets the image from Azure Storage Account.
3.2 Resize the image and stores it in Azure Storage Account.
3.3 Publishes ImageResized message to Azure Service Bus.
4. The website's backend consumes the ImageResized message, and:
4.1 Sends a message to Azure SignalR
5. The website frontend receives the message via websockets (SignalR) and updates the UI.


## Setup 1 - Azure Functions as backend

The website sends requests to Azure Functions. 

The goal is to validate costs and resiliency of serverless techniques in Azure and how to deal with cold-start's of Azure Functions.

## Setup 2 - RESTful API as backend

The website sends requests to a RESTful API which runs in a Docker container hosted in Azure App Service PaaS.

The goal is to validate PaaS hosting model in Azure.

## Other things to test later

* GitHub Actions to automate deployments.
* Built-in Authentication in App Service for the website.
* Azure Managed Identities to protect the backend.
* Bicep to automate creation of resources in Azure.

## Tools 

* GitHub
* Visual Studio Code
* Old Macbook Pro mid 2012