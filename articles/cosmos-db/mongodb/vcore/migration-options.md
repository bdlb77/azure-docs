---
title: Options to migrate data from MongoDB
titleSuffix: Azure Cosmos DB for MongoDB vCore
description: Review various options to migrate your data from other MongoDB sources to Azure Cosmos DB for MongoDB vCore.
author: gahl-levy
ms.author: gahllevy
ms.reviewer: sidandrews
ms.service: cosmos-db
ms.subservice: mongodb-vcore
ms.topic: conceptual
ms.date: 09/12/2023
---

# Options to migrate data from MongoDB to Azure Cosmos DB for MongoDB vCore

This document describes the various options to lift and shift your MongoDB workloads to Azure Cosmos DB for MongoDB vCore offering.

## Premigration assessment

Assessment involves finding out whether you're using the [features and syntax that are supported](./compatibility.md). The aim of this stage is to create a list of incompatibilities and warnings, if any. After you have the assessment results, you can try to address the findings during rest of the migration planning.

The [Azure Cosmos DB Migration for MongoDB extension](/sql/azure-data-studio/extensions/database-migration-for-mongo-extension) in Azure Data Studio helps you assess a MongoDB workload for migrating to Azure Cosmos DB for MongoDB. You can use this extension to run an end-to-end assessment on your workload and find out the actions that you may need to take to seamlessly migrate your workloads on Azure Cosmos DB. During  the assessment of a MongoDB endpoint, the extension reports all the discovered resources.

> [!TIP]
> We recommend you to go through [the supported features and syntax](./compatibility.md) in detail, as well as perform a proof-of-concept prior to the actual migration.

## Native MongoDB tools (Offline)

You can use the native MongoDB tools such as *mongodump/mongorestore*, *mongoexport/mongoimport* to migrate datasets offline (without replicating live changes) to Azure Cosmos DB for MongoDB vCore offering.

| Scenario | MongoDB native tool |
| --- | --- |
| Move subset of database data (JSON/CSV-based) | *mongoexport/mongoimport* |
| Move whole database (BSON-based) | *mongodump/mongorestore* |

- *mongoexport/mongoimport* is the best pair of migration tools for migrating a subset of your MongoDB database.
  - *mongoexport* exports your existing data to a human-readable JSON or CSV file. *mongoexport* takes an argument specifying the subset of your existing data to export. 
  - *mongoimport* opens a JSON or CSV file and inserts the content into the target database instance (Azure Cosmos DB for MongoDB vCore in this case.). 
  - JSON and CSV aren't a compact format; you may incur excess network charges as *mongoimport* sends data to Azure Cosmos DB for MongoDB vCore.
- *mongodump/mongorestore* is the best pair of migration tools for migrating your entire MongoDB database. The compact BSON format makes more efficient use of network resources as the data is inserted into Azure Cosmos DB for MongoDB vCore.
  - *mongodump* exports your existing data as a BSON file.
  - *mongorestore* imports your BSON file dump into Azure Cosmos DB for MongoDB vCore.

> [!NOTE]
> The MongoDB native tools can move data only as fast as the host hardware allows.

## Data migration using Azure Databricks (Offline/Online)

- This method offers full control of the migration rate and data transformation. It can also support large datasets that are in TBs in size.
- [Azure Databricks](https://azure.microsoft.com/services/databricks/) is a platform as a service (PaaS) offering for [Apache Spark](https://spark.apache.org/). You can use Azure Databricks to do an offline/online migration of databases from MongoDB to Azure Cosmos DB for MongoDB.
- Here's how you can [migrate data to Azure Cosmos DB for MongoDB vCore offline using Azure Databricks](../migrate-databricks.md#provision-an-azure-databricks-cluster)

## Next steps

- Migrate data to Azure Cosmos DB for MongoDB vCore [using native MongoDB tools](how-to-migrate-native-tools.md).
- Migrate data to Azure Cosmos DB for MongoDB vCore [using Azure Databricks](../migrate-databricks.md).
