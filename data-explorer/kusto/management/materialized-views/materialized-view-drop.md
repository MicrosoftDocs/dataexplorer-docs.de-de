---
title: Materialisierte Sichten Drop-Azure Daten-Explorer
description: In diesem Artikel wird der Befehl Drop materialisierte View in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: yifats
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 312b8dbd15f9ee570d1693f7bdbb77b9988d8207
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320609"
---
# <a name="drop-materialized-view"></a>.drop materialized-view 

Löscht eine materialisierte Sicht.

Erfordert [Datenbankadministrator](../access-control/role-based-authorization.md) -oder materialisierte View admin-Berechtigungen.

## <a name="syntax"></a>Syntax

`.drop``materialized-view` *Materializedviewname*

## <a name="properties"></a>Eigenschaften

| Eigenschaft | type| Beschreibung |
|----------------|-------|-----|
| Materializedviewname| String| Der Name der materialisierten Sicht.|

## <a name="returns"></a>Rückgabe

Der Befehl gibt die übrigen materialisierten Sichten in der Datenbank zurück. Dies ist die Ausgabe des Befehls " [materialisierte Sicht anzeigen](materialized-view-show-commands.md#show-materialized-view) ".

## <a name="example"></a>Beispiel

```kusto
.drop materialized-view ViewName
```

## <a name="output"></a>Output

|Ausgabeparameter |type |Beschreibung
|---|---|---|
|Name  |String |Der Name der materialisierten Sicht.
|SourceTable|String|Die Quell Tabelle der materialisierten Sicht.
|Abfrage|String|Die materialisierte Ansichts Abfrage.
|Materializedto|datetime|Der maximale materialisierte ingestion_time ()-Zeitstempel in der Quell Tabelle. Weitere Informationen finden Sie unter [Funktionsweise materialisierter Sichten](materialized-view-overview.md#how-materialized-views-work).
|Lastrun|datetime |Die letzte Zeit, zu der die Materialisierung ausgeführt wurde.
|Lastrauunresult|String|Ergebnis der letzten Testlauf. Gibt `Completed` für erfolgreiche Ausführungen zurück, andernfalls `Failed` .
|Ishealthy|bool|`True` , wenn die Ansicht als fehlerfrei angesehen wird; `False` andernfalls. Die Ansicht wird als fehlerfrei angesehen, wenn Sie bis zur letzten Stunde erfolgreich materialisiert wurde ( `MaterializedTo` ist größer als `ago(1h)` ).
|isEnabled|bool|`True` Wenn die Ansicht aktiviert ist (siehe [deaktivieren oder Aktivieren der materialisierten Ansicht](materialized-view-enable-disable.md)).
|Ordner|Zeichenfolge|Der materialisierte Ansichts Ordner.
|DocString|Zeichenfolge|Die materialisierte Ansicht der doc-Zeichenfolge.
|Autoupdateschema|bool|Gibt an, ob die Ansicht für automatische Updates aktiviert ist.
|Effectivedatetime|datetime|Der Gültigkeits Zeitpunkt der Sicht, der während der Erstellung bestimmt wird (siehe [`.create materialized-view`](materialized-view-create.md#create-materialized-view) ).
