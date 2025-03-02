---
title: Monitoring Azure OpenAI Service
description: Learn how to use Azure Monitor tools like Log Analytics to capture and analyze metrics and data logs for your Azure OpenAI Service resources.
author: mrbullwinkle
ms.author: mbullwin
ms.service: cognitive-services
ms.subservice: openai
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 09/07/2023
---

# Monitoring Azure OpenAI Service

When you have critical applications and business processes that rely on Azure resources, you want to monitor those resources for their availability, performance, and operation.

This article describes the monitoring data generated by Azure OpenAI Service. Azure OpenAI is part of Azure AI services, which uses [Azure Monitor](../../../azure-monitor/monitor-reference.md). If you're unfamiliar with the features of Azure Monitor that are common to all Azure services that use the service, see [Monitoring Azure resources with Azure Monitor](../../../azure-monitor/essentials/monitor-azure-resource.md).

## Data collection and routing in Azure Monitor

Azure OpenAI collects the same kinds of monitoring data as other Azure resources. You can configure Azure Monitor to generate data in activity logs, resource logs, virtual machine logs, and platform metrics. For more information, see [Monitoring data from Azure resources](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data-from-azure-resources).

Platform metrics and the Azure Monitor activity log are collected and stored automatically. This data be routed to other locations by using a diagnostic setting. Azure Monitor resource logs aren't collected and stored until you create a diagnostic setting and then route the logs to one or more locations.

When you create a diagnostic setting, you specify which categories of logs to collect. For more information about creating a diagnostic setting by using the Azure portal, the Azure CLI, or PowerShell, see [Create diagnostic setting to collect platform logs and metrics in Azure](/azure/azure-monitor/platform/diagnostic-settings).

Keep in mind that using diagnostic settings and sending data to Azure Monitor Logs has other costs associated with it. For more information, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs).

The metrics and logs that you can collect are described in the following sections.

## Analyze metrics

You can analyze metrics for your Azure OpenAI Service resources with Azure Monitor tools in the Azure portal. From the **Overview** page for your Azure OpenAI resource, select **Metrics** under **Monitoring** in the left pane. For more information, see [Get started with Azure Monitor metrics explorer](../../../azure-monitor/essentials/metrics-getting-started.md).

:::image type="content" source="../media/monitoring/monitor-metrics-azure-portal.png" alt-text="Screenshot that shows how to monitor and analyze metrics data for an Azure OpenAI resource in the Azure portal." lightbox="../media/monitoring/monitor-metrics-azure-portal.png" border="false":::

Azure OpenAI has commonality with a subset of Azure AI services. For a list of all platform metrics collected for Azure OpenAI and similar Azure AI services by Azure Monitor, see [Supported metrics for Microsoft.CognitiveServices/accounts](/azure/azure-monitor/reference/supported-metrics/microsoft-cognitiveservices-accounts-metrics).

### Azure OpenAI metrics

The following table summarizes the current subset of metrics available in Azure OpenAI. 

