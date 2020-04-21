---
title: KQL über TDS - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt KQL über TDS in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 364db99141c528f4a822692124ebb870d7315bca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523306"
---
# <a name="kql-over-tds"></a>KQL über TDS

Kusto ermöglicht die Nutzung von TDS-Endpunkten für die Ausführung von Abfragen, die in der systemeigenen [KQL-Abfragesprache](../../query/index.md) erstellt wurden. Diese Funktionalität bietet eine reibungslosere Wanderung in Richtung Kusto. Beispielsweise ist es möglich, Einen SSIS-Auftrag zu erstellen, um Kusto mit der KQL-Abfrage abzufragen.

## <a name="executing-kusto-stored-functions"></a>Ausführen von gespeicherten Kusto-Funktionen

Kusto ermöglicht das Ausführen [gespeicherter Funktionen](../../query/schema-entities/stored-functions.md) ähnlich wie beim Aufrufen gespeicherter SQL-Prozeduren.

Zum Beispiel die gespeicherte Funktion MyFunction:

|name |Parameter|Body|Ordner|DocString
|---|---|---|---|---
|Myfunction |(myLimit: lang)| "StormEvents&#124; myLimit einschränken"|Myfolder|Demofunktion mit Parameter||

kann wie folgt bezeichnet werden:

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("kusto.MyFunction", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

Bitte beachten Sie, dass gespeicherte `kusto`Funktionen mit dem expliziten Schema mit dem Namen aufgerufen werden sollten, um zwischen gespeicherten Kusto-Funktionen und emulierten gespeicherten SQL-Systemprozeduren zu unterscheiden.

Kusto gespeicherte Funktionen können auch von T-SQL aufgerufen werden, genau wie SQL-Tabellarische Funktionen:

```sql
SELECT * FROM kusto.MyFunction(10)
```

Es wird empfohlen, optimierte KQL-Abfragen zu erstellen und sie in gespeicherten Funktionen zu kapseln, was den T-SQL-Abfragecode minimal macht.

## <a name="executing-kql-query"></a>Ausführen der KQL-Abfrage

Ähnlich wie `sp_executesql`beim SQL-Server `sp_execute_kql` führte Kusto gespeicherte Prozeduren zum Ausführen von [KQL-Abfragen](../../query/index.md) ein, einschließlich parametrisierter Abfragen.

Der erste Parameter `sp_execute_kql` von ist die KQL-Abfrage. Zusätzliche Parameter können eingeführt werden und sie wirken wie [Abfrageparameter](../../query/queryparametersstatement.md).

Beispiel:

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("sp_execute_kql", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var query = new SqlParameter("@kql_query", SqlDbType.NVarChar);
      command.Parameters.Add(query);
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      query.Value = "StormEvents | limit myLimit";
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

Bitte beachten Sie, dass beim Aufruf über TDS keine Parameter deklariert werden müssen, da Parametertypen über das Protokoll festgelegt werden.