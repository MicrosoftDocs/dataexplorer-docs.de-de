---
title: Diagnoseinformationen-Azure Daten-Explorer
description: Dieser Artikel beschreibt die Diagnoseinformationen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6c9bfed37ea54e541e55106a505471f04fe94e99
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610558"
---
# <a name="diagnostic-information"></a>Diagnoseinformationen

Diese Befehle können verwendet werden, um Systemdiagnoseinformationen anzuzeigen.

* [. Cluster anzeigen](#show-cluster)
* [. Diagnose anzeigen](#show-diagnostics)
* [. Anzeigen der Kapazität](#show-capacity)
* [. Show-Vorgänge](#show-operations)

## <a name="show-cluster"></a>. Cluster anzeigen

```kusto
.show cluster
```

Gibt eine Menge zurück, die einen Datensatz für jeden Knoten enthält, der derzeit im Cluster aktiv ist.  

**Ergebnisse** 

|Ausgabe Spalte |type |Beschreibung
|---|---|---|
|NodeId|String|Identifiziert den Knoten. Bei der Knoten-ID handelt es sich um die Azure-RoleID des Knotens, wenn der Cluster in Azure bereitgestellt wird.
|Adresse|String |Der interne Endpunkt, der vom Cluster für die Kommunikation zwischen Knoten verwendet wird.
|Name |String |Ein interner Name für den Knoten. Der Name enthält den Computernamen, den Prozessnamen und die Prozess-ID.
|StartTime |Datetime |Das Datum und die Uhrzeit (in UTC), zu der die aktuelle Kusto-Instanziierung im Knoten gestartet wurde. Dieser Wert kann verwendet werden, um zu ermitteln, ob der Knoten (oder Kusto, der auf dem Knoten ausgeführt wird) kürzlich neu gestartet wurde.
|IsAdmin |Boolean |Wenn dieser Knoten derzeit die "Führungskraft" des Clusters ist 
|Machinetotalmemory  |Int64 |Der RAM-Wert des Knotens.
|Machineavailablememory  |Int64 |Der RAM-Betrag, der derzeit für die Verwendung auf dem Knoten verfügbar ist.
|ProcessorCount  |Int32 |Die Anzahl der Prozessoren auf dem Knoten.
|Umgebungs Beschreibung  |Zeichenfolge |Zusätzliche Informationen zur Umgebung des Knotens, die als JSON serialisiert ist. Beispielsweise als Upgrade/fehlerdomänen

**Beispiel**

```kusto
.show cluster
```

NodeId|Adresse|Name|StartTime|IsAdmin|Machinetotalmemory|Machineavailablememory|ProcessorCount|Umgebungs Beschreibung
---|---|---|---|---|---|---|---|---
Kusto. Azure. Svc_IN_1|NET. TCP://100.112.150.30:23107/|Kusto. Azure. Svc_IN_4/rd000d3ab1e9bd/waworkerhost/3820|2016-01-15 02:00:22.6522152|True|274877435904|247797796864|16|{"Updatedomain": 0, "Fehler Domäne": 0}
Kusto. Azure. Svc_IN_3|NET. TCP://100.112.154.34:23107/|Kusto. Azure. Svc_IN_3/rd000d3ab1e062/waworkerhost/2760|2016-01-15 05:52:52.1434683|False|274877435904|258740346880|16|{"Updatedomain": 1, "Fehler Domäne": 1}
Kusto. Azure. Svc_IN_2|NET. TCP://100.112.128.40:23107/|Kusto. Azure. Svc_IN_2/rd000d3ab1e054/waworkerhost/3776|2016-01-15 07:17:18.0699790|False|274877435904|244232339456|16|{"Updatedomain": 2, "Fehler Domäne": 2}
Kusto. Azure. Svc_IN_0|NET. TCP://100.112.138.15:23107/|Kusto. Azure. Svc_IN_0/rd000d3ab0d6c6/waworkerhost/3208|2016-01-15 09:46:36.9865016|False|274877435904|238414581760|16|{"Updatedomain": 3, "Fehler Domäne": 3}


## <a name="show-diagnostics"></a>. Diagnose anzeigen

```kusto
.show diagnostics
```

Gibt Informationen zum Kusto-Cluster Integritäts Status zurück.
 
**Rückgabe**

|Output-Parameter |type |Beschreibung|
|-----------------|-----|-----------| 
|Ishealthy|Boolean|Wenn der Cluster fehlerfrei ist oder nicht
|Isscaleumquired|Boolean|Wenn die Größe des Clusters durch Hinzufügen von weiteren Computer Knoten erhöht werden soll
|Machinestotal|Int64|Die Anzahl der Computer im Cluster
|Machinesoffline|Int64|Die Anzahl von Computern, die zurzeit offline sind
|Nodelastrestartedon|Datetime|Das letzte Datum/die Uhrzeit, zu der ein Knoten im Cluster neu gestartet wurde
|Adminlastelectedon|Datetime|Der letzte Datums-/Uhrzeit-Besitz der Cluster Administrator Rolle wurde geändert.
|Memoryloadfactor|Double|Die Menge der vom Cluster gehaltenen Daten relativ zur maximalen Kapazität von 100,0
|Extentstotal|Int64|Die Gesamtanzahl der Datenblöcke, die der Cluster derzeit umfasst, über alle Datenbanken und alle Tabellen hinweg
|Reserviert|Int64|
|Reserviert|Int64|
|Instancestargetbasedondatacapacity|Int64|Die Anzahl der Instanzen, die erforderlich sind, um den clusterdatacapacityfactor unter 80 zu verschieben. Dieser Wert ist nur gültig, wenn alle Computer gleich groß sind.
|Totaloriginaldatasize|Int64|Gesamtgröße der ursprünglich erfassten Daten
|Totalextentsize|Int64|Gesamtgröße der gespeicherten Daten nach der Komprimierung und Indizierung
|IngestionsLoadFactor|Double|Der Prozentsatz der verwendeten Cluster Erfassungs Kapazität. Die maximale Kapazität kann mit dem Befehl eingesehen werden. `.show capacity`
|Ingestionsinprogress|Int64|Die Anzahl der Erfassungs Vorgänge, die zurzeit ausgeführt werden.
|Ingestionserfolgreiches Rate|Double|Der Prozentsatz der Erfassungs Vorgänge, die in den letzten 10 Minuten erfolgreich abgeschlossen wurden.
|Mergesinprogress|Int64|Die Anzahl der zurzeit durchgeführten Blöcke zum Zusammenführen von Blöcken.
|BuildVersion|String|Die im Cluster bereitgestellte Kusto-Softwareversion
|Buildtime|Datetime|Das Datum und die Uhrzeit der Buildversion der Kusto-Software.
|ClusterDataCapacityFactor|Double|Der Prozentsatz der verwendeten Cluster Datenkapazität. Der Prozentsatz wird als Summe (Blockgrößen Daten)/Sum (SSD-Cache Größe) berechnet.
|Isdatawarmingrequired|Boolean|Intern: Wenn die Aufwärm Abfragen des Clusters ausgeführt werden sollen, um Daten in den lokalen SSD-Cache zu bringen 
|Datawarminglastrauunon|Datetime|Das letzte Datum/die Uhrzeit, zu der die warmdaten auf dem Cluster ausgeführt wurden.
|Mergessuccess Rate|Double|Der Prozentsatz der Merge-Vorgänge, die in den letzten 10 Minuten erfolgreich abgeschlossen wurden.
|Noderalthyreason|String|Gibt an, warum der Cluster nicht fehlerfrei ist. 
|Isattentionrequired|Boolean|Wenn für den Cluster ein Eingreifen des Betriebsteams erforderlich ist
|Attentionrequirements dreason|String|Gibt den Grund für den Cluster an, der Eingreifen erfordert.
|ProductVersion|String|Gibt Produktinformationen an (Branch, Version usw.).
|Failedingestoperations|Int64|Anzahl der fehlgeschlagenen Erfassungs Vorgänge in den letzten 10 Minuten
|Failedmergeoperations|Int64|Anzahl fehlgeschlagener Zusammenarbeits Vorgänge in der letzten 1 Stunde
|Maxextentsinsingletable|Int64|Maximale Anzahl von Blöcken in der Tabelle (tablewithmaxextents)
|Tablewithmaxextents|String|Tabelle mit der maximalen Anzahl von Blöcken (maxextentsinsingletable)
|Warmextentsize|Double|Gesamtgröße der Blöcke im aktiven Cache
|Anzahl von Datenbanken|Int32|Anzahl der Datenbanken im Cluster

## <a name="show-capacity"></a>. Anzeigen der Kapazität

```kusto
.show capacity
```

Gibt die Ergebnisse einer Berechnung für eine geschätzte Cluster Kapazität für jede Ressource zurück.
 
**Ergebnisse**

|Output-Parameter |type |BESCHREIBUNG 
|---|---|---
|Resource |String |Der Name der Ressource 
|Gesamt |Int64 |Die Gesamtmenge der Ressourcen vom Typ "Resource", die verfügbar sind. Beispielsweise die Anzahl der gleichzeitigen Ingestionen.
|Consumed |Int64 |Momentan verbrauchte Ressourcen Menge des Typs "Ressource"
|Verbleibend |Int64 |Die Menge der verbleibenden Ressourcen vom Typ "Resource".
 
**Beispiel**

|Resource |Gesamt |Consumed |Verbleibend
|---|---|---|---
|Ingestionen |576 |1 |575

## <a name="show-operations"></a>. Show-Vorgänge

Dieser Befehl gibt eine Tabelle zurück, die alle administrativen Vorgänge enthält, seit der neue Administrator Knoten gewählt wurde.

|Syntax Option |Beschreibung|
|---|---| 
|`.show` `operations`              |Gibt alle Vorgänge zurück, die vom Cluster verarbeitet oder verarbeitet wurden.
|`.show``operations` *OperationId*|Gibt den Vorgangs Status für eine bestimmte ID zurück.
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ...)|Gibt den Vorgangs Status für bestimmte IDs zurück.

**Ergebnisse**
 
|Output-Parameter |type |BESCHREIBUNG
|---|---|---
|id |String |Vorgangs Bezeichner
|Vorgang |String |Administrator befehlsalias
|NodeId |String |, Wenn der Befehl etwas Remote ausgeführt wird, z. b. dataingestpull. Die Knoten-ID enthält die ID des Remote Knotens, auf dem ausgeführt wird.
|Startedon |Datetime |Datum/Uhrzeit (in UTC), zu der der Vorgang gestartet wurde 
|Lastupdatedon |Datetime |Datum/Uhrzeit (in UTC), als der Vorgang zuletzt aktualisiert wurde. Der Vorgang kann entweder ein Schritt innerhalb des Vorgangs oder ein Abschluss Schritt sein.
|Duration |Datetime |Zeitspanne zwischen "lastupdateon" und "startedon"
|State |String |Befehls Zustand mit den Werten "InProgress", "abgeschlossen" oder "failed"
|Status |String |Zusätzliche Hilfe Zeichenfolge, die die Fehler für fehlgeschlagene Vorgänge enthält
 
**Beispiel**
 
|id |Vorgang |Knoten-ID |Gestartet am |Zuletzt aktualisiert am |Duration |State |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395gehörlos |Schemashow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen | 
|841-Datei-4-076a-4cba-9300-4836da0d9c75 |Dataingestpull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Abgeschlossen | 
|e198c519-5263-4629-a158-8d68f7a1022f |Operationsshow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Abgeschlossen |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |Extentsdrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress |
|9edb3ecc-f 4b4-4738-87e1-648eed2bd998 |Dataingestpull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Fehler |Die Sammlung wurde geändert. Enumerationsvorgang kann nicht ausgeführt werden. |
