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

## Architecture Overview


## Technology Used

| Category                | Tool/Service                        | Description |
|-------------------------|-------------------------------------|-------------|
| **Cloud Platform**      | Azure Function App                  | Executes timer-triggered Python functions every 30 seconds to fetch and stream weather data from WeatherAPI to Event Hub. |
|                         | Azure Databricks                    | Used for structured streaming with PySpark to pull, process, and stream weather data to Event Hub. |
|                         | Azure Event Hub                     | High-throughput event ingestion service acting as a real-time buffer between data ingestion and analytics. |
|                         | Microsoft Fabric Event Stream       | Streams data from Event Hub into Kusto DB, supporting real-time event processing and schema mapping. |
|                         | Microsoft Fabric Kusto DB           | Real-time analytics database optimized for time-series queries using KQL. Stores and serves streaming weather data for reporting and alerting. |
|                         | Azure Key Vault                     | Securely stores API keys and credentials, accessed via Managed Identity without hardcoding secrets. |
| **Programming & Querying** | Python (Function & Databricks)     | Used for API calls, JSON parsing, streaming to Event Hub, and securely accessing secrets via `DefaultAzureCredential`. |
|                         | KQL (Kusto Query Language)          | Powers querying and filtering of real-time weather data, including alert detection and dashboard preparation. |
|                         | DAX (Power BI Calculations)         | Used to create calculated columns like air quality bands and timestamp formatting in Power BI Desktop. |
| **Visualization & Alerts** | Power BI Desktop (Import Mode)     | Offers full flexibility in modeling, formatting, and building interactive dashboards. Supports scheduled refresh. |
|                         | Power BI Online (Direct Query)      | Enables live dashboards with 1-second auto-refresh using KQL query sets from Fabric. |
|                         | Microsoft Fabric Data Activator     | Sends real-time email alerts when KQL-based conditions are met (e.g., weather warnings), checking every 5 minute. |