|Metric|Display Name|Category|Unit|Aggregation|Description|Dimensions|
|---|---|---|---|---|---|---|
|`BlockedCalls` |Blocked Calls |HTTP | Count |Total |Number of calls that exceeded rate or quota limit. | `ApiName`, `OperationName`, `Region`, `RatelimitKey` |
|`ClientErrors` |Client Errors |HTTP | Count |Total |Number of calls with a client side error (HTTP response code 4xx). |`ApiName`, `OperationName`, `Region`, `RatelimitKey` |
|`DataIn` |Data In |HTTP | Bytes |Total |Size of incoming data in bytes. |`ApiName`, `OperationName`, `Region` |
|`DataOut` |Data Out |HTTP | Bytes |Total |Size of outgoing data in bytes. |`ApiName`, `OperationName`, `Region` |
|`FineTunedTrainingHours` |Processed FineTuned Training Hours |USAGE |Count |Total |Number of training hours processed on an Azure OpenAI fine-tuned model. |`ApiName`, `ModelDeploymentName`, `FeatureName`, `UsageChannel`, `Region` |
|`GeneratedTokens` |Generated Completion Tokens |USAGE |Count |Total |Number of generated tokens from an Azure OpenAI model. |`ApiName`, `ModelDeploymentName`, `FeatureName`, `UsageChannel`, `Region` |
|`Latency` |Latency |HTTP |MilliSeconds |Average |Latency in milliseconds. |`ApiName`, `OperationName`, `Region`, `RatelimitKey` |
|`ProcessedPromptTokens` |Processed Prompt Tokens |USAGE |Count |Total |Number of prompt tokens processed on an Azure OpenAI model. |`ApiName`, `ModelDeploymentName`, `FeatureName`, `UsageChannel`, `Region` |
|`Ratelimit` |Ratelimit |HTTP |Count |Total |The current rate limit of the rate limit key. |`Region`, `RatelimitKey` |
|`ServerErrors` |Server Errors |HTTP | Count |Total |Number of calls with a service internal error (HTTP response code 5xx). |`ApiName`, `OperationName`, `Region`, `RatelimitKey` |
|`SuccessfulCalls` |Successful Calls |HTTP |Count |Total |Number of successful calls. |`ApiName`, `OperationName`, `Region`, `RatelimitKey` |
|`SuccessRate` |Availability Rate |SLI |Percentage |Total |Availability percentage for the calculation `(TotalCalls - ServerErrors)/TotalCalls` for `ServerErrors` of HTTP response code 5xx. |`ApiName`, `ModelDeploymentName`, `FeatureName`, `UsageChannel`, `Region` |
|`TokenTransaction` |Processed Inference Tokens |USAGE |Count |Total |Number of inference tokens processed on an Azure OpenAI model. |`ApiName`, `ModelDeploymentName`, `FeatureName`, `UsageChannel`, `Region` |
|`TotalCalls` |Total Calls |HTTP |Count |Total |Total number of calls. |`ApiName`, `OperationName`, `Region`, `RatelimitKey` |
|`TotalErrors` |Total Errors |HTTP |Count |Total |Total number of calls with an error response (HTTP response code 4xx or 5xx). |`ApiName`, `OperationName`, `Region`, `RatelimitKey` |

## Configure diagnostic settings

All of the metrics are exportable with [diagnostic settings in Azure Monitor](/azure/azure-monitor/essentials/diagnostic-settings). To analyze logs and metrics data with Azure Monitor Log Analytics queries, you need to configure diagnostic settings for your Azure OpenAI resource and your Log Analytics workspace.

1. From your Azure OpenAI resource page, under **Monitoring**, select **Diagnostic settings** on the left pane. On the **Diagnostic settings** page, select **Add diagnostic setting**.

   :::image type="content" source="../media/monitoring/monitor-add-diagnostic-setting.png" alt-text="Screenshot that shows how to open the Diagnostic setting page for an Azure OpenAI resource in the Azure portal." border="false":::

1. On the **Diagnostic settings** page, configure the following fields:

   1. Select **Send to Log Analytics workspace**.
   1. Choose your Azure account subscription.
   1. Choose your Log Analytics workspace.
   1. Under **Logs**, select **allLogs**.
   1. Under **Metrics**, select **AllMetrics**.

   :::image type="content" source="../media/monitoring/monitor-configure-diagnostics.png" alt-text="Screenshot that shows how to configure diagnostic settings for an Azure OpenAI resource in the Azure portal.":::

1. Enter a **Diagnostic setting name** to save the configuration.

1. Select **Save**.

After you configure the diagnostic settings, you can work with metrics and log data for your Azure OpenAI resource in your Log Analytics workspace.

## Analyze logs

Data in Azure Monitor Logs is stored in tables where each table has its own set of unique properties.

All resource logs in Azure Monitor have the same fields followed by service-specific fields. For information about the common schema, see [Common and service-specific schemas for Azure resource logs](../../../azure-monitor/essentials/resource-logs-schema.md).

The [activity log](../../../azure-monitor/essentials/activity-log.md) is a type of platform log in Azure that provides insight into subscription-level events. You can view this log independently or route it to Azure Monitor Logs. In the Azure portal, you can use the activity log in Azure Monitor Logs to run complex queries with Log Analytics.

