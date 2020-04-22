---
title: Nullwerte - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Nullwerte in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c3a48fdfca855a1ff2f848d4ed97d8162e97b931
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765960"
---
# <a name="null-values"></a>NULL-Werte

Alle skalaren Datentypen in Kusto haben einen speziellen Wert, der einen fehlenden Wert darstellt.
Dieser Wert wird als **NULL-Wert**oder einfach **null**bezeichnet.

## <a name="null-literals"></a>Nullliterale

Der NULL-Wert eines skalaren Typs `T` wird in der `T(null)`Abfragesprache durch das null-Literal dargestellt.
Daher gibt Folgendes eine einzelne Zeile voller NULLs zurück:

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> Bitte beachten Sie, dass der `string` Typ derzeit keine NULL-Werte unterstützt.

## <a name="comparing-null-to-something"></a>Vergleich von NULL mit etwas

Der NULL-Wert ist nicht gleich mit einem anderen Wert des Datentyps, einschließlich sich selbst. (Das heißt, `null == null` ist falsch.) Um zu bestimmen, ob ein Wert der NULL-Wert ist, verwenden Sie die [isnull()-Funktion](../isnullfunction.md) und die [isnotnull()-Funktion.](../isnotnullfunction.md)

## <a name="binary-operations-on-null"></a>Binäre Operationen auf NULL

Im Allgemeinen verhält sich null "klebrig" um binäre Operatoren herum. Ein binärer Vorgang zwischen einem NULL-Wert und einem anderen Wert (einschließlich eines anderen NULL-Werts) erzeugt einen NULL-Wert.

## <a name="data-ingestion-and-null-values"></a>Datenaufnahme und NULL-Werte

Bei den meisten Datentypen erzeugt ein fehlender Wert in der Datenquelle einen NULL-Wert in der entsprechenden Tabellenzelle. Eine Ausnahme bilden Spalten `string` vom Typ und CSV-ähnliche Aufnahme, bei der ein fehlender Wert eine leere Zeichenfolge erzeugt.
Wenn wir also z. B.: 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

Führen Sie dann folgende Schritte aus:

|a     |b     |isnull(a)|isempty(a)|strlen(a)|isnull(b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* Wenn Sie die obige Abfrage in `true` Kusto.Explorer ausführen, `1`werden `false` alle Werte als `0`displated und alle Werte werden als angezeigt.

::: zone-end

* Kusto bietet keine Möglichkeit, die Spalte einer Tabelle davon abzuhalten, NULL-Werte zu haben (d. h., es gibt keine Entsprechung zur `NOT NULL` SQL-Einschränkung).
