# Real-Time Weather Streaming Data Engineering Project (Azure + Microsoft Fabric)

This is a complete end-to-end real-time data streaming solution built using **Azure services** and **Microsoft Fabric**. The project simulates and delivers continuous live weather data updates—including temperature, air quality, and weather conditions—and sends real-time alerts when extreme conditions occur. The data is visualized through interactive dashboards, powered by a modern streaming pipeline.

## Use Case
The objective of this project is to create a **real-time weather reporting system** that:

- Continuously fetches weather data (e.g., temperature, air quality) from an external API
- Streams the data every 30 seconds to **Azure Event Hub** via **Azure Databricks** or **Azure Function**
- Processes and stores the data in **Microsoft Fabric’s Kusto DB (Real-Time Analytics DB)**
- Visualizes the live data using **Power BI dashboards**
- Sends real-time email alerts when extreme weather conditions are detected

By completing this project, I built a fully automated real-time data pipeline that showcases my ability to ingest, process, alert, and visualize streaming data—making it a strong portfolio project as I pursue a career in data engineering.


## Architecture & Technology Overview

This project implements a full real-time data streaming architecture using Microsoft Azure and Microsoft Fabric. The system fetches weather data every 30 seconds, processes it, and delivers insights and alerts through dashboards and automated email notifications.

| **Stage**                    | **Technology/Service**                | **Purpose & Function**                                                                                                                                         |
|-----------------------------|----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Data Source**             | 🌦️ **Weather API**                    | Free-tier API (1M calls/month) providing real-time weather metrics like temperature, humidity, wind, condition, and air quality. Called every 30 seconds.     |
| **Ingestion (Option 1)**    | ⚙️ **Azure Databricks**               | PySpark-based structured streaming pipeline. Parses, flattens JSON from API, and streams data into Azure Event Hub using `foreachBatch()`.                   |
| **Ingestion (Option 2)**    | ⚙️ **Azure Function App**             | Lightweight, cost-effective ingestion using a Python Timer Trigger. Runs every 30 seconds to fetch and stream data to Event Hub using Managed Identity.       |
| **Streaming Backbone**      | ⚡ **Azure Event Hub**                 | High-throughput ingestion service that buffers and streams event data to downstream services.                                                                 |
| **Real-Time Analytics**     | 🔁 **Microsoft Fabric Event Stream**   | Connects to Event Hub and maps streaming data into Kusto DB in real-time. Uses "Event Processing Before Ingestion" mode to simplify schema generation.       |
|                             | 📦 **Microsoft Fabric Kusto DB**       | Time-series optimized database for querying real-time weather data using KQL. Supports reporting and alerting.                                                 |
| **Secrets Management**      | 🔐 **Azure Key Vault**                 | Stores API keys and credentials. Accessed securely using Managed Identity (via `DefaultAzureCredential`)—no hardcoded secrets.                               |
| **Programming & Querying**  | 🐍 **Python (Function & Databricks)**  | Handles API integration, data parsing, streaming logic, and secret retrieval from Key Vault.                                                                 |
|                             | 📜 **KQL (Kusto Query Language)**      | Enables filtering, transformation, and alert detection from real-time Kusto DB data. Used to power dashboards and conditions.                                 |
|                             | 🧮 **DAX (Power BI)**                  | Used in Power BI Desktop to build calculated columns (e.g., air quality bands) and enhance visuals.                                                           |
| **Visualization**           | 📊 **Power BI Desktop (Import Mode)**  | Provides full customization and advanced modeling. Supports calculated columns, scheduled refresh, and high formatting control.                              |
|                             | 🌐 **Power BI Online (Direct Query)**  | Supports lightweight, real-time dashboards with 1-second auto-refresh using live KQL queries.                                                                 |
| **Real-Time Alerts**        | 🚨 **Microsoft Fabric Data Activator** | Executes alert queries every 5 minute. Sends real-time email alerts when extreme weather is detected. Prevents duplication using logic for recent triggers.   |

## Implementation Steps

### Step 1: Setting Up Azure Environment

Established the foundational infrastructure to support an end-to-end real-time data streaming pipeline:

