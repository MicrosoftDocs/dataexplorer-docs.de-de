---
title: Kql über TDS-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die kql über TDS in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 2c4443c0a9301dbc6bb3e65392163da0cc237f74
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617883"
---
# <a name="kql-over-tds"></a>KQL über TDS

Kusto ermöglicht TDS-Endpunkten das Ausführen von Abfragen, die in der nativen [kql](../../query/index.md) -Abfragesprache erstellt wurden. Diese Fähigkeit ermöglicht eine reibungslosere Migration zu Kusto. Beispielsweise können Sie SSIS-Aufträge erstellen, um Kusto mit einer kql-Abfrage abzufragen.

## <a name="executing-kusto-stored-functions"></a>Ausführen von gespeicherten Kusto-Funktionen

Kusto ermöglicht das Ausführen [gespeicherter Funktionen](../../query/schema-entities/stored-functions.md) , wie das Aufrufen von gespeicherten SQL-Prozeduren.

Beispielsweise ist die gespeicherte Funktion MyFunction:

|Name |Parameter|Body|Ordner|DocString
|---|---|---|---|---
|MyFunction |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|MyFolder|Demo Funktion mit Parameter||

kann wie folgt aufgerufen werden:

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

> [! Hinweis:] ruft gespeicherte Funktionen mit einem expliziten Schema `kusto`mit dem Namen auf, um zwischengespeicherten Kusto-Funktionen und gespeicherten gespeicherten SQL-System Prozeduren zu unterscheiden.

Sie können auch von T-SQL gespeicherte Funktionen mit Kusto (wie tabellarische SQL-Funktionen) abrufen:

```sql
SELECT * FROM kusto.MyFunction(10)
```

Erstellen Sie optimierte kql-Abfragen, und Kapseln Sie diese in gespeicherten Funktionen, sodass der T-SQL-Abfrage Code minimal ist.

## <a name="executing-kql-query"></a>Ausführen der kql-Abfrage

Die gespeicherte Prozedur `sp_execute_kql` führt [kql](../../query/index.md) -Abfragen (einschließlich parametrisierter Abfragen) aus. Diese Vorgehensweise ähnelt SQL Server `sp_executesql`.

Der erste Parameter von `sp_execute_kql` ist die kql-Abfrage. Sie können zusätzliche Parameter einführen, und Sie fungieren wie [Abfrage Parameter](../../query/queryparametersstatement.md).

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

> [! Hinweis:] Es ist nicht erforderlich, Parameter zu deklarieren, wenn Sie über TDS aufrufen, da Parametertypen über das Protokoll festgelegt werden.
