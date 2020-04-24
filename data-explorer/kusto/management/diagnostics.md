---
title: Diagnoseinformationen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Diagnoseinformationen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ae8efdf99b7ed91285e90defed7568d2a440240d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521266"
---
# <a name="diagnostic-information"></a>Diagnoseinformationen

Die nächsten Befehle können verwendet werden, um Systemdiagnoseinformationen anzuzeigen.

## <a name="show-cluster"></a>.show-Cluster

```kusto
.show cluster
```

Gibt einen Satz mit einem Datensatz pro Knoten zurück, der derzeit im Cluster aktiv ist.  

**Ergebnisse**

|Ausgabespalte |type |BESCHREIBUNG 
|---|---|---|
|NodeId|String|Identifiziert den Knoten (dies ist die Azure RoleId des Knotens, wenn der Cluster in Azure bereitgestellt wird). |
|Adresse|String |Der interne Endpunkt, der vom Cluster für die Kommunikation zwischen Knoten verwendet wird. 
|name |String |Ein interner Name für den Knoten (einschließlich des Computernamens, des Prozessnamens und der Prozess-ID). 
|StartTime |Datetime |Das genaue Datum/die uhrgenaue Uhrzeit (in UTC), mit dem die aktuelle Kusto-Instanziierung im Knoten gestartet wurde. Dies kann verwendet werden, um zu erkennen, ob der Knoten (oder Kusto, der auf dem Knoten ausgeführt wird) vor kurzem neu gestartet wurde. 
|IsAdmin |Boolean |Gibt an, ob dieser Knoten derzeit der "Leader" des Clusters ist. 
|MachineTotalMemory  |Int64 |Die RAM-Menge, über die der Knoten verfügt. 
|MachineAvailableMemory  |Int64 |Die Menge an RAM, die derzeit auf dem Knoten "verfügbar" ist. 
|ProcessorCount  |Int32 |Die Anzahl der Prozessoren auf dem Knoten. 
|UmweltBeschreibung  |Zeichenfolge |Zusätzliche Informationen zur Umgebung des Knotens (z. B. Upgrade/Fault Domains), serialisiert als JSON. 

**Beispiel**

```kusto
.show cluster
```

NodeId|Adresse|name|StartTime|IsAdmin|MachineTotalMemory|MachineAvailableMemory|ProcessorCount|UmweltBeschreibung
---|---|---|---|---|---|---|---|---
Kusto.Azure.Svc_IN_1|net.tcp://100.112.150.30:23107/|Kusto.Azure.Svc_IN_4/RD000D3AB1E9BD/WaWorkerHost/3820|2016-01-15 02:00:22.6522152|True|274877435904|247797796864|16|"UpdateDomain":0, "FaultDomain":0"
Kusto.Azure.Svc_IN_3|net.tcp://100.112.154.34:23107/|Kusto.Azure.Svc_IN_3/RD000D3AB1E062/WaWorkerHost/2760|2016-01-15 05:52:52.1434683|False|274877435904|258740346880|16|"UpdateDomain":1, "FaultDomain":1
Kusto.Azure.Svc_IN_2|net.tcp://100.112.128.40:23107/|Kusto.Azure.Svc_IN_2/RD000D3AB1E054/WaWorkerHost/3776|2016-01-15 07:17:18.0699790|False|274877435904|244232339456|16|"UpdateDomain":2, "FaultDomain":2
Kusto.Azure.Svc_IN_0|net.tcp://100.112.138.15:23107/|Kusto.Azure.Svc_IN_0/RD000D3AB0D6C6/WaWorkerHost/3208|2016-01-15 09:46:36.9865016|False|274877435904|238414581760|16|"UpdateDomain":3, "FaultDomain":3


## <a name="show-diagnostics"></a>.show Diagnose

```kusto
.show diagnostics
```

Gibt Informationen zum Kusto-Clusterstatus zurück.
 
**Rückgabe**

