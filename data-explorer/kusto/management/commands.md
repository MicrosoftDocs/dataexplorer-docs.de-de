---
title: Befehlsverwaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Befehlsverwaltung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5685833431529af22aa4d8778d121f5a4dec16d1
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744302"
---
# <a name="commands-management"></a>Befehlsverwaltung

## <a name="show-commands"></a>.show Befehle 

`.show``commands` Befehl gibt eine Tabelle mit Admin-Befehlen zurück, die einen endgültigen Status erreicht haben. Diese Befehle können 30 Tage lang abgefragt werden.

Die Tabelle "Befehle" enthält zwei Spalten mit Ressourcenverbrauchsdetails für jeden abgeschlossenen Befehl:
* TotalCpu: Die gesamte CPU-Taktzeit (Benutzermodus + Kernelmodus), die von diesem Befehl verbraucht wird.
* ResourceUtilization: Ein Objekt, das alle Ressourcennutzungsinformationen enthält, die sich auf diesen Befehl beziehen (einschließlich der TotalCpu).

Der nachverfolgte Ressourcenverbrauch umfasst Datenaktualisierungen sowie alle Abfragen, die dem aktuellen Admin-Befehl zugeordnet sind.
Derzeit werden nur einige der Admin-Befehle von der Tabelle Commands abgedeckt (.ingest, .set, .append, .set-or-replace, .set-or-append), und nach und nach werden in Zukunft weitere Befehle hinzugefügt.


* Ein [Datenbankadministrator oder Datenbankmonitor](../management/access-control/role-based-authorization.md) kann jeden Befehl sehen, der in seiner Datenbank aufgerufen wurde.
* Andere Benutzer können nur Befehle sehen, die von ihnen aufgerufen wurden.

**Syntax**

`.show` `commands`
 
**Beispiel**
 
|ClientActivityId |CommandType |Text |Datenbank |StartedOn |LastUpdatedOn |Duration |State |RootActivityId |Benutzer |FailureReason |Application |Prinzipal |TotalCpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |ExtentsMerge   |.merge async Operationen ...    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Abgeschlossen  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD app id=5ba8cec2-9a70-e92c98cad651  |   |Kusto.Azure.DM.Svc |aadapp=5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |• "ScannedExtentsStatistics": "MinDataScannedTime": null, "MaxDataScannedTime": null, "CacheStatistics": "Memory": "Misses": 2, "Hits": 20 , "Disk": " Misses": 2, "Hits": 0 , "MemoryPeak": 159620640, "TotalCpu": "00:00:03.5781250" 
|Ke. RunCommand;710e08ca-2cd3-4d2d-b7bd-2738d335aa50 |DataIngestPull |.ingest in MyTableName ...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |Fehler |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |Die Socketverbindung wurde verworfen.   |Kusto.Explorer |aaduser=...    |00:00:00   |" "ScannedExtentsStatistics": "MinDataScannedTime": null, "MaxDataScannedTime": null , "CacheStatistics": " Memory": " Misses": 0, Hits": 0, "Disk": " Misses": 0, "Hits": 0 , "MemoryPeak": 0, "TotalCpu": "00:00:00". 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |ExtentsRebuild |.merge async Operationen ...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Abgeschlossen  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD app id=5ba8cec2-9a70-e92c98cad651  |   |Kusto.Azure.DM.Svc |aadapp=5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |• "ScannedExtentsStatistics": "MinDataScannedTime": null, "MaxDataScannedTime": null, "CacheStatistics": "Memory": "Misses": 0, "Hits": 1 , "Disk": " Misses": 0, "Hits": 0 , "MemoryPeak": 88828560, "TotalCpu": "00:00:00.8906250". 

**Beispiel: Extrahieren bestimmter Daten aus der Spalte ResourceUtilization**

Der Zugriff auf eine der Eigenschaften in der Spalte ResourceUtilization erfolgt durch Aufrufen ResourcesUtilization.xxx (wobei xxx der Eigenschaftsname ist).
Beachten Sie, dass die ResourceUtilization eine dynamische Spalte ist, und daher sollte man sie, um mit ihren Werten zu arbeiten, zuerst in einen bestimmten Datentyp konvertieren (mit einer Konvertierungsfunktion wie: tolong, toint, totimespan, ...).  

Beispiel:

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|StartedOn |MemoryPeak |TotalCpu |Text
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500  | .append Server_Boots \| <bootStartsSourceTable = SessionStarts; ...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750  | .set-or-append WebUsage \| <Datenbank('CuratedDB'). WebUsage_v2 | Zusammenfassen... | Projekt...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000  | .set-or-append AtlasClusterEventStats mit(..) <\| Atlas_Temp(datetime(2017-09-28 12:13:28.7621737),datetime(2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>Untersuchen von Leistungsproblemen

`.show``commands` kann verwendet werden, um Leistungsprobleme zu untersuchen, da sie es ermöglichen, die von den einzelnen Steuerelementbefehlen verbrauchten Ressourcen anzuzeigen.
Die folgenden Beispiele sind einfache und nützliche Abfragen für solche Untersuchungen.

### <a name="querying-the-totalcpu-column"></a>Abfragen der TotalCpu-Spalte

Top 10 CPU verbrauchende Abfragen am letzten Tag:

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

Alle Abfragen in den letzten 10 Stunden, die ihre TotalCpu die 3 Minuten überschritten hat:

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

Alle Abfragen in den letzten 24 Stunden, deren TotalCpu über 5 Minuten lag, gruppiert nach Benutzer und Prinzipal:

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

Zeitdiagramm: Durchschnittliche CPU vs 95th Perzentil vs Max CPU :

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="querying-the-memorypeak"></a>Abfragen des MemoryPeak

Die 10 wichtigsten Abfragen des letzten Tages mit den höchsten MemoryPeak-Werten:

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

Zeitdiagramm von Average MemoryPeak vs 95th Perzentil vs Max MemoryPeak:

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
