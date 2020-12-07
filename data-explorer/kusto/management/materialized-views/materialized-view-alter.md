---
title: Alter materialisierte View-Azure Daten-Explorer
description: In diesem Artikel wird beschrieben, wie materialisierte Sichten in Azure Daten-Explorer geändert werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 03d047d89abc3de3c80414564ec101f309bf69b0
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320569"
---
# <a name="alter-materialized-view"></a>.alter materialized-view

Das Ändern der [materialisierten Sicht](materialized-view-overview.md) kann verwendet werden, um die Abfrage einer materialisierten Sicht zu ändern, während die vorhandenen Daten in der Ansicht beibehalten werden.

Erfordert [Datenbankadministrator](../access-control/role-based-authorization.md) Berechtigungen oder einen Administrator der materialisierten Sicht.

> [!WARNING]
> Seien Sie beim Ändern einer materialisierten Sicht besonders vorsichtig. Eine falsche Verwendung kann zu Datenverlusten führen.

## <a name="syntax"></a>Syntax

`.alter` `materialized-view`  
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]  
*ViewName* `on table` *SourceTableName*  
`{`  
    &nbsp;&nbsp;&nbsp;&nbsp;*Such*  
`}`

## <a name="arguments"></a>Argumente

|Argument|type|Beschreibung
|----------------|-------|---|
|Ansichtsname|String|Der materialisierte Sichtname.|
|SourceTableName|String|Der Name der Quell Tabelle, in der die Sicht definiert ist.|
|Abfrage|String|Die materialisierte Ansichts Abfrage.|

## <a name="properties"></a>Eigenschaften

`dimensionTables`Ist die einzige unterstützte Eigenschaft im MATERIALIZED-VIEW Alter-Befehl. Diese Eigenschaft sollte verwendet werden, wenn die Abfrage auf Dimensions Tabellen verweist. Weitere Informationen finden Sie unter dem [`.create materialized-view`](materialized-view-create.md) Befehl.

## <a name="use-cases"></a>Anwendungsfälle

* Fügen Sie der Sicht Aggregationen hinzu, indem Sie beispielsweise Aggregationen hinzufügen `avg` `T | summarize count(), min(Value) by Id` , indem Sie die Ansichts Abfrage in ändern `T | summarize count(), min(Value), avg(Value) by Id` .
* Andere Änderungs Operatoren als der Zusammenfassungs Operator. Tor-Beispiel: Filtern Sie einige Datensätze, indem  `T | summarize arg_max(Timestamp, *) by User` Sie in ändern `T | where User != 'someone' | summarize arg_max(Timestamp, *) by User` .
* Ändern Sie die Abfrage aufgrund einer Änderung in der Quell Tabelle ohne Änderungen an der Abfrage. Tor-Beispiel, angenommen, eine Ansicht von `T | summarize arg_max(Timestamp, *) by Id` , die nicht auf festgelegt ist `autoUpdateSchema` (siehe [`.create materialized-view`](materialized-view-create.md) Befehl). Wenn eine Spalte der Quell Tabelle der Sicht hinzugefügt oder daraus entfernt wird, wird die Ansicht automatisch deaktiviert. Führen Sie den Alter-Befehl mit genau derselben Abfrage aus, um das Schema der materialisierten Ansicht so zu ändern, dass es dem neuen Tabellen Schema entspricht. Die Sicht muss nach der Änderung weiterhin explizit aktiviert werden, indem der Befehl " [materialisierte Sicht aktivieren](materialized-view-enable-disable.md) " verwendet wird.

## <a name="alter-materialized-view-limitations"></a>Einschränkungen für die Alter materialisierte Sicht

* **Änderungen werden nicht unterstützt:**
    * Das Ändern des Spalten Typs wird nicht unterstützt.
    * Umbenannte Spalten werden nicht unterstützt. Wenn Sie z. b. eine Sicht von in ändern, `T | summarize count() by Id` `T | summarize Count=count() by Id` wird die Spalte gelöscht `count_` und eine neue Spalte erstellt `Count` , die anfänglich nur Nullen enthält.
    * Änderungen an der materialisierten Sicht Group by-Ausdrücke werden nicht unterstützt.

* **Auswirkungen auf vorhandene Daten:**
    * Das Ändern der materialisierten Sicht hat keine Auswirkungen auf vorhandene Daten.
    * Neue Spalten erhalten NULL-Werte für alle vorhandenen Datensätze, bis die Datensätze, die nach dem Alter-Befehl erfasst werden, die NULL-Werte ändern.
        * Wenn z. b. eine Sicht von `T | summarize count() by bin(Timestamp, 1d)` in geändert wird `T | summarize count(), sum(Value) by bin(Timestamp, 1d)` , und für eine bestimmte, `Timestamp=T` für die bereits Datensätze verarbeitet wurden, bevor die Sicht geändert wird, `sum` enthält die Spalte partielle Daten. Diese Ansicht enthält nur Datensätze, die nach der Alter-Ausführung verarbeitet wurden.
    * Durch das Hinzufügen von Filtern zur Abfrage werden Datensätze, die bereits materialisiert wurden, nicht geändert. Der Filter gilt nur für neu erfasste Datensätze.