- Created a new resource group in Azure.
- Configured an external weather data provider (WeatherAPI) to simulate live ingestion.
- Provisioned key Azure services:
  - **Azure Databricks** for PySpark-based structured streaming.
  - **Azure Function App** for lightweight, timer-triggered ingestion.
  - **Azure Event Hub** for high-throughput, real-time data ingestion.
  - **Microsoft Fabric Capacity** for event processing and analytics.
  - **Azure Key Vault** for managing API keys and secrets securely.


### Step 2: Implementing Secure Secret Management with Azure Key Vault

Azure Key Vault was used to securely manage sensitive secrets such as API keys and connection strings, enabling password-less authentication across compute services:

- Granted access using:
  - **Service Principal** for Azure Databricks  
  - **Managed Identity** for Azure Function App
- Created and stored the required secrets (e.g., API keys, connection strings).
- Configured each service to securely retrieve secrets from the vault—ensuring no credentials were hardcoded into code or notebooks.

This setup enabled secure, scalable secret management that adheres to cloud security best practices.


### Step 3: Streaming Ingestion with Azure Databricks

Azure Databricks was leveraged to prototype and validate the initial ingestion logic using PySpark structured streaming:

- Created a compute cluster and launched a new notebook workspace.
- Installed necessary libraries and configured the cluster for streaming workloads.
- Designed and tested the ingestion flow to pull weather data—such as current conditions, 3-day forecasts, weather alerts, and air quality index—from an external API.
- Explored and validated the incoming data structure within notebooks.
- Streamed transformed weather data into **Azure Event Hub** at **30-second intervals**.

This environment was used to validate the ingestion pipeline before implementing the same logic using Azure Functions for comparison in cost and deployment approach.


### Step 4: Implementing Lightweight Ingestion with Azure Function

A streamlined ingestion approach was built using a Python-based Azure Function App with a **30-second timer trigger**, offering a lightweight and scalable alternative to Databricks:

- Reused the same ingestion logic to fetch weather data (e.g., current conditions, 3-day forecasts, alerts, air quality index).
- Developed and deployed the function using **Visual Studio Code** with Python & Azure Functions extensions.
- Leveraged **Managed Identity** for secure access to secrets and Event Hub—configured previously in **Azure Key Vault**.
- Validated end-to-end data flow by monitoring real-time events in **Event Hub Data Explorer**.

This solution was implemented to execute the same ingestion logic as Databricks, enabling a direct comparison of cost and architecture approaches.

### Step 5: Evaluating Cost

A cost and architectural comparison was conducted to determine the most suitable ingestion approach for the pipeline. Both **Azure Databricks** and **Azure Function App** were assessed based on pricing, complexity, and workload suitability:

| Criteria          | Azure Databricks                      | Azure Function App                                   |
|-------------------|----------------------------------------|------------------------------------------------------|
| Monthly Cost    | ~958 NZD/month (DS3 v2 cluster)        | 0 NZD (within 1M executions/month free)              |
| Trigger Type    | Spark Structured Streaming              | Timer Trigger (every 30 seconds)                     |
| Complexity       | Requires cluster + Spark setup         | Simple Python script with built-in timer             |
| Ideal For       | High-volume, large-scale workloads     | Lightweight, API-based pipelines                     |

>  *Cost estimates were calculated using the [Azure Pricing Calculator](https://azure.microsoft.com/en-us/pricing/calculator/).*

**Decision**: Azure Function App was selected for continued use due to its simpler architecture and significantly lower cost for this scenario.

### Step 6: Processing and Loading with Microsoft Fabric

Microsoft Fabric was used to process and route streaming data from Azure Event Hub into a real-time analytics database using its **Event Stream** and **Kusto DB** components:

- Created a new **Microsoft Fabric workspace** to host all streaming and reporting assets.
- Set up an **Event Stream pipeline** to receive weather events from Azure Event Hub.
- Configured the stream to load data into an **auto-generated Kusto DB** (Real-Time Analytics database).
- Enabled **Event Processing Before Ingestion** to automatically create the target table schema.
- Verified real-time data flow by inspecting live previews and monitoring row count updates in the Kusto table.

This setup enabled seamless integration of live data into Microsoft Fabric, laying the foundation for real-time reporting and alerting in the next steps.











