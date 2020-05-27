---
title: 'Befehls Verwaltung: Azure Daten-Explorer'
description: In diesem Artikel wird die Verwaltung von Befehlen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e6a31e2c79ae658cceecfdad0ce307e2522bb55b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011413"
---
# <a name="commands-management"></a>Befehls Verwaltung

## <a name="show-commands"></a>. Show-Befehle 

`.show commands`gibt eine Tabelle mit admin-Befehlen zurück, die einen Endzustand erreicht haben. Diese Befehle stehen für 30 Tage zur Abfrage zur Verfügung.

Die Befehls Tabelle verfügt über zwei Spalten mit Details zu den Ressourcenverbrauch aller abgeschlossenen Befehle.

* Totalcpu: die CPU-Gesamtzeit (Benutzermodus + Kernel Modus), die von diesem Befehl genutzt wird.
* Resourceutilization: enthält alle Ressourcen Verwendungs Informationen, die mit diesem Befehl verknüpft sind, einschließlich totalcpu.

Der Ressourcenverbrauch, der nachverfolgt wird, umfasst Datenaktualisierungen und alle dem aktuellen Administrator Befehl zugeordneten Abfragen.
Zurzeit werden nur einige der Admin-Befehle von der Befehls Tabelle abgedeckt ( `.ingest` , `.set` , `.append` , `.set-or-replace` , `.set-or-append` ). Nach und nach werden der Befehls Tabelle weitere Befehle hinzugefügt.

* Ein [Datenbankadministrator oder Daten Bank Monitor](../management/access-control/role-based-authorization.md) kann jeden Befehl sehen, der in der Datenbank aufgerufen wurde.
* Andere Benutzer können nur Befehle sehen, die von Ihnen aufgerufen wurden.

**Syntax**

`.show` `commands`
 
**Beispiel**
 
|Clientactivityid |CommandType |Text |Datenbank |Startedon |Lastupdatedon |Duration |State |RootActivityId |Benutzer |FailureReason |Anwendung |Prinzipal |Totalcpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |Extentsmerge   |. asynchrone Vorgänge werden zusammengeführt...    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Abgeschlossen  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |Aad-APP-ID = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM. svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |{"Scannetdextentsstatistics": {"mindatascannedtime": NULL, "maxdatascannedtime": NULL}, "cachestatistics": {memory ": {" Fehler ": 2," Treffer ": 20}," Disk ": {" Fehler ": 2," Treffer ": 0}}," memorypeak ": 159620640," totalcpu ":" 00:00:03.5781250 "} 
|KE. RunCommand; 710e08ca-2cd3-4 D2D-b7bd-2738d335aa50    |Dataingestpull |. Erfassung in myTableName...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |Failed |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |Die Socketverbindung wurde verworfen.   |Kusto.Explorer |aaduser =...    |00:00:00   |{"Scannetdextentsstatistics": {"mindatascannedtime": NULL, "maxdatascannedtime": NULL}, "cachestatistics": {"Memory": {"fehl Treffer": 0, Treffer ": 0}", "Datenträger": {"Fehler": 0, "Treffer": 0}}, "memorypeak": 0, "totalcpu": "00:00:00"} 
|KD2RunCommand; 97db47e6-93e2-4306-8b7d-670F 2c3307ff |Extentsrebuild |. asynchrone Vorgänge werden zusammengeführt...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Abgeschlossen  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |Aad-APP-ID = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM. svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |{"Scannetdextentsstatistics": {"mindatascannedtime": NULL, "maxdatascannedtime": NULL}, "cachestatistics": {memory ": {" fehl Treffer ": 0," Treffer ": 1}," Disk ": {" Fehler ": 0," Treffer ": 0}}," memorypeak ": 88828560," totalcpu ":" 00:00:00.8906250 "} 

**Beispiel: Extrahieren bestimmter Daten aus der resourceutilization-Spalte**

Der Zugriff auf eine der Eigenschaften in der resourceutilization-Spalte erfolgt durch Aufrufen von ResourcesUtilization.xxx (wobei xxx der Eigenschaftsname ist).
> [!NOTE] 
> `ResourceUtilization`ist eine dynamische Spalte. Um mit ihren Werten zu arbeiten, sollten Sie Sie zuerst in einen bestimmten Datentyp konvertieren. Verwenden Sie eine Konvertierungs Funktion `tolong` , `toint` z `totimespan` . b.,,.  

Beispiel:

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|Startedon |Memorypeak |Totalcpu |Text
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500 |. Append Server_Boots <\| Let bootstartssourcetable = Sessionstarts;...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750 |. Set-oder-Append webusage <\| Datenbank (' Cursor-DB '). WebUsage_v2 | zusammenfassen... | Projekt...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000 |. Set-oder-Append atlasclustereventstats with (...) <\| Atlas_Temp (DateTime (2017-09-28 12:13:28.7621737), DateTime (2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>Untersuchen von Leistungsproblemen

`.show``commands`kann verwendet werden, um Leistungsprobleme zu untersuchen, da Sie die von den einzelnen Steuerungs Befehlen genutzten Ressourcen anzeigen.

Die folgenden Beispiele sind einfache und nützliche Abfragen für diese Untersuchungen.

### <a name="query-the-totalcpu-column"></a>Abfragen der totalcpu-Spalte

Die 10 wichtigsten Abfragen mit CPU-Auslastung am letzten Tag.

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

Alle Abfragen in den letzten 10 Stunden, deren totalcpu 3 Minuten vergangen ist.

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

Alle Abfragen in den letzten 24 Stunden, deren totalcpu 5 Minuten beträgt, gruppiert nach Benutzer und Prinzipal.

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

Zeitdiagramm: durchschnittliche CPU-Leistung im Vergleich zum 95. Perzentil im Vergleich zur maximalen CPU.

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="query-the-memorypeak"></a>Abfragen von memorypeak

Die ersten 10 Abfragen am letzten Tag mit den höchsten memorypeak-Werten.

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

Zeitdiagramm von Average memorymaximum im Vergleich zum 95. Perzentil im Vergleich zu Max memorypeak.

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
