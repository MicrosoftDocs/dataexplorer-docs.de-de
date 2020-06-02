---
title: 'Operations Management: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Operations Management in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac3d44fadf614606bc63e6a9aa3b8318419d0c70
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294388"
---
# <a name="operations-management"></a>Operations Management

## <a name="show-operations"></a>. Show-Vorgänge 

`.show`der `operations` Befehl gibt eine Tabelle mit allen administrativen Vorgängen zurück, die ausgeführt werden, und die in den letzten zwei Wochen ausgeführt wurden (zurzeit die Konfiguration der Beibehaltungs Dauer).

**Syntax**

|||
|---|---| 
|`.show` `operations`              |Gibt alle Vorgänge zurück, die vom Cluster verarbeitet werden oder die vom Cluster verarbeitet wurden.
|`.show` `operations` *OperationId*|Gibt den Vorgangs Status für eine bestimmte ID zurück. 
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ...)|Gibt den Vorgangs Status für bestimmte IDs zurück.

**Ergebnisse**
 
|Output-Parameter |type |BESCHREIBUNG
|---|---|---
|id |Zeichenfolge |Vorgangs Bezeichner
|Vorgang |Zeichenfolge |Administrator befehlsalias
|NodeId |Zeichenfolge |Wenn der Befehl eine Remote Ausführung aufweist (z. b. dataingestpull), enthält NodeId die ID des ausgeführten Remote Knotens.
|Startedon |Datetime |Datum/Uhrzeit (in UTC), zu der der Vorgang gestartet wurde
|Lastupdatedon |Datetime |Datum/Uhrzeit (in UTC), als der Vorgang zuletzt aktualisiert wurde (kann entweder ein Schritt innerhalb des Vorgangs oder ein Abschluss Schritt sein)
|Duration |Datetime |TimeSpan zwischen lastupdateon und startedon
|Zustand |Zeichenfolge |Befehlsstatus: die Werte "InProgress", "abgeschlossen" oder "failed" sind möglich.
|Status |Zeichenfolge |Zusätzliche Hilfe Zeichenfolge, die Fehler fehlgeschlagener Vorgänge enthält
 
**Beispiel**
 
|Id |Vorgang |Knoten-ID |Gestartet am |Zuletzt aktualisiert am |Duration |Zustand |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395gehörlos |Schemashow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen |
|841-Datei-4-076a-4cba-9300-4836da0d9c75 |Dataingestpull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Abgeschlossen |
|e198c519-5263-4629-a158-8d68f7a1022f |Operationsshow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |Extentsdrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress |
|9edb3ecc-f 4b4-4738-87e1-648eed2bd998 |Dataingestpull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Failed |Die Sammlung wurde geändert. Der Enumerationsvorgang kann nicht ausgeführt werden.

## <a name="show-operation-details"></a>. Vorgangs Details anzeigen

Vorgänge können (optional) ihre Ergebnisse beibehalten, und die Ergebnisse können nach Abschluss des Vorgangs mithilfe von abgerufen werden `.show` `operation` `details` .

> [!NOTE]
> Nicht alle Steuerelement Befehle speichern Ihre Ergebnisse. Die Befehle, die dies ausführen, werden in der Regel standardmäßig für asynchrone Ausführungen verwendet, wobei das- `async` Schlüsselwort verwendet wird. Lesen Sie die Dokumentation für den jeweiligen Befehl, und prüfen Sie, ob dies der Fall ist. Beispielsweise siehe [Datenexport](data-export/index.md)).
> Das Ausgabe Schema des `.show` `operations` `details` Befehls ist das gleiche Schema, das von der synchronen Ausführung des Befehls zurückgegeben wird.
> Der `.show` `operation` `details` Befehl kann nur aufgerufen werden, nachdem der Vorgang erfolgreich abgeschlossen wurde. Verwenden Sie den [Befehl Show Operations (Vorgänge anzeigen](#show-operations)), um den Status des Vorgangs zu überprüfen, bevor Sie diesen Befehl ausführen.

**Syntax**

`.show` `operation` *OperationId* `details`

**Ergebnisse**

Das Ergebnis unterscheidet sich je nach Typ des Vorgangs und entspricht dem Schema des Vorgangs Ergebnisses, wenn es synchron ausgeführt wird.

**Beispiele**

Die *operationId* im Beispiel gibt eine asynchrone Ausführung eines der [Datenexport](../management/data-export/index.md) Befehle zurück.

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 
```

Der Async-Export Befehl hat die folgende Vorgangs-ID zurückgegeben:

|OperationId|
|---|
|56e51622-eb49-4d1a-B896-06a03178efcd|

Diese Vorgangs-ID kann verwendet werden, wenn der Befehl zum Abfragen der exportierten blobvorgänge abgeschlossen wurde. 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

|Pfad|Numrecords |
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|
