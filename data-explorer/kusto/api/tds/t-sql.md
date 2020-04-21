---
title: T-SQL - Azure-Daten-Explorer | Microsoft Docs
description: In diesem Artikel wird T-SQL in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d262d8b7587296c02a2a31d850919af0931bcde6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523408"
---
# <a name="t-sql"></a>T-SQL

Der Kusto-Dienst kann T-SQL-Abfragen mit einigen Spracheinschränkungen interpretieren und ausführen.
Während die [Kusto-Abfragesprache](../../query/index.md) die bevorzugte Sprache für Kusto ist, ist eine solche Unterstützung nützlich für bestehende Tools, die nicht einfach konvertiert werden können, um die bevorzugte Abfragesprache zu verwenden, und für die gelegentliche Verwendung von Kusto durch Personen, die mit SQL vertraut sind.

> [!NOTE]
> Kusto unterstützt einen DDL-Befehl auf diese Weise `SELECT` nicht, nur T-SQL-Anweisungen werden unterstützt. Weitere Informationen zu den wichtigsten Unterschieden zwischen SQL Server und Kusto in Bezug auf T-SQL finden Sie unter [SQL-bekannte Probleme.](./sqlknownissues.md)

## <a name="querying-kusto-from-kustoexplorer-with-t-sql"></a>Abfragen von Kusto aus Kusto.Explorer mit T-SQL

Das Kusto.Explorer-Tool unterstützt das Senden von T-SQL-Abfragen an Kusto.
Um Kusto.Explorer anzuweisen, eine Abfrage in diesem Modus auszuführen, stellen Sie der Abfrage eine leere T-SQL-Kommentarzeile vor. Beispiel:

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>Von T-SQL zur Kusto-Abfragesprache

Kusto unterstützt das Übersetzen von T-SQL-Abfragen in die Kusto-Abfragesprache. Dies kann z. B. von SQL-Vertrauten verwendet werden, die die Kusto-Abfragesprache besser verstehen möchten. Um die entsprechende Kusto-Abfragesprache in `SELECT` eine T-SQL-Anweisung zurückzubekommen, fügen Sie `EXPLAIN` einfach vor der Abfrage hinzu.

Beispiel: Die folgende T-SQL-Abfrage:

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

Erzeugt diese Ausgabe:

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```