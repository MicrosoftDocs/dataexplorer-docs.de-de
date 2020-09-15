---
title: 'cosmosdb_sql_request-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird das cosmosdb_sql_request-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 09/11/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 035705e0a15ab686af16b9e8b2a00268db21d6a2
ms.sourcegitcommit: c140bc3bc984f861df0b85e672d2e685e6659a54
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90109982"
---
# <a name="cosmosdb_sql_request-plugin"></a>Plug-in cosmosdb_sql_request

::: zone pivot="azuredataexplorer"

Das `cosmosdb_sql_request` Plug-in sendet eine SQL-Abfrage an einen Cosmos DB SQL-Netzwerk Endpunkt und gibt die Ergebnisse der Abfrage zurück. Dieses Plug-in dient primär zum Abfragen kleiner Datasets, z. b. zum Erweitern von Daten mit Verweis Daten, die in [Azure Cosmos DB](/azure/cosmos-db/)gespeichert werden.

## <a name="syntax"></a>Syntax

`evaluate``cosmosdb_sql_request` `(` *ConnectionString* `,` *sqlQuery* [ `,` *SQLPARAMETERS* [ `,` *Optionen*]]`)`

## <a name="arguments"></a>Argumente

|Argumentname | BESCHREIBUNG | Erforderlich/optional | 
|---|---|---|
| *ConnectionString* | Ein `string` Literale, das die Verbindungs Zeichenfolge angibt, die auf die abzufragende Cosmos DB Auflistung verweist. Sie muss *accountendpoint*, *Database*und *Collection*enthalten. Es kann *accountkey* enthalten, wenn ein Hauptschlüssel für die Authentifizierung verwendet wird. <br> **Beispiel:** `'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/ ;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;'`| Erforderlich |
| *SqlQuery*| Ein `string` Literalwert, der die auszuführende Abfrage angibt. | Erforderlich |
| *SQLPARAMETERS* | Ein konstanter Wert des Typs `dynamic` , der Schlüssel-Wert-Paare enthält, die als Parameter zusammen mit der Abfrage übergeben werden. Parameter Namen müssen mit beginnen `@` . | Optional |
| *Optionen* | Ein konstanter Wert des Typs `dynamic` , der Erweiterte Einstellungen als Schlüssel-Wert-Paare enthält. | Optional |
|| ----*Zu den unterstützten Options Einstellungen gehören:*-----
|      `armResourceId` | Abrufen des API-Schlüssels aus dem Azure Resource Manager <br> **Beispiel:** `/subscriptions/a0cd6542-7eaf-43d2-bbdd-b678a869aad1/resourceGroups/ cosmoddbresourcegrouput/providers/Microsoft.DocumentDb/databaseAccounts/cosmosdbacc`| 
|  `token` | Geben Sie das Azure AD Zugriffs Token an, das für die Authentifizierung beim Azure Resource Manager verwendet wird.
| `preferredLocations` | Steuern, von welchem Bereich die Daten abgefragt werden. <br> **Beispiel:** `['East US']` | |  

## <a name="set-callout-policy"></a>Festlegen der Legenden Richtlinie

Das Plug-in macht Aufrufe für die Cosmos DB. Stellen Sie sicher, dass die [Legenden-Richtlinie](../management/calloutpolicy.md) des Clusters Aufrufe des Typs `cosmosdb` an das Ziel- *cosmosdburi*ermöglicht.

Im folgenden Beispiel wird gezeigt, wie die Legenden Richtlinie für Cosmos DB definiert wird. Es wird empfohlen, es auf bestimmte Endpunkte ( `my_endpoint1` ,) zu beschränken `my_endpoint2` .

```kusto
[
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint1.documents.azure.com",
    "CanCall": true
  },
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint2.documents.azure.com",
    "CanCall": true
  }
]
```

Das folgende Beispiel zeigt einen Befehl zum Ändern von Legenden Legenden Richtlinien für `cosmosdb` *callouttype* .

```kusto
.alter cluster policy callout @'[{"CalloutType": "cosmosdb", "CalloutUriRegex": "\\.documents\\.azure\\.com", "CanCall": true}]'
```

## <a name="examples"></a>Beispiele

### <a name="query-cosmos-db"></a>Abfrage Cosmos DB

Im folgenden Beispiel wird das *cosmosdb_sql_request* -Plug-in verwendet, um mithilfe der SQL-API eine SQL-Abfrage zum Abrufen von Daten aus Cosmos DB abzurufen.

```kusto
evaluate cosmosdb_sql_request(
  'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
  'SELECT * from c')
```

### <a name="query-cosmos-db-with-parameters"></a>Abfrage Cosmos DB mit Parametern

Im folgenden Beispiel werden SQL-Abfrage Parameter verwendet und die Daten aus einer alternativen Region abgefragt. Weitere Informationen finden Sie unter [`preferredLocations`](/azure/cosmos-db/tutorial-global-distribution-sql-api?tabs=dotnetv2%2Capi-async#preferred-locations).

```kusto
evaluate cosmosdb_sql_request(
    'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
    "SELECT c.id, c.lastName, @param0 as Column0 FROM c WHERE c.dob >= '1970-01-01T00:00:00Z'",
    dynamic({'@param0': datetime(2019-04-16 16:47:26.7423305)}),
    dynamic({'preferredLocations': ['East US']}))
| where lastName == 'Smith'
```

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
