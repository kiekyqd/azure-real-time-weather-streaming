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
> ![cost analysis](https://github.com/user-attachments/assets/02c4bb62-29fa-4ac6-98d2-07213b13bb40)

**Decision**: Azure Function App was selected for continued use due to its simpler architecture and significantly lower cost for this scenario.

### Step 6: Processing and Loading Data with Microsoft Fabric

Microsoft Fabric was used to ingest and store real-time weather data for downstream analytics. The pipeline connects **Azure Event Hub** as the streaming source to a **Kusto DB** destination using Fabric’s Event Stream feature:

- Created a dedicated **Fabric workspace** to manage project assets.
- Set up an **Eventhouse**, which automatically provisioned a **Kusto (KQL) database** for real-time analytics.
- Created an **Event Stream pipeline** to connect Azure Event Hub (configured with shared access policy) to the KQL database.
 ![event-stream](https://github.com/user-attachments/assets/b82df29a-a2bc-4b25-9ec1-68a20b8c5ec8)

- Selected **JSON** as the data format and used **“Event Processing Before Ingestion”** mode to automatically generate the KQL destination table.
- Published the stream pipeline to begin ingesting and storing weather data in real time.
- Verified data flow by previewing the records and row count within the KQL table.
![count1](https://github.com/user-attachments/assets/503956fb-bb1d-4222-a973-519805ae3d4b)

![count2](https://github.com/user-attachments/assets/5781964f-6711-4cc9-a363-c4a77293025d)

This integration enabled seamless ingestion of real-time weather data into Fabric's analytics engine, forming the basis for live reporting and alerting.

### Step 7: Visualizing Real-Time Data with Power BI

Power BI was used to build weather reporting dashboards powered by real-time data stored in the KQL database. Two approaches were explored—**Power BI Desktop (Import Mode)** for flexibility and **Power BI Online (Direct Query)** for live updates:

- Connected Power BI to the **Kusto DB** in Microsoft Fabric using the cluster URI and database/table name.
- Imported weather data into Power BI Desktop to enable rich visuals, calculated columns, and layout customizations.
- Designed a complete weather report, including:
  - Current weather snapshot and forecast
  - 3-day trends and condition icons
  - Air quality interpretation using DAX
- Created filtered datasets to support both historical and latest weather views.
  
  ![powerbi1](https://github.com/user-attachments/assets/2b04195a-9450-4df2-8666-f84c54182d6e)
  ![powerbi2](https://github.com/user-attachments/assets/27110ed7-6ce3-4493-b242-c3cb912f7f12)
 
- Published the report to the Fabric workspace and configured scheduled data refresh.
  
  ![uploaded-powerbi](https://github.com/user-attachments/assets/4acb2322-69cf-46e0-b517-62da79a09b24)

  
Additionally, Power BI Online was briefly used to demonstrate a **real-time dashboard** powered by direct query from KQL. While suitable for near real-time insights, customization options were more limited compared to Desktop.

This step brought the pipeline's output to life, offering both dynamic and polished views into live weather conditions.

### Step 8: Configuring Real-Time Alerts with Microsoft Fabric Data Activator

To enable real-time weather alert notifications, Microsoft Fabric's **Data Activator** was integrated with the streaming pipeline:

- Created a dedicated **KQL Query Set** to detect weather alerts from the ingested data.
- Wrote KQL logic to check for non-empty alert fields, extract alert values, and apply timestamp-based filtering to avoid duplicate notifications.
![logic](https://github.com/user-attachments/assets/5fd6ad8f-b64e-42c8-85e6-cfaee5c946ac)
  
- Configured **Data Activator** to automatically evaluate the query every 5 minutes.
- Set up email notifications triggered by query results when new alerts are identified.
- Verified functionality by injecting test events into **Azure Event Hub** and observing alert delivery.
<img src="https://github.com/user-attachments/assets/3aa78b6b-cfb9-4db4-a0c6-f7977bee8b13" alt="test alert" width="50%" />

  
This setup enabled automatic email alerts when extreme weather conditions are detected—enhancing the pipeline’s responsiveness to real-time data.

### Step 9: Testing the End-to-End Pipeline

A final test was conducted to validate the complete real-time streaming pipeline from ingestion to visualization and alerting:

- Temporarily stopped the **Azure Function App** to simulate a data flow interruption.
- Observed that the **Power BI dashboard** stopped updating, confirming real-time dependency.
- Restarted the Function App and verified end-to-end data flow:
  - Weather data resumed streaming to **Azure Event Hub**.
  - **KQL database** in Microsoft Fabric reflected the new records.
  - **Power BI** visuals auto-refreshed with the updated data.
- Confirmed that **email alerts** were triggered appropriately when test alert conditions were met.
  
![alert1](https://github.com/user-attachments/assets/876377b8-c2a6-42e5-85cd-3e08e3f89685)

![alert3](https://github.com/user-attachments/assets/5c8691fb-feb7-4f48-b025-1b258872a590)

This test validated the pipeline’s reliability and confirmed smooth integration across all components—from API ingestion to live dashboards and real-time alerts.

## Learning & Inspiration

I followed the methodology and step-by-step guidance from **[Mr. K Talks Tech](https://www.youtube.com/watch?v=TIjgNlkvqxI&t=29s)**, adapting his approach to build my own end-to-end real-time data pipeline using Microsoft Azure and Microsoft Fabric.

Through this hands-on project, I gained practical experience with key Azure services, including **Azure Event Hub**, **Databricks**, **Azure Function App**, **Azure Key Vault**, **Microsoft Fabric** (Event Stream & Kusto DB), **Power BI** (Desktop & Online), and **Data Activator** for real-time alerting.

This journey enhanced my understanding of **cloud-based streaming architectures**, **secure credential management**, **cost optimization**, and **real-time analytics**—while shaping the solution to reflect real-world data engineering workflows.

## Project Summary & Conclusion

This project showcases a complete real-time streaming data pipeline built with Azure and Microsoft Fabric—covering ingestion, transformation, storage, reporting, and real-time alerting.

By simulating continuous weather data and integrating key services like Event Hub, Databricks, Azure Functions, Power BI, and Data Activator, I gained hands-on experience designing and implementing modern data workflows.

Throughout the process, I focused on security best practices, cost-effective architecture choices, and real-time responsiveness—aligning the solution with real-world data engineering needs.

This has been a valuable opportunity to deepen my skills in cloud-based data systems and build a solution that’s both practical and production-ready.





