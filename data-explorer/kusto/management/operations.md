---
title: Betriebsverwaltung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Betriebsverwaltung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 01f30a8d391948d5466ef76b2951d55aa6084e15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520688"
---
# <a name="operations-management"></a>Operations Management

## <a name="show-operations"></a>.show-Vorgänge

`.show``operations` Befehl gibt eine Tabelle mit allen ausgeführten und abgeschlossenen Verwaltungsvorgängen zurück, die in den letzten zwei Wochen ausgeführt wurden (dies ist derzeit die Konfiguration des Aufbewahrungszeitraums).

**Syntax**

|||
|---|---| 
|`.show` `operations`              |Gibt alle Vorgänge zurück, die der Cluster verarbeitet hat oder verarbeitet 
|`.show``operations` *OperationId*|Gibt den Vorgangsstatus für eine bestimmte ID zurück 
|`.show``operations` `,` *OperationId2* `,` *OperationId1* OperationId1 OperationId2 ...) `(`|Gibt den Status von Vorgängen für bestimmte IDs zurück

**Ergebnisse**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|Id |String |Operation Identifier.
|Vorgang |String |Admin-Befehlsalias 
|NodeId |String |Wenn der Befehl eine Remoteausführung hat (z.B. DataIngestPull) - NodeId enthält die ID des ausrichtenden Remoteknotens 
|StartedOn |Datetime |Datum/Uhrzeit (in UTC), wenn der Vorgang gestartet wurde 
|LastUpdatedOn |Datetime |Datum/Uhrzeit (in UTC), wenn der Vorgang zuletzt aktualisiert wurde (kann entweder ein Schritt innerhalb des Vorgangs oder ein Abschlussschritt sein) 
|Duration |Datetime |TimeSpan zwischen LastUpdateOn und StartedOn 
|State |String |Befehlsstatus: kann Werte von "InProgress", "Completed" oder "Failed" haben 
|Status |String |Zusätzliche Hilfezeichenfolge, die entweder Fehler bei fehlgeschlagenen Vorgängen enthält 
 
**Beispiel**
 
|Id |Vorgang |Knoten-ID |Started On |Zuletzt aktualisiert |Duration |State |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto.Azure.Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Abgeschlossen | 
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen | 
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress | 
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Fehler |Die Auflistung wurde geändert; Enumerationsvorgang wird möglicherweise nicht ausgeführt. 

## <a name="show-operation-details"></a>.Show-Vorgangsdetails

Vorgänge können (optional) ihre Ergebnisse beibehalten, und diese können abgerufen `.show` `operation` werden, wenn der Vorgang mit dem`details` 

**Hinweise:**

* Nicht alle Steuerbefehle behalten ihre Ergebnisse bei, und diejenigen, die dies normalerweise `async` tun, tun dies standardmäßig nur bei asynchronen Ausführungen (mit dem Schlüsselwort). Bitte durchsuchen Sie die Dokumentation nach dem jeweiligen Befehl und überprüfen Sie, ob dies der Zuspruch vorliegt (siehe z. B. [Datenexport](data-export/index.md)). 
* Das Ausgabeschema `.show` `operations` `details` des Befehls ist dasselbe Schema, das von der synchronen Ausführung des Befehls zurückgegeben wird. 
* `.show` `operation` Der `details` Befehl kann erst aufgerufen werden, nachdem der Vorgang erfolgreich abgeschlossen wurde. Verwenden Sie den [Befehl "Betriebsbereite anzeigen ",](#show-operations)um den Status der Operation zu überprüfen, bevor Sie diesen Befehl aufrufen. 

**Syntax**

`.show``operation` *OperationId*`details`

**Ergebnisse**

Das Ergebnis ist je nach Vorgangstyp unterschiedlich und entspricht dem Schema des Vorgangsergebnisses, wenn es synchron ausgeführt wird. 

**Beispiele**

Die *OperationId* in diesem Beispiel ist eine, die von einer asynchronen Ausführung eines der [Datenexportbefehle](../management/data-export/index.md) zurückgegeben wird.

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```
Der async-Exportbefehl hat die folgende Vorgangs-ID zurückgegeben:

|OperationId|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

Diese Vorgangs-ID kann wie folgt verwendet werden, wenn der Befehl abgeschlossen ist, um die exportierten Blobs abzufragen: 

```
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**Ergebnisse**

|`Path`|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|