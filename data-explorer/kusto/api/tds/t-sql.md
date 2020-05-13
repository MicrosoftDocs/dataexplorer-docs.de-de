---
title: T-SQL-Azure-Daten-Explorer
description: In diesem Artikel wird T-SQL in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1115414358a1025d4931484b81d6eda76109e6cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373527"
---
# <a name="t-sql-support"></a>T-SQL-Unterstützung

Die [Kusto-Abfragesprache (kql)](../../query/index.md) ist die bevorzugte Abfragesprache.
Die T-SQL-Unterstützung ist jedoch nützlich für Tools, die nicht problemlos zur Verwendung von kql konvertiert werden können.  
Die T-SQL-Unterstützung ist auch nützlich für die gelegentliche Verwendung durch Benutzer, die mit SQL vertraut sind.

Mithilfe von Kusto können T-SQL-Abfragen mit einigen Spracheinschränkungen interpretiert und ausgeführt werden.

> [!NOTE]
> Kusto unterstützt keine DDL-Befehle. Nur T-SQL- `select` Anweisungen werden unterstützt. Weitere Informationen zu den Haupt unterschieden in Bezug auf T-SQL finden Sie unter [bekannte SQL-Probleme](./sqlknownissues.md).

## <a name="querying-from-kustoexplorer-with-t-sql"></a>Abfragen von "Kusto. Explorer" mit T-SQL

Das Tool Kusto. Explorer unterstützt T-SQL-Abfragen zu Kusto.
Um Kusto. Explorer anzuweisen, eine Abfrage auszuführen, starten Sie die Abfrage mit einer leeren T-SQL-Kommentarzeile ( `--` ). Zum Beispiel:

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>Von T-SQL zu Kusto-Abfragesprache

Kusto unterstützt die Übersetzung von T-SQL-Abfragen in die Kusto-Abfragesprache (kql). Diese Übersetzung kann Benutzern helfen, die mit SQL vertraut sind, um kql besser zu verstehen.
Um die entsprechende kql aus einer T-SQL-Anweisung zurückzugeben `select` , fügen Sie `explain` vor der Abfrage hinzu.

Beispielsweise die folgende T-SQL-Abfrage:

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

erzeugt die folgende Ausgabe:

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```