|Ausgabeparameter |type |BESCHREIBUNG|
|-----------------|-----|-----------| 
|IsHealthy|Boolean|Gibt an, ob der Cluster als fehlerfrei gilt oder nicht.
|IsScaleOutRequired|Boolean|Gibt an, ob der Cluster vergrößert werden soll (weitere Computerknoten hinzufügen). 
|MaschinenTotal|Int64|Die Anzahl der Computer im Cluster.
|MaschinenOffline|Int64|Die Anzahl der Computer, die derzeit offline sind (nicht reagieren).
|NodeLastRestartedOn|Datetime|Das letzte Mal, dass einer der Knoten im Cluster neu gestartet wurde.
|AdminLastElectedOn|Datetime|Das letzte Mal, dass der Besitz der Clusteradministratorrolle geändert wurde.
|MemoryLoadFactor|Double|Die Datenmenge, die der Cluster im Verhältnis zu seiner Kapazität (die 100,0 beträgt)
|ExtentsTotal|Int64|Die Gesamtanzahl der Datenerweiterungen, die der Cluster derzeit in allen Datenbanken und Tabellen hat.
|Reserved|Int64|
|Reserved|Int64|
|InstanzenTargetBasedOnDataCapacity|Int64| Die Anzahl der Instanzen, die erforderlich sind, um den ClusterDataCapacityFactor unter 80 zu bringen (gültig nur, wenn alle Computer gleich dimensioniert sind).
|TotalOriginalDataSize|Int64|Gesamtgröße der ursprünglich aufgenommenen Daten
|TotalExtentSize|Int64|Gesamtgröße der gespeicherten Daten (nach Komprimierung und Indizierung)
|IngestionsLoadFactor|Double|Der Auslastungsprozentsatz der Clustererfassungskapazität (kann mit dem Befehl .show capacity angezeigt werden)
|IngestionsInProgress|Int64|Die Anzahl der derzeit durchgeführten Aufnahmevorgänge.
|IngestionsSuccessRate|Double|Der Prozentsatz der Aufnahmevorgänge, die in den letzten 10 Minuten erfolgreich abgeschlossen wurden.
|MergesInProgress|Int64|Die Anzahl der Erweiterungen, die derzeit ausgeführt werden.
|BuildVersion|String|Die Im Cluster bereitgestellte Kusto-Softwareversion.
|BuildTime|Datetime|Die Buildzeit dieser Kusto-Softwareversion.
|ClusterDataCapacityFactor|Double|Der Prozentsatz der Clusterdatenkapazitätsauslastung. Er wird als SUMME (Extent Size Data) / SUM (SSD Cache Size) berechnet.
|IsDataWarmingErforderlich|Boolean|Intern: Gibt an, ob die Erwärmungsabfragen des Clusters ausgeführt werden sollen (um Daten in den lokalen SSD-Cache zu bringen). 
|DatawarmingLastRunon|Datetime|Das letzte Mal, dass .warm-Daten auf dem Cluster ausgeführt wurden
|MergesSuccessRate|Double|Der Prozentsatz der Zusammenführungsvorgänge, die in den letzten 10 Minuten erfolgreich abgeschlossen wurden.
|NotHealthyReason|String|Zeichenfolge, die den Grund für die Fehlerwartin des Clusters angibt, ist nicht fehlerfrei. 
|IsAttentionRequired|Boolean|Ob Cluster die Aufmerksamkeit des Operation-Teams erfordert
|AchtungRequiredReason|String|Zeichenfolge, die den Grund für den Cluster angibt, der Aufmerksamkeit erfordert
|ProductVersion|String|String mit Produktinformationen (Zweig, Version, etc.)
|FailedIngestOperations|Int64|Anzahl der fehlgeschlagenen Aufnahmevorgänge nach 10 Minuten
|FailedMergeOperations|Int64|Anzahl fehlgeschlagener Zusammenführungsvorgänge nach 1 Stunde
|MaxExtentsInSingleTable|Int64|Maximale Anzahl von Ausdehnungen in der Tabelle (TableWithMaxExtents)
|TableWithMaxExtents|String|Tabelle mit der maximalen Anzahl von Ausdehnungen (MaxExtentsInSingleTable)
|WarmExtentSize|Double|Gesamtgröße der Ausdehnungen im hot Cache
|NumberOfDatabases|Int32|Anzahl der Datenbanken im Cluster

## <a name="show-capacity"></a>.show Kapazität 

```kusto
.show capacity
```

Gibt eine Berechnung für eine geschätzte Clusterkapazität für jede Ressource zurück. 
 
**Ergebnisse**

|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|Resource |String |Der Name der Ressource. 
|Gesamt |Int64 |Die Menge der verfügbaren Gesamtressourcen des Typs 'Ressource' (z. B. Menge der gleichzeitigen Einnahme) 
|Consumed |Int64 |Die Anzahl der Ressourcen des Typs 'Ressource', die jetzt verbraucht werden 
|Verbleibend |Int64 |Die Menge der verbleibenden Ressourcen vom Typ 'Ressource' 
 
**Beispiel**

|Resource |Gesamt |Consumed |Verbleibend 
|---|---|---|---
|Einnahme |576 |1 |575 

## <a name="show-operations"></a>.show-Vorgänge 

Gibt eine Tabelle mit allen administrativen Vorgängen seit der Wahl des neuen Admin-Knotens zurück. 

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