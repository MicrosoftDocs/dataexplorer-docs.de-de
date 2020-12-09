---
title: 'Übersicht über Client Bibliotheken: Azure Daten-Explorer'
description: In diesem Artikel werden die Client Bibliotheken in Azure Daten-Explorer aufgeführt.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: c33906e61751e7e1bf871be1286a3b25051c8342
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96868585"
---
# <a name="client-libraries"></a>Clientbibliotheken

In der folgenden Tabelle werden die verschiedenen Bibliotheken aufgelistet, die für die Abfrage, Erfassung und Arm/RP-Verwaltung bereitgestellt werden. Verwenden Sie diese Bibliotheken für Azure-APIs und, um die Funktionen von Azure Daten-Explorer Programm gesteuert aufzurufen. 


|    Sprache/Funktionalität        |    Abfrage        |    Erfassung        |    Arm/RP-Verwaltung        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .NET        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)         |
|    .NET Standard        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2020_09_18)        |
|    JavaScript        |             |             |    [npm](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    NodeJS        |    [NPM](https://www.npmjs.com/package/azure-kusto-data) - [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [NPM](https://www.npmjs.com/package/azure-kusto-ingest)-[GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)               |    [npm](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [Pypi](https://pypi.org/project/azure-kusto-ingest/)    [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [Pypi](https://pypi.org/project/azure-kusto-data/)      [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [Pypi](https://pypi.org/project/azure-mgmt-kusto/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt)        |
|    Ruby        |             |             |    [Schmuck]( https://rubygems.org/gems/azure_mgmt_kusto)         |
|    PowerShell        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [Paket](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [Azure CLI](/cli/azure/install-azure-cli)         |
|    Rest-API        |    [REST](rest/index.md)        |    [REST](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [NPM](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>Tools und Integrationen

* Lightinerfassungs: [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* Einklickungskit: [GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* Kafka: [GitHub](https://github.com/Azure/kafka-sink-azure-kusto)
* Logstash: [GitHub](https://github.com/Azure/logstash-output-kusto) 
* Spark: [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)