For a list of the types of resource logs available for Azure OpenAI and similar Azure AI services, see [Microsoft.CognitiveServices](/azure/role-based-access-control/resource-provider-operations#microsoftcognitiveservices) Azure resource provider operations.

## Use Kusto queries

After you deploy an Azure OpenAI model, you can send some completions calls by using the **playground** environment in [Azure AI Studio](https://oai.azure.com/).

:::image type="content" source="../media/monitoring/azure-openai-studio-playground.png" alt-text="Screenshot that shows how to generate completions for an Azure OpenAI resource in the Azure OpenAI Studio playground." lightbox="../media/monitoring/azure-openai-studio-playground.png" border="false":::

Any text that you enter in the **Completions playground** or the **Chat completions playground** generates metrics and log data for your Azure OpenAI resource. In the Log Analytics workspace for your resource, you can query the monitoring data by using the [Kusto](/azure/data-explorer/kusto/query/) query language.

> [!IMPORTANT]
> The **Open query** option on the Azure OpenAI resource page browses to Azure Resource Graph, which isn't described in this article.
> The following queries use the query environment for Log Analytics. Be sure to follow the steps in [Configure diagnostic settings](#configure-diagnostic-settings) to prepare your Log Analytics workspace. 

1. From your Azure OpenAI resource page, under **Monitoring** on the left pane, select **Logs**.

1. Select the Log Analytics workspace that you configured with diagnostics for your Azure OpenAI resource. 

1. From the **Log Analytics workspace** page, under **Overview** on the left pane, select **Logs**.

   The Azure portal displays a **Queries** window with sample queries and suggestions by default. You can close this window.

For the following examples, enter the Kusto query into the edit region at the top of the **Query** window, and then select **Run**. The query results display below the query text.

The following Kusto query is useful for an initial analysis of Azure Diagnostics (`AzureDiagnostics`) data about your resource: 

```kusto
AzureDiagnostics
| take 100
| project TimeGenerated, _ResourceId, Category, OperationName, DurationMs, ResultSignature, properties_s
```

This query returns a sample of 100 entries and displays a subset of the available columns of data in the logs. In the query results, you can select the arrow next to the table name to view all available columns and associated data types.

:::image type="content" source="../media/monitoring/log-analytics-diagnostics-query.png" alt-text="Screenshot that shows the Log Analytics query results for Azure Diagnostics data about the Azure OpenAI resource." lightbox="../media/monitoring/log-analytics-diagnostics-query.png":::

To see all available columns of data, you can remove the scoping parameters line `| project ...` from the query:

```kusto
AzureDiagnostics
| take 100
```

To examine the Azure Metrics (`AzureMetrics`) data for your resource, run the following query:

```kusto
AzureMetrics
| take 100
| project TimeGenerated, MetricName, Total, Count, Maximum, Minimum, Average, TimeGrain, UnitName
```

The query returns a sample of 100 entries and displays a subset of the available columns of Azure Metrics data:

:::image type="content" source="../media/monitoring/log-analytics-metrics-query.png" alt-text="Screenshot that shows the Log Analytics query results for Azure Metrics data about the Azure OpenAI resource." lightbox="../media/monitoring/log-analytics-metrics-query.png":::

> [!NOTE]
> When you select **Monitoring** > **Logs** in the Azure OpenAI menu for your resource, Log Analytics opens with the query scope set to the current resource. The visible log queries include data from that specific resource only. To run a query that includes data from other resources or data from other Azure services, select **Logs** from the **Azure Monitor** menu in the Azure portal. For more information, see [Log query scope and time range in Azure Monitor Log Analytics](../../../azure-monitor/logs/scope.md) for details.

## Set up alerts

Azure Monitor alerts proactively notify you when important conditions are found in your monitoring data. They allow you to identify and address issues in your system before your users notice them. You can set alerts on [metrics](/azure/azure-monitor/alerts/alerts-types#metric-alerts), [logs](/azure/azure-monitor/alerts/alerts-types#log-alerts), and the [activity log](/azure/azure-monitor/alerts/alerts-types#activity-log-alerts). Different types of alerts have different benefits and drawbacks.

Every organization's alerting needs vary and can change over time. Generally, all alerts should be actionable and have a specific intended response if the alert occurs. If an alert doesn't require an immediate response, the condition can be captured in a report rather than an alert. Some use cases might require alerting anytime certain error conditions exist. In other cases, you might need alerts for errors that exceed a certain threshold for a designated time period.

Errors below certain thresholds can often be evaluated through regular analysis of data in Azure Monitor Logs. As you analyze your log data over time, you might discover that a certain condition doesn't occur for an expected period of time. You can track for this condition by using alerts. Sometimes the absence of an event in a log is just as important a signal as an error.

Depending on what type of application you're developing with your use of Azure OpenAI, [Azure Monitor Application Insights](../../../azure-monitor/overview.md) might offer more monitoring benefits at the application layer.

## Next steps

- [Monitor Azure resources with Azure Monitor](../../../azure-monitor/essentials/monitor-azure-resource.md)
- [Understand log searches in Azure Monitor logs](../../../azure-monitor/logs/log-query-overview.md)
