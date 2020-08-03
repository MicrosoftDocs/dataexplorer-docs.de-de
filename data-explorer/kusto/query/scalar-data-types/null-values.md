---
title: 'NULL-Werte: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden NULL-Werte in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c493431fcfa22ad0419659a5b6e036205f3bf299
ms.sourcegitcommit: 194453a8eb11c3ccb54c473e887c84cb8e91b939
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87473971"
---
# <a name="null-values"></a>NULL-Werte

Alle skalaren Datentypen in Kusto haben einen speziellen Wert, der einen fehlenden Wert darstellt.
Dieser Wert wird als **NULL-Wert**oder einfach **null**bezeichnet.

## <a name="null-literals"></a>NULL-Literale

Der NULL-Wert eines skalaren Typs `T` wird in der Abfragesprache durch das NULL-Literalzeichen dargestellt `T(null)` .
Folglich gibt Folgendes eine einzelne Zeile mit einer vollständigen Nullen zurück:

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> Beachten Sie, dass der `string` Typ derzeit keine NULL-Werte unterstützt.

## <a name="comparing-null-to-something"></a>Vergleichen von NULL mit etwas

Der NULL-Wert entspricht keinem anderen Wert des Datentyps, einschließlich selbst. (Das heißt, `null == null` ist false.) Verwenden Sie die [IsNull ()](../isnullfunction.md) -Funktion und die [IsNotNull ()](../isnotnullfunction.md) -Funktion, um zu bestimmen, ob ein Wert der NULL-Wert ist.

## <a name="binary-operations-on-null"></a>Binäre Vorgänge bei Null

Im allgemeinen verhält sich NULL in einer "Kurzform" um binäre Operatoren. eine binäre Operation zwischen einem NULL-Wert und einem anderen Wert (einschließlich eines anderen NULL-Werts) erzeugt einen NULL-Wert.

## <a name="data-ingestion-and-null-values"></a>Datenerfassung und NULL-Werte

Bei den meisten Datentypen erzeugt ein fehlender Wert in der Datenquelle einen NULL-Wert in der entsprechenden Tabellenzelle. Eine Ausnahme von ist eine Spalte vom Typ `string` und eine CSV-ähnliche Erfassung, bei der ein fehlender Wert eine leere Zeichenfolge erzeugt.
Wenn beispielsweise Folgendes vorhanden ist: 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

Führen Sie anschließend Folgendes durch:

|a     |b     |IsNull (a)|IsEmpty (a)|Straume (a)|IsNull (b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|False    |true      |0        |true     |
|&nbsp;|&nbsp;|False    |False     |1        |true     |
|a     |1     |False    |False     |1        |False    |

::: zone pivot="azuredataexplorer"

* Wenn Sie die obige Abfrage in Kusto. Explorer ausführen, werden alle `true` Werte als angezeigt `1` , und alle `false` Werte werden als angezeigt `0` .

::: zone-end

* Kusto bietet keine Möglichkeit, die Spalte einer Tabelle auf NULL-Werte zu beschränken (d. h., es gibt keine Entsprechung zur SQL- `NOT NULL` Einschränkung).
