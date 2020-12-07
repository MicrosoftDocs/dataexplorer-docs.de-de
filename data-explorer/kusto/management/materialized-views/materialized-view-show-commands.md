---
title: 'materialisierte Sicht Befehle anzeigen: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Befehle materialisierte Sichten in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 0ee41d8aba05eb9b5bf3bc6db3206524fdb5ec0d
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321300"
---
# <a name="show-materialized-views-commands"></a>. Anzeigen von Befehlen für materialisierte Sichten

Mit den folgenden Befehlen werden Informationen über [materialisierte Sichten](materialized-view-overview.md)angezeigt.

## <a name="show-materialized-view"></a>. Sicht materialisierte Ansicht

Zeigt Informationen zur Definition der materialisierten Sicht und deren aktuellen Status an.

### <a name="syntax"></a>Syntax

`.show``materialized-view` *Materializedviewname*

`.show` `materialized-views`

### <a name="properties"></a>Eigenschaften

|Eigenschaft|type|Beschreibung
|----------------|-------|---|
|Materializedviewname|String|Der Name der materialisierten Sicht.|

### <a name="example"></a>Beispiel

```kusto
.show materialized-view ViewName
```

### <a name="output"></a>Output

|Ausgabeparameter |type |Beschreibung
|---|---|---
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

## <a name="show-materialized-view-schema"></a>. materialisiertes Ansichts Schema anzeigen

Gibt das Schema der materialisierten Sicht in CSL/JSON zurück.

### <a name="syntax"></a>Syntax

`.show``materialized-view` *Materializedviewname*`cslschema`

`.show``materialized-view` *Materializedviewname* `schema` `as``json`

`.show``materialized-view` *Materializedviewname* `schema` `as``csl`

### <a name="output-parameters"></a>Ausgabeparameter

| Ausgabeparameter | type   | BESCHREIBUNG                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | Der Name der materialisierten Sicht.                        |
| Schema           | Zeichenfolge | Das materialisierte CSL-Schema der Sicht                          |
| DatabaseName     | String | Die Datenbank, zu der die materialisierte Sicht gehört.       |
| Ordner           | String | Ordner der materialisierten Ansicht                                |
| DocString        | String | DocString für materialisierte Sicht                             |

## <a name="show-materialized-view-extents"></a>. Anzeigen von Blöcke mit materialisierter Ansicht

Gibt die Blöcke im *materialisierten* Teil der materialisierten Ansicht zurück. Eine Definition des *materialisierten* Teils finden Sie unter [Funktionsweise materialisierter Sichten](materialized-view-overview.md#how-materialized-views-work).

Dieser Befehl stellt die gleichen Details wie der Befehl [Tabellen Blöcke anzeigen](../show-extents.md#table-level) bereit.

### <a name="syntax"></a>Syntax

`.show``materialized-view` *Materializedviewname* `extents` [ `hot` ]
 
## <a name="show-materialized-view-failures"></a>. Anzeigen von Fehlern in materialisierter Ansicht

Gibt Fehler zurück, die als Teil des Materialisierungs Prozesses der materialisierten Sicht aufgetreten sind.

### <a name="syntax"></a>Syntax

`.show``materialized-view` *Materializedviewname*`failures`

### <a name="properties"></a>Eigenschaften

|Eigenschaft|type|Beschreibung
|----------------|-------|---|
|Materializedviewname|String|Der Name der materialisierten Sicht.|

### <a name="output"></a>Output

|Ausgabeparameter |type |Beschreibung
|---|---|---
|Name  |Timestamp |Fehler Zeitstempel.
|OperationId  |String |Die Vorgangs-ID der fehlgeschlagenen Ausführung.
|Name|String|Der materialisierte Ansichts Name.
|Lastsuccess Run|datetime|Der Zeitstempel der letzten Ausführung, die erfolgreich abgeschlossen wurde.
|Failurekind|String|Typ des Fehlers.
|Details|String|Fehlerdetails.

