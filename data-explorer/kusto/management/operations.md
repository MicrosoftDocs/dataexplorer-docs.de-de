---
title: 'Operations Management: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Operations Management in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e4e373cd694de989b2bbef8058aaf1c8b3ca3025
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616472"
---
# <a name="operations-management"></a>Operations Management

## <a name="show-operations"></a>. Show-Vorgänge

`.show``operations` der Befehl gibt eine Tabelle mit allen administrativen Vorgängen zurück, die ausgeführt werden, und die in den letzten zwei Wochen ausgeführt wurden (zurzeit die Konfiguration der Beibehaltungs Dauer).

**Syntax**

|||
|---|---| 
|`.show` `operations`              |Gibt alle Vorgänge zurück, die vom Cluster verarbeitet oder verarbeitet werden. 
|`.show` `operations` *OperationId*|Gibt den Vorgangs Status für eine bestimmte ID zurück. 
|`.show``operations` `,` *OperationId1* OperationId1 OperationId2`,` ...) *OperationId2* `(`|Gibt den Vorgangs Status für bestimmte IDs zurück.

**Ergebnisse**
 
|Output-Parameter |type |BESCHREIBUNG 
|---|---|---
|Id |String |Vorgangs Bezeichner.
|Vorgang |String |Administrator befehlsalias 
|NodeId |String |Wenn der Befehl eine Remote Ausführung (z. b. dataingestpull) aufweist, enthält NodeId die ID des ausgeführten Remote Knotens. 
|Startedon |Datetime |Datum/Uhrzeit (in UTC), zu der der Vorgang gestartet wurde 
|Lastupdatedon |Datetime |Datum/Uhrzeit (in UTC), als der Vorgang zuletzt aktualisiert wurde (kann entweder ein Schritt innerhalb des Vorgangs oder ein Abschluss Schritt sein) 
|Duration |Datetime |TimeSpan zwischen lastupdateon und startedon 
|State |String |Befehls Zustand: kann Werte von "InProgress", "abgeschlossen" oder "failed" aufweisen. 
|Status |String |Zusätzliche Hilfe Zeichenfolge, die entweder Fehler für fehlgeschlagene Vorgänge enthält 
 
**Beispiel**
 
|Id |Vorgang |Knoten-ID |Gestartet am |Zuletzt aktualisiert am |Duration |State |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395gehörlos |Schemashow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen | 
|841-Datei-4-076a-4cba-9300-4836da0d9c75 |Dataingestpull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Abgeschlossen | 
|e198c519-5263-4629-a158-8d68f7a1022f |Operationsshow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen | 
|a9f287a1-f3e6-4154-ad18-b86438da0929 |Extentsdrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress | 
|9edb3ecc-f 4b4-4738-87e1-648eed2bd998 |Dataingestpull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Fehler |Die Sammlung wurde geändert. Der Enumerationsvorgang kann nicht ausgeführt werden. 

## <a name="show-operation-details"></a>. Vorgangs Details anzeigen

Vorgänge können (optional) ihre Ergebnisse beibehalten, und diese können abgerufen werden, wenn der Vorgang mit dem `.show` `operation``details` 

**Hinweise:**

* Nicht alle Steuerelement Befehle behalten ihre Ergebnisse bei, und diese werden in der Regel standardmäßig für asynchrone Ausführungen (mit dem `async` -Schlüsselwort) verwendet. Suchen Sie in der Dokumentation nach dem jeweiligen Befehl, und überprüfen Sie, ob dies der Fall ist (z. b. [Datenexport](data-export/index.md)). 
* Das Ausgabe Schema des `.show` `operations` `details` Befehls ist das gleiche Schema, das von der synchronen Ausführung des Befehls zurückgegeben wird. 
* `.show` `operation` Der `details` Befehl kann nur aufgerufen werden, nachdem der Vorgang erfolgreich abgeschlossen wurde. Verwenden Sie den [Befehl Show Operations (Vorgänge anzeigen](#show-operations)), um den Status des Vorgangs vor dem Aufrufen dieses Befehls zu überprüfen. 

**Syntax**

`.show` `operation` *OperationId* `details`

**Ergebnisse**

Das Ergebnis unterscheidet sich je nach Typ des Vorgangs und entspricht dem Schema des Vorgangs Ergebnisses, wenn es synchron ausgeführt wird. 

**Beispiele**

Bei der *operationId* in diesem Beispiel handelt es sich um einen Wert, der von einer asynchronen Ausführung eines der [Datenexport](../management/data-export/index.md) Befehle zurückgegeben wird.

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

Diese Vorgangs-ID kann verwendet werden, wenn der Befehl zum Abfragen der exportierten blobvorgänge wie folgt abgeschlossen wurde: 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**Ergebnisse**

|`Path`|Numrecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|