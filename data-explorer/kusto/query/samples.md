---
title: Beispiele für Abfragen in Azure Data Explorer und Azure Monitor
description: In diesem Artikel werden gängige Abfragen und Beispiele beschrieben, in denen die Kusto-Abfragesprache für Azure Data Explorer und Azure Monitor verwendet wird.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 866df577fa039e8b92c31753197cf4a4fa03f139
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783524"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Beispiele für Abfragen für Azure Data Explorer und Azure Monitor

::: zone pivot="azuredataexplorer"

Dieser Artikel identifiziert Anforderungen an gängige Abfragen in Azure Data Explorer und erläutert, wie Sie die Kusto-Abfragesprache verwenden können, um sie zu erfüllen.

## <a name="display-a-column-chart"></a>Anzeigen eines Säulendiagramms

So projizieren Sie zwei oder mehr Spalten und verwenden die Spalten dann als x-Achse und y-Achse eines Diagramms

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* Die erste Spalte bildet die x-Achse. Sie kann vom Typ „Numerisch, „Datum/Uhrzeit“ oder „Zeichenfolge“ sein. 
* Verwenden Sie `where`, `summarize` und `top`, um das angezeigte Datenvolumen einzuschränken.
* Sortieren Sie die Ergebnisse, um die Reihenfolge der x-Achse zu definieren.

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="Screenshot eines Säulendiagramms mit zehn farbigen Spalten, die die entsprechenden Werte von 10 Standorten darstellen.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Abrufen von Sitzungen aus Start- und Stop-Ereignissen

In einem Ereignisprotokoll markieren einige Ereignisse den Anfang oder das Ende einer erweiterten Aktivität oder Sitzung. 

|Name|City|SessionID|Timestamp|
|---|---|---|---|
|Start|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Start|Manchester|4267667|2015-12-09T10:14:02.23|
|Beenden|London|2817330|2015-12-09T10:23:43.18|
|Abbrechen|Manchester|4267667|2015-12-09T10:27:26.29|
|Beenden|Manchester|4267667|2015-12-09T10:28:31.72|

Jedes Ereignis besitzt eine Sitzungs-ID (`SessionId`). Die Herausforderung besteht darin, den Start- und Ende-Ereignissen eine Sitzungs-ID zuzuordnen.

Beispiel:

```kusto
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on SessionId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

So ordnen Sie Start- und Ende-Ereignissen eine Sitzungs-ID zu

1. Verwenden Sie [let](./letstatement.md), um eine Projektion der Tabelle zu benennen, die vor dem Beginn der Verknüpfung (join) so weit wie möglich reduziert wurde.
1. Verwenden Sie [project](./projectoperator.md), um die Namen der Zeitstempel so zu ändern, dass die Start- und Endzeiten in den Ergebnissen angezeigt werden. `project` wählt außerdem die anderen Spalten aus, die in den Ergebnissen angezeigt werden sollen. 
1. Verwenden Sie [join](./joinoperator.md), um die Start- und Ende-Einträge für dieselbe Aktivität zuzuordnen. Für jede Aktivität wird eine Zeile erstellt. 
1. Verwenden Sie `project` erneut, um eine Spalte hinzuzufügen, in der die Dauer der Aktivität angezeigt werden soll.

So sieht die Ausgabe aus:

|City|SessionID|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>Abrufen von Sitzungen ohne Verwendung einer Sitzungs-ID

Angenommen, die Start- und Ende-Ereignisse besitzen keine Sitzungs-ID, die wir zuordnen können. Wir verfügen aber über die IP-Adresse des Clients, in dem die Sitzung stattgefunden hat. Vorausgesetzt, dass jede Clientadresse immer nur eine Sitzung gleichzeitig durchführt, dann können wir jedes Start-Ereignis dem nächsten Ende-Ereignis derselben IP-Adresse zuordnen:

Beispiel:

```kusto
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize arg_min(duration, *) by bin(StartTime,1s), ClientIp
```

`join` ordnet jede Startzeit allen Endzeiten von derselben Client-IP-Adresse zu. Der Beispielcode:

- Entfernt Übereinstimmungen mit früheren Endzeiten.
- Gruppiert nach Startzeit und IP-Adresse, um eine Gruppe für jede Sitzung zu erhalten. 
- Stellt eine `bin`-Funktion für den `StartTime`-Parameter bereit. Wenn Sie diesen Schritt nicht ausführen, verwendet Kusto automatisch einstündige Intervalle, in denen einige Startzeiten falschen Endzeiten zugeordnet werden.

`arg_min` sucht die Zeile mit der kürzestsen Dauer in jeder Gruppe, und der `*`-Parameter durchläuft alle anderen Spalten. 

Das Argument versieht jeden Spaltennamen mit dem Präfix `min_`. 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="Screenshot einer Tabelle, in der die Ergebnisse aufgelistet sind, mit Spalten für Startzeit, Client-IP, Dauer, Stadt und frühestes Ende für jede Kombination aus Client und Startzeit."::: 

Fügen Sie Code hinzu, um die Dauerwerte in Intervallen von angemessener Größe zu zählen. In diesem Beispiel teilen Sie wegen der Bevorzugung eines Balkendiagramms durch `1s`, um die Zeiträume in Zahlen umzuwandeln:

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="Screenshot eines Säulendiagramms, das die Anzahl der Sitzungen zusammen mit Dauerwerten in angegebenen Bereichen darstellt.":::

### <a name="full-example"></a>Vollständiges Beispiel

```kusto
Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"      
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

## <a name="chart-concurrent-sessions-over-time"></a>Erstellen eines Diagramms für gleichzeitige Sitzungen im Verlauf der Zeit

Angenommen, Sie haben eine Tabelle mit Aktivitäten und deren jeweiligen Start- und Endzeiten. Sie können ein Diagramm anzeigen, das darstellt, wie viele Aktivitäten im Laufe der Zeit gleichzeitig ausgeführt werden.

Hier sehen Sie eine Beispieleingabe namens `X`:

|SessionID | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Für ein Diagramm in einminütigen Intervallen zählen Sie jede laufende Aktivität in jedem einminütigen Intervall.

Hier ein Zwischenergebnis:

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` generiert ein Array von Werten in den angegebenen Abständen:

|SessionID | StartTime | StopTime  | Beispiele|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Statt diese Arrays zu behalten, erweitern Sie sie mithilfe von [mv-expand](./mvexpandoperator.md):

```kusto
X | mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
```

|SessionID | StartTime | StopTime  | Beispiele|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | 10:01:00|
| a | 10:01:33 | 10:06:31 | 10:02:00|
| a | 10:01:33 | 10:06:31 | 10:03:00|
| a | 10:01:33 | 10:06:31 | 10:04:00|
| a | 10:01:33 | 10:06:31 | 10:05:00|
| a | 10:01:33 | 10:06:31 | 10:06:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|

Gruppieren Sie nun die Ergebnisse nach Beispielzeit und Anzahl des Auftretens jeder Aktivität:

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Verwenden Sie `todatetime()`, weil [mv-expand](./mvexpandoperator.md) eine Spalte vom dynamischen Typ ergibt.
* Verwenden Sie `bin()`, weil `summarize` für numerische Werte und Daten immer eine `bin()`-Funktion mit einem Standardintervall anwendet, wenn Sie kein Intervall bereitstellen. 

So sieht die Ausgabe aus:

| count_SessionId | Beispiele|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Sie können ein Balkendiagramm oder ein Zeitdiagramm verwenden, um die Ergebnisse zu rendern.

## <a name="introduce-null-bins-into-summarize"></a>Einführen von NULL-Intervallen in *summarize*

Wenn der `summarize`-Operator auf einen Gruppenschlüssel angewendet wird, der aus einer Datums-/Uhrzeitspalte besteht, fassen Sie diese Werte in Intervallen mit fester Breite zusammen:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

In diesem Beispiel wird eine Tabelle mit einer einzelnen Zeile pro Gruppe von Zeilen in `T` erzeugt, die jeweils in Intervalle von fünf Minuten fallen.

Der Code fügt keine „NULL-Intervalle“ hinzu – Zeilen für Zeit-Intervallwerte zwischen `StartTime` und `StopTime`, für die es keine entsprechende Zeile in `T` gibt. Es ist ratsam, die Tabelle mit diesen Intervallen „aufzufüllen“. Dies können Sie folgendermaßen durchführen:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| summarize Count=count() by bin(Timestamp, 5m)
| where ...
| union ( // 1
  range x from 1 to 1 step 1 // 2
  | mv-expand Timestamp=range(StartTime, StopTime, 5m) to typeof(datetime) // 3
  | extend Count=0 // 4
  )
| summarize Count=sum(Count) by bin(Timestamp, 5m) // 5 
```

Im Folgenden finden Sie eine schrittweise Erläuterung der vorhergehenden Abfrage: 

1. Verwenden Sie den `union`-Operator, um einer Tabelle weitere Zeilen hinzuzufügen. Diese Zeilen werden vom `union`-Ausdruck erzeugt.
1. Der `range`-Operator erzeugt eine Tabelle mit einer einzelnen Zeile und Spalte. Die Tabelle wird ausschließlich dafür verwendet, dass sie von `mv-expand` verarbeitet wird.
1. Der `mv-expand`-Operator für die `range`-Funktion erstellt so viele Zeilen, wie es 5-Minuten-Intervalle zwischen `StartTime` und `EndTime` gibt.
1. Verwenden Sie eine `Count` von `0`.
1. Der `summarize`-Operator gruppiert Intervalle vom ursprünglichen (linken oder äußeren) Argument in `union`. Der Operator fasst auch aus dem inneren Argument darin (die NULL-Intervall-Zeilen) in Intervallen zusammen. Durch diesen Vorgang wird sichergestellt, dass die Ausgabe eine Zeile pro Intervall enthält, deren Wert entweder 0 oder die ursprüngliche Anzahl ist.

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Verbessern der Auswertung Ihrer Daten mithilfe von Kusto mit Machine Learning 

Viele interessante Anwendungsfälle verwenden Machine Learning-Algorithmen und leiten interessante Erkenntnisse aus Telemetriedaten ab. Häufig erfordern diese Algorithmen ein streng strukturiertes Dataset als Eingabe. Die Protokollrohdaten entsprechen in der Regel nicht der erforderlichen Struktur und Größe. 

Beginnen Sie mit der Suche nach Anomalien in der Fehlerrate eines bestimmten Bing-Rückschlussdiensts. Die Tabelle „Logs“ enthält 65 Milliarden Datensätze. Die folgende einfache Abfrage filtert 250.000 Fehler und erstellt dann eine Zeitreihe, der Fehleranzahl, die die Funktion zur Anomalieerkennung [series_decompose_anomalies](series-decompose-anomaliesfunction.md) verwendet. Die Anomalien werden vom Kusto-Dienst erkannt und als rote Punkte im Zeitreihendiagramm hervorgehoben.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Der Dienst hat ein paar Zeitbuckets mit verdächtigen Fehlerraten identifiziert. Verwenden Sie Kusto, um diesen Zeitraum zu vergrößern. Führen Sie dann eine Abfrage aus, die über die `Message`-Spalte aggregiert. Versuchen Sie, die häufigsten Fehler zu finden. 

Die relevanten Teile der gesamten Stapelüberwachung von Message werden herausgeschnitten, damit die Ergebnisse besser auf die Seite passen. 

Sie sehen die erfolgreiche Identifizierung der häufigsten acht Fehler. Das Nächste ist jedoch eine lange Reihe von Fehlern, da die Fehlermeldung mit einer Formatzeichenfolge erstellt wurde, die sich ändernde Daten enthielt:

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|Meldung
|---|---
|7125|Fehler von „ExecuteAlgorithmMethod“ für die Methode „RunCycleFromInterimData“...
|7125|Fehler des Aufrufs von „InferenceHostService“..System.NullReferenceException: Der Objektverweis wurde nicht auf eine Instanz eines Objekts festgelegt...
|7124|Unerwarteter Rückschlusssystemfehler..System.NullReferenceException: Der Objektverweis wurde nicht auf eine Instanz eines Objekts festgelegt... 
|5112|Unerwarteter Rückschlusssystemfehler..System.NullReferenceException: Der Objektverweis wurde nicht auf eine Instanz eines Objekts festgelegt.
|174|Fehler des Aufrufs von „InferenceHostService“..System.ServiceModel.CommunicationException: Fehler beim Schreiben in die Pipe:...
|10|Fehler von „ExecuteAlgorithmMethod“ für die Methode „RunCycleFromInterimData“...
|10|Rückschlusssystemfehler..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|3|Fehler des Aufrufs von „InferenceHostService“..System.ServiceModel.CommunicationException:...
|1|Rückschlusssystemfehler... SocialGraph.BOSS.OperationResponse...AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|Rückschlusssystemfehler... SocialGraph.BOSS.OperationResponse...AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF...

An dieser Stelle hilft die Verwendung des Operators `reduce`. Der Operator hat 63 verschiedene Fehler identifiziert, die am selben Instrumentierungspunkt im Code der Ablaufverfolgung entstanden sind. `reduce` hilft dabei, sich auf weitere sinnvolle Fehlerablaufverfolgungen in diesem Zeitfenster zu konzentrieren.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Anzahl|Muster
|---|---
|7125|Fehler von „ExecuteAlgorithmMethod“ für die Methode „RunCycleFromInterimData“...
|  7125|Fehler des Aufrufs von „InferenceHostService“..System.NullReferenceException: Der Objektverweis wurde nicht auf eine Instanz eines Objekts festgelegt...
|  7124|Unerwarteter Rückschlusssystemfehler..System.NullReferenceException: Der Objektverweis wurde nicht auf eine Instanz eines Objekts festgelegt...
|  5112|Unerwarteter Rückschlusssystemfehler..System.NullReferenceException: Der Objektverweis wurde nicht auf eine Instanz eines Objekts festgelegt...
|  174|Fehler des Aufrufs von „InferenceHostService“..System.ServiceModel.CommunicationException: Fehler beim Schreiben in die Pipe:...
|  63|Rückschlusssystemfehler..Microsoft.Bing.Platform.Inferences.\*: \* schreiben, um in das Objekt BOSS.\* zu schreiben: SocialGraph.BOSS.Reques...
|  10|Fehler von „ExecuteAlgorithmMethod“ für die Methode „RunCycleFromInterimData“...
|  10|Rückschlusssystemfehler..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|  3|Fehler des Aufrufs von „InferenceHostService“..System.ServiceModel.\*: Das Objekt System.ServiceModel.Channels.\*+\* für \*\* ist das \*... bei Syst...

Nun haben Sie einen guten Überblick über die häufigsten Fehler, die zu den erkannten Anomalien beigetragen haben.

Beachten Sie Folgendes, um die Auswirkungen dieser Fehler auf das Beispielsystem zu verstehen: 
* Die Tabelle `Logs` enthält zusätzliche Dimensionsdaten, z. B. `Component` und `Cluster`.
* Mit dem neuen Autocluster-Plug-In können Sie mit einer einfachen Abfrage Erkenntnisse zu Komponenten und Clustern ableiten. 

Im folgenden Beispiel können Sie deutlich erkennen, dass jeder der häufigsten vier Fehler für eine Komponente spezifisch ist. Außerdem tritt der vierte Fehler, auch wenn die häufigsten drei Fehler für den DB4-Cluster spezifisch sind, in allen Clustern auf.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Anzahl |Prozentsatz (%)|Komponente|Cluster|Meldung
|---|---|---|---|---
|7125|26,64|InferenceHostService|DB4|ExecuteAlgorithmMethod für Methode...
|7125|26,64|Unbekannte Komponente|DB4|Fehler des Aufrufs von „InferenceHostService“...
|7124|26,64|InferenceAlgorithmExecutor|DB4|Unerwarteter Rückschlusssystemfehler...
|5112|19,11|InferenceAlgorithmExecutor|*|Unerwarteter Rückschlusssystemfehler...

## <a name="map-values-from-one-set-to-another"></a>Zuordnen von Werten aus einem Satz zum anderen

Ein gängiger Abfrageanwendungsfall ist die statische Zuordnung von Werten. Die statische Zuordnung kann dazu beitragen, dass sich Ergebnisse besser darstellen lassen.

In der nächsten Tabelle gibt `DeviceModel` beispielsweise ein Gerätemodell an. Die Verwendung des Gerätemodells ist keine bequeme Methode, um auf den Gerätenamen zu verweisen.  

|DeviceModel |Anzahl 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

 Die Verwendung eines Anzeigenamens ist bequemer:  

|FriendlyName |Anzahl 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Die nächsten beiden Beispiele veranschaulichen, wie Sie von der Verwendung des Gerätemodells zum Anzeigenamen wechseln, um ein Gerät zu identifizieren.  

### <a name="map-by-using-a-dynamic-dictionary"></a>Zuordnen mithilfe eines dynamischen Wörterbuchs

Sie können die Zuordnung mithilfe eines dynamischen Wörterbuchs und dynamischer Zugriffsmethoden erzielen. Zum Beispiel:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Dataset definition
let Source = datatable(DeviceModel:string, Count:long)
[
  'iPhone5,1', 32,
  'iPhone3,2', 432,
  'iPhone7,2', 55,
  'iPhone5,2', 66,
];
// Query start here
let phone_mapping = dynamic(
  {
    "iPhone5,1" : "iPhone 5",
    "iPhone3,2" : "iPhone 4",
    "iPhone7,2" : "iPhone 6",
    "iPhone5,2" : "iPhone5"
  });
Source 
| project FriendlyName = phone_mapping[DeviceModel], Count
```

|FriendlyName|Anzahl|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|

### <a name="map-by-using-a-static-table"></a>Zuordnen mithilfe einer statischen Tabelle

Sie können die Zuordnung auch mithilfe einer persistenten Tabelle und eines `join`-Operators erzielen.
 
1. Erstellen Sie die Zuordnungstabelle nur einmal:

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. Erstellen einer Tabelle der Geräteinhalte:

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5,1 |iPhone 5 
    |iPhone3,2 |iPhone 4 
    |iPhone7,2 |iPhone 6 
    |iPhone5,2 |iPhone5 

1. Erstellen Sie eine Testtabellenquelle:

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. Verknüpfen Sie die Tabellen, und führen Sie das Projekt aus:

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

So sieht die Ausgabe aus:

|FriendlyName |Anzahl 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Erstellen und Verwenden von Abfragezeit-Dimensionstabellen

Häufig möchten Sie die Ergebnisse einer Abfrage mit einer Ad-hoc-Dimensionstabelle verknüpfen, die nicht in der Datenbank gespeichert ist. Sie können einen Ausdruck definieren, dessen Ergebnis eine Tabelle ist, die auf eine einzelne Abfrage beschränkt ist. 

Zum Beispiel:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Create a query-time dimension table using datatable
let DimTable = datatable(EventType:string, Code:string)
  [
    "Heavy Rain", "HR",
    "Tornado",    "T"
  ]
;
DimTable
| join StormEvents on EventType
| summarize count() by Code
```

Hier sehen Sie ein nur unwesentlich komplexeres Beispiel:

```kusto
// Create a query-time dimension table using datatable
let TeamFoundationJobResult = datatable(Result:int, ResultString:string)
  [
    -1, 'None', 0, 'Succeeded', 1, 'PartiallySucceeded', 2, 'Failed',
    3, 'Stopped', 4, 'Killed', 5, 'Blocked', 6, 'ExtensionNotFound',
    7, 'Inactive', 8, 'Disabled', 9, 'JobInitializationError'
  ]
;
JobHistory
  | where PreciseTimeStamp > ago(1h)
  | where Service  != "AX"
  | where Plugin has "Analytics"
  | sort by PreciseTimeStamp desc
  | join kind=leftouter TeamFoundationJobResult on Result
  | extend ExecutionTimeSpan = totimespan(ExecutionTime)
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage
```

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>Abrufen der neuesten Datensätze (nach Zeitstempel) pro Identität

Angenommen, Sie haben eine Tabelle, die Folgendes enthält:
* Eine `ID`-Spalte, die die Entität identifiziert, der jede einzelne Zeile zugeordnet ist, z. B. eine Benutzer-ID oder eine Knoten-ID.
* Eine `timestamp`-Spalte, die den Zeitverweis für die Zeile bereitstellt.
* Andere Spalten

Mit dem [„top-nested“-Operator](topnestedoperator.md) können Sie eine Abfrage erstellen, die die neuesten beiden Datensätze für jeden Wert der Spalte `ID` zurückgibt, wobei _neueste_ als _mit dem höchsten Wert von `timestamp`_ definiert ist:

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // #1
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // #2
  top-nested 2 of timestamp by dummy1=max(timestamp),          // #3
  top-nested   of bla       by dummy2=max(1)                   // #4
| project-away dummy0, dummy1, dummy2                          // #5
```


Im Folgenden finden Sie eine schrittweise Erläuterung der vorhergehenden Abfrage (die Nummerierung bezieht sich auf die Zahlen in den Codekommentaren):

1. Die `datatable` ist eine Möglichkeit, Testdaten zu Demonstrationszwecken zu erzeugen. Normalerweise würden Sie hier echte Daten verwenden.
1. Diese Zeile bedeutet im Wesentlichen _alle unterschiedlichen Werte von `id` zurückgeben_. 
1. Diese Zeile gibt dann Ergebnisse für die obersten beiden Datensätze zurück, die ein Maximum bilden:
     * Die `timestamp`-Spalte
     * Die Spalten der vorangehenden Ebene (hier nur `id`)
     * Die auf dieser Ebene angegebene Spalte (hier `timestamp`)
1. In dieser Zeile werden die Werte der Spalte `bla` für jeden der Datensätze, die von der vorangehenden Ebene zurückgegeben werden, addiert. Wenn die Tabelle weitere Spalten enthält, an denen Sie interessiert sind, können Sie diese Zeile für jede dieser Spalten wiederholen.
1. In der letzten Zeile wird der [„project-away“-Operator](projectawayoperator.md) verwendet, um die „zusätzlichen“ Spalten zu entfernen, die von `top-nested` eingeführt werden.

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>Erweitern einer Tabelle um einen Prozentsatz der Gesamtberechnung

Ein Tabellenausdruck, der eine numerische Spalte enthält, ist für den Benutzer nützlicher, wenn er von seinem Wert als Prozentsatz des Gesamtwerts begleitet wird.

Nehmen Sie beispielsweise an, dass eine Abfrage die folgende Tabelle erzeugt:

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

Sie möchten die Tabelle wie folgt anzeigen:

|SomeSeries|SomeInt|Proz. |
|----------|-------|----|
|Apple       |    100|33,3|
|Banana       |    200|66,6|

Um die Darstellungsweise der Tabelle zu ändern, berechnen Sie den Gesamtwert (Summe) der Spalte `SomeInt` und teilen dann jeden Wert dieser Spalte durch den Gesamtwert. Um beliebige Ergebnisse zu erzielen, verwenden Sie den [as-Operator](asoperator.md).

Zum Beispiel:

```kusto
// The following table literally represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Apple",
  200, "Banana",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>Durchführen von Aggregation über ein gleitendes Fenster

Im folgenden Beispiel wird gezeigt, wie Sie Spalten mithilfe eines gleitenden Fensters zusammenfassen. Verwenden Sie für die Abfrage die folgende Tabelle, die die Preise von Obst nach Zeitstempeln enthält.

Berechnen Sie die Mindest-, Höchst- und Gesamtkosten der einzelnen Früchte pro Tag, indem Sie ein gleitendes Fenster von sieben Tagen verwenden. Jeder Datensatz im Resultset aggregiert die vorangegangenen sieben Tage, und die Ergebnisse enthalten einen Datensatz pro Tag im Analysezeitraum.

Obst-Tabelle:

|Timestamp|Frucht|Preis|
|---|---|---|
|2018-09-24 21:00:00.0000000|Bananen|3|
|2018-09-25 20:00:00.0000000|Äpfel|9|
|2018-09-26 03:00:00.0000000|Bananen|4|
|2018-09-27 10:00:00.0000000|Pflaumen|8|
|2018-09-28 07:00:00.0000000|Bananen|6|
|2018-09-29 21:00:00.0000000|Bananen|8|
|2018-09-30 01:00:00.0000000|Pflaumen|2|
|2018-10-01 05:00:00.0000000|Bananen|0|
|2018-10-02 02:00:00.0000000|Bananen|0|
|2018-10-03 13:00:00.0000000|Pflaumen|4|
|2018-10-04 14:00:00.0000000|Äpfel|8|
|2018-10-05 05:00:00.0000000|Bananen|2|
|2018-10-06 08:00:00.0000000|Pflaumen|8|
|2018-10-07 12:00:00.0000000|Bananen|0|

Hier sehen Sie die Aggregationsabfrage für das gleitende Fenster. Weitere Informationen finden Sie in der Erläuterung im Anschluss an das Abfrageergebnis.

```kusto
let _start = datetime(2018-09-24);
let _end = _start + 13d; 
Fruits 
| extend _bin = bin_at(Timestamp, 1d, _start) // #1 
| extend _endRange = iif(_bin + 7d > _end, _end, 
                            iff( _bin + 7d - 1d < _start, _start,
                                iff( _bin + 7d - 1d < _bin, _bin,  _bin + 7d - 1d)))  // #2
| extend _range = range(_bin, _endRange, 1d) // #3
| mv-expand _range to typeof(datetime) limit 1000000 // #4
| summarize min(Price), max(Price), sum(Price) by Timestamp=bin_at(_range, 1d, _start) ,  Fruit // #5
| where Timestamp >= _start + 7d; // #6

```

So sieht die Ausgabe aus:

|Timestamp|Frucht|min_Price|max_Price|sum_Price|
|---|---|---|---|---|
|2018-10-01 00:00:00.0000000|Äpfel|9|9|9|
|2018-10-01 00:00:00.0000000|Bananen|0|8|18|
|2018-10-01 00:00:00.0000000|Pflaumen|2|8|10|
|2018-10-02 00:00:00.0000000|Bananen|0|8|18|
|2018-10-02 00:00:00.0000000|Pflaumen|2|8|10|
|2018-10-03 00:00:00.0000000|Pflaumen|2|8|14|
|2018-10-03 00:00:00.0000000|Bananen|0|8|14|
|2018-10-04 00:00:00.0000000|Bananen|0|8|14|
|2018-10-04 00:00:00.0000000|Pflaumen|2|4|6|
|2018-10-04 00:00:00.0000000|Äpfel|8|8|8|
|2018-10-05 00:00:00.0000000|Bananen|0|8|10|
|2018-10-05 00:00:00.0000000|Pflaumen|2|4|6|
|2018-10-05 00:00:00.0000000|Äpfel|8|8|8|
|2018-10-06 00:00:00.0000000|Pflaumen|2|8|14|
|2018-10-06 00:00:00.0000000|Bananen|0|2|2|
|2018-10-06 00:00:00.0000000|Äpfel|8|8|8|
|2018-10-07 00:00:00.0000000|Bananen|0|2|2|
|2018-10-07 00:00:00.0000000|Pflaumen|4|8|12|
|2018-10-07 00:00:00.0000000|Äpfel|8|8|8|

Die Abfrage „streckt“ (dupliziert) jeden Datensatz in der Eingabetabelle über die sieben Tagen nach seinem tatsächlichen Auftreten. Jeder Datensatz wird tatsächlich sieben Mal angezeigt. Folglich umfasst die tägliche Aggregation alle Datensätze der vorangegangenen sieben Tage.


Im Folgenden finden Sie eine schrittweise Erläuterung der vorhergehenden Abfrage: 

1. Fassen Sie jeden Datensatz in einem Intervall von einem Tag (relativ zu `_start`) zusammen. 
1. Bestimmen Sie das Ende des Bereichs pro Datensatz: `_bin + 7d`, es sei denn, der Wert liegt außerhalb des Bereichs von `_start` und `_end`. In diesem Fall wird er angepasst. 
1. Erstellen Sie für jeden Datensatz ein Array von sieben Tagen (Zeitstempeln), beginnend mit dem Tag des aktuellen Datensatzes. 
1. Wenden Sie `mv-expand` auf das Array an, wodurch jeder Datensatz in sieben Datensätze dupliziert wird, jeweils einen Tag auseinander. 
1. Führen Sie die Aggregatfunktion für jeden Tag aus. Aufgrund von #4 fasst dieser Schritt tatsächlich die _vergangenen_ sieben Tagen zusammen. 
1. Die Daten für die ersten sieben Tage sind unvollständig, da für die ersten sieben Tage kein Rückblickzeitraum von sieben Tagen vorhanden ist. Die ersten sieben Tage werden aus dem Endergebnis ausgeschlossen. In diesem Beispiel sind Sie nur Bestandteil der Aggregation für 2018-10-01.

## <a name="find-the-preceding-event"></a>Suchen des vorhergehenden Ereignisses

Im nächsten Beispiel wird veranschaulicht, wie Sie ein vorhergehendes Ereignis zwischen zwei Datasets finden.  

Sie verfügen über zwei Datasets: A und B. Suchen Sie für jeden Datensatz in Dataset B sein vorheriges Ereignis in Dataset A (d. h. den `arg_max`-Datensatz in A, der noch _älter_ als B ist).

Hier sehen Sie die Beispieldatasets: 

```kusto
let A = datatable(Timestamp:datetime, ID:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, ID:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Timestamp|ID|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|j|Ay1|
|2019-01-01 00:00:05.0000000|j|Ay2|

</br>

|Timestamp|ID|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|j|B|
|2019-01-01 00:02:00.0000000|z|B|

Erwartete Ausgabe: 

|ID|Timestamp|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|j|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Wir empfehlen zwei unterschiedliche Ansätze für dieses Problem. Sie können beide mit Ihrem spezifischen Dataset testen, um den herauszufinden, der für Ihr Szenario am besten geeignet ist.

> [!NOTE] 
> Jeder Ansatz wird mit verschiedenen Datasets möglicherweise unterschiedlich ausgeführt.

### <a name="approach-1"></a>Ansatz 1

Bei diesem Ansatz werden beide Datasets nach ID und Zeitstempel serialisiert. Anschließend werden alle Ereignisse in Dataset B mit allen ihren vorangehenden Ereignissen in Dataset A gruppiert. Schließlich wählt er unter allen Ereignissen in Dataset A in der Gruppe den `arg_max` aus.

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by ID, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != ID), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, ID
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="approach-2"></a>Ansatz 2

Dieser Ansatz zum Lösen des Problems erfordert einen maximalen Rückblickzeitraum. Der Ansatz untersucht, um wie viel _älter_ der Datensatz in Dataset A im Vergleich zu dem in Dataset B sein könnte. Die Methode verknüpft dann die beiden Datasets auf Grundlage der ID und dieses Rückblickzeitraums.

`join` gerzeugt alle möglichen Kandidaten, alle Dataset A-Datensätze, die älter sind als Datensätze in Dataset B und innerhalb des Rückblickzeitraums. Anschließend wird der, der am nächste an dem in Dataset B liegt, nach `arg_min (TimestampB - TimestampA)` gefiltert. Je kürzer der Rückblickzeitraum ist, desto besser werden die Abfrageergebnisse.

Im folgenden Beispiel ist der Rückblickzeitraum auf `1m` festgelegt. Der Datensatz mit der ID `z` weist kein entsprechendes `A`-Ereignis auf, weil sein `A`-Ereignis um zwei Minuten älter ist.

```kusto 
let _maxLookbackPeriod = 1m;  
let _internalWindowBin = _maxLookbackPeriod / 2;
let B_events = B 
    | extend ID = new_guid()
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend B_Timestamp = Timestamp, _range;
let A_events = A 
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend A_Timestamp = Timestamp, _range;
B_events
    | join kind=leftouter (
        A_events
) on ID, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project ID, B_Timestamp, A_Timestamp, EventB, EventA
```

|ID|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|j|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>Nächste Schritte

- Exemplarische Vorgehensweise anhand eines [Tutorials zur Kusto-Abfragesprache](tutorial.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Dieser Artikel identifiziert Anforderungen an gängige Abfragen in Azure Monitor und erläutert, wie Sie die Kusto-Abfragesprache verwenden können, um sie zu erfüllen.

## <a name="string-operations"></a>Zeichenfolgenvorgänge

In den folgenden Abschnitten finden Sie Beispiele für das Arbeiten mit Zeichenfolgen, wenn Sie die Kusto-Abfragesprache verwenden.

### <a name="strings-and-how-to-escape-them"></a>Zeichenfolgen und deren Escapezeichen

Zeichenfolgenwerte werden entweder in einfache oder doppelte Anführungszeichen eingeschlossen. Fügen Sie den umgekehrten Schrägstrich (\\) links von einem Zeichen hinzu, um das Zeichen zu escapen: `\t` für Tabulator, `\n` für Zeilenvorschub und `\"` für das einfache Anführungszeichen.

```kusto
print "this is a 'string' literal in double \" quotes"
```

```kusto
print 'this is a "string" literal in single \' quotes'
```

Wenn „\\“ nicht als Escapezeichen verwendet werden soll, müssen Sie der Zeichenfolge „\@“ voranstellen.

```kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>Zeichenfolgenvergleiche

Operator       |BESCHREIBUNG                         |Beachtet Groß-/Kleinschreibung|Beispiel (ergibt `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Equals                              |Ja           |`"aBc" == "aBc"`
`!=`           |Not Equals                          |Ja           |`"abc" != "ABC"`
`=~`           |Equals                              |Nein            |`"abc" =~ "ABC"`
`!~`           |Not Equals                          |Nein            |`"aBc" !~ "xyz"`
`has`          |Rechter Wert ist vollständig im linken Wert enthalten |Nein|`"North America" has "america"`
`!has`         |Rechter Wert ist nicht vollständig im linken Wert enthalten       |Nein            |`"North America" !has "amer"` 
`has_cs`       |Rechter Wert ist vollständig im linken Wert enthalten |Ja|`"North America" has_cs "America"`
`!has_cs`      |Rechter Wert ist nicht vollständig im linken Wert enthalten       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Rechter Wert ist ein Termpräfix im linken Wert         |Nein            |`"North America" hasprefix "ame"`
`!hasprefix`   |Rechter Wert ist kein Termpräfix im linken Wert     |Nein            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Rechter Wert ist ein Termpräfix im linken Wert         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Rechter Wert ist kein Termpräfix im linken Wert     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Rechter Wert ist ein Termsuffix im linken Wert         |Nein            |`"North America" hassuffix "ica"`
`!hassuffix`   |Rechter Wert ist kein Termsuffix im linken Wert     |Nein            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Rechter Wert ist ein Termsuffix im linken Wert         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Rechter Wert ist kein Termsuffix im linken Wert     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Right-side value occurs as a subsequence of left-side value  |Nein            |`"FabriKam" contains "BRik"`
`!contains`    |Rechter Wert kommt nicht im linken Wert vor           |Nein            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Rechter Wert kommt als Teilzeichenfolge im linken Wert vor  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Rechter Wert kommt nicht im linken Wert vor           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Rechter Wert ist eine anfängliche Teilzeichenfolge im linken Wert|Nein            |`"Fabrikam" startswith "fab"`
`!startswith`  |Rechter Wert ist keine anfängliche Teilzeichenfolge im linken Wert|Nein        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Rechter Wert ist eine Teilzeichenfolge am Anfang des linken Werts|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Rechter Wert ist keine Teilzeichenfolge am Anfang des linken Werts|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Rechter Wert ist eine Teilzeichenfolge am Ende des linken Werts|Nein             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Rechter Wert ist keine Teilzeichenfolge am Ende des linken Werts|Nein         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Rechter Wert ist eine Teilzeichenfolge am Ende des linken Werts|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Rechter Wert ist keine Teilzeichenfolge am Ende des linken Werts|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|Linker Wert enthält eine Übereinstimmung mit dem rechten Wert|Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Entspricht einem der Elemente       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Entspricht keinem der Elemente   |Ja           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Kann einfache Zeichenfolgen abgleichen oder reguläre Ausdrücke (regex) verwenden. Bei Übereinstimmungen für einfache Zeichenfolgen können Überschneidungen auftreten, doch bei Übereinstimmungen für reguläre Ausdrücke kommt dies nicht vor.

```
countof(text, search [, kind])
```

- `text`: Die eingegebene Zeichenfolge 
- `search`: Die einfache Zeichenfolge oder der reguläre Ausdruck, die/der in „text“ abgeglichen werden soll.
- `kind`: _normal_ | _regex_ (Standard: „normal“).

Gibt zurück, wie oft Übereinstimmungen für die Suchzeichenfolge im Container gefunden werden können. Bei Übereinstimmungen für einfache Zeichenfolgen können Überschneidungen auftreten, doch bei Übereinstimmungen für reguläre Ausdrücke kommt dies nicht vor.

#### <a name="plain-string-matches"></a>Übereinstimmungen für einfache Zeichenfolgen

```kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Übereinstimmungen für reguläre Ausdrücke

```kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>*extract*

Ruft eine Übereinstimmung für einen regulären Ausdruck aus einer bestimmten Zeichenfolge ab. Optional kann die extrahierte Teilzeichenfolge in den angegebenen Typ konvertiert werden.

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`: Ein regulärer Ausdruck.
- `captureGroup`: Eine positive Integerkonstante, die die zu extrahierende Erfassungsgruppe angibt. Verwenden Sie 0 für vollständige Übereinstimmung, 1 für den mit der ersten Klammer \(\) übereinstimmenden Wert im regulären Ausdruck und 2 oder höher für nachfolgende Klammern.
- `text`: Die zu suchende Zeichenfolge.
- `typeLiteral`: Ein optionales Typliteral (z. B. `typeof(long)`). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert.

Gibt die mit der angegebenen Erfassungsgruppe `captureGroup` abgeglichene Teilzeichenfolge zurück, optional konvertiert in `typeLiteral`. Wenn keine Übereinstimmung vorliegt oder bei der Typkonvertierung ein Fehler auftritt, wird NULL zurückgegeben.

Im folgenden Beispiel wird aus einem Heartbeatdatensatz das letzte Oktett von `ComputerIP` extrahiert:

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Im folgenden Beispiel wird das letzte Oktett extrahiert und in den Typ *real* (Zahl) umgewandelt. Anschließend wird der nächste IP-Wert berechnet:

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Im nächsten Beispiel wird die Zeichenfolge `Trace` nach einer Definition für `Duration` durchsucht. Die Übereinstimmung wird in `real` umgewandelt und mit einer Zeitkonstanten (1 s) multipliziert, die dann wiederum `Duration` in den Typ `timespan` umwandelt.

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*isempty*, *isnotempty*, *notempty*

- `isempty` gibt `true` zurück, wenn das Argument eine leere Zeichenfolge oder NULL ist (siehe `isnull`).
- `isnotempty` gibt `true` zurück, wenn das Argument keine leere Zeichenfolge oder NULL ist (siehe `isnotnull`). Alias: `notempty`.


```kusto
isempty(value)
isnotempty(value)
```

#### <a name="example"></a>Beispiel

```kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>*parseurl*

Teilt eine URL in ihre jeweiligen Bestandteile wie Protokoll, Host und Port auf und gibt dann ein Wörterbuchobjekt zurück, das diese Bestandteile als Zeichenfolgen enthält.

```
parseurl(urlstring)
```

#### <a name="example"></a>Beispiel

```kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

So sieht die Ausgabe aus:

```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```

### <a name="replace"></a>*replace*

Ersetzt alle Übereinstimmungen für reguläre Ausdrücke durch eine andere Zeichenfolge. 

```
replace(regex, rewrite, input_text)
```

- `regex`: Der reguläre Ausdruck, für den eine Übereinstimmung ermittelt werden soll. Er kann Erfassungsgruppen in Klammern \(\) enthalten.
- `rewrite`: Der reguläre Ausdruck, durch den alle Übereinstimmungen ersetzt werden sollen, die durch Abgleich mit einem regulären Ausdruck ermittelt wurden. Verwenden Sie „\0“, um auf die gesamte Übereinstimmung zu verweisen, „\1“ für die erste Erfassungsgruppe, „\2“ für die zweite Erfassungsgruppe usw.
- `input_text`: Die Eingabezeichenfolge, in der gesucht werden soll.

Gibt den Text nach dem Ersetzen aller Übereinstimmungen für reguläre Ausdrücke durch Auswertungen von „rewrite“ zurück. Bei Übereinstimmungen kommt es nicht zu Überschneidungen.

#### <a name="example"></a>Beispiel

```kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

So sieht die Ausgabe aus:

Aktivität                                        |Ersetzt
------------------------------------------------|----------------------------------------------------------
4663: Es wurde versucht, auf das Objekt zuzugreifen.  |Aktivitäts-ID 4663: Es wurde versucht, auf das Objekt zuzugreifen.


### <a name="split"></a>*split*

Teilt eine bestimmte Zeichenfolge entsprechend des festgelegten Trennzeichens auf und gibt dann ein Array mit den sich ergebenden Teilzeichenfolgen zurück.

```
split(source, delimiter [, requestedIndex])
```

- `source`: Die Zeichenfolge, die anhand des festgelegten Trennzeichens aufgeteilt werden soll.
- `delimiter`: Das Trennzeichen, das zum Teilen der Quellzeichenfolge verwendet werden soll.
- `requestedIndex`: Ein optionaler, nullbasierter Index. Falls ein solcher angegeben wird, enthält das Zeichenfolgenarray nur dieses Element (sofern vorhanden).


#### <a name="example"></a>Beispiel

```kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>*strcat*

Verkettet Zeichenfolgenargumente (unterstützt 1 bis 16 Argumente).

```
strcat("string1", "string2", "string3")
```

#### <a name="example"></a>Beispiel

```kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>*strlen*

Gibt die Länge einer Zeichenfolge zurück.

```
strlen("text_to_evaluate")
```

#### <a name="example"></a>Beispiel

```kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>*substring*

Extrahiert ab dem angegebenen Index eine Teilzeichenfolge aus einer bestimmten Quellzeichenfolge. Optional können Sie die Länge der angeforderten Teilzeichenfolge angeben.

```
substring(source, startingIndex [, length])
```

- `source`: Die Quellzeichenfolge, aus der die Teilzeichenfolge entnommen wird.
- `startingIndex`: Die nullbasierte Position des Anfangszeichens der angeforderten Teilzeichenfolge.
- `length`: Ein optionaler Parameter, mit dem Sie die erforderliche Länge der zurückgegebenen Teilzeichenfolge festlegen können.

#### <a name="example"></a>Beispiel

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*tolower*, *toupper*

Konvertiert eine bestimmte Zeichenfolge vollständig in Kleinbuchstaben oder vollständig in Großbuchstaben.

```
tolower("value")
toupper("value")
```

#### <a name="example"></a>Beispiel

```kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>Datums- und Uhrzeitvorgänge

In den folgenden Abschnitten finden Sie Beispiele für das Arbeiten mit Datums- und Uhrzeitwerten, wenn Sie die Kusto-Abfragesprache verwenden.

### <a name="date-time-basics"></a>Grundlagen zu Datum und Uhrzeit

Die Abfragesprache Kusto verfügt über zwei Hauptdatentypen, die Datums- und Uhrzeitangaben zugeordnet sind: `datetime`und `timespan`. Alle Datumsangaben werden in UTC ausgedrückt. Wenn auch mehrere Datums-/Uhrzeitformate unterstützt werden, wird doch das ISO8601-Format bevorzugt. 

Zeiträume werden als Dezimalwert gefolgt von einer Zeiteinheit ausgedrückt:

|Kurzform   | Zeiteinheit    |
|:---|:---|
|d           | day          |
|h.           | hour         |
|m           | minute       |
|s           | second       |
|ms          | Millisekunde  |
|Mikrosekunde | Mikrosekunde  |
|Takt        | Nanosekunde   |

Sie können Datums-/Uhrzeitwerte können durch Umwandeln einer Zeichenfolge mithilfe des Operators `todatetime` erstellen. Verwenden Sie beispielsweise zur Überprüfung der in einer bestimmten Zeitspanne gesendeten VM-Heartbeats den Operator `between`, um einen Zeitraum anzugeben:

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ein weiteres gängiges Szenario besteht im Vergleichen eines Datums-/Uhrzeitwerts mit der Gegenwart. Wenn Sie beispielsweise sämtliche Heartbeats der letzten zwei Minuten anzeigen möchten, können Sie den Operator `now` zusammen mit einem timespan-Wert verwenden, der zwei Minuten darstellt:

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Für diese Funktion ist auch eine Verknüpfung verfügbar:

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Die kürzeste und am besten lesbare Methode besteht in der Verwendung des Operators `ago`:

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Angenommen, Ihnen ist die Start- und Endzeit nicht bekannt, sondern die Startzeit und die Dauer. Sie können die Abfrage umschreiben:

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>Konvertieren von Zeiteinheiten

Sie können einen Datums-/Uhrzeitwert oder einen Zeitraumwert in einer anderen als der Standardzeiteinheit ausdrücken. Wenn Sie beispielsweise Fehlerereignisse der letzten 30 Minuten überprüfen und eine berechnete Spalte benötigen, in der angezeigt wird, wie lange das Auftreten des Ereignisses her ist, können Sie diese Abfrage verwenden:

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Die Spalte `timeAgo` enthält Werte wie `00:09:31.5118992`, die als „hh:mm:ss.fffffff“ formatiert sind. Wenn Sie diese Werte auf die `number` (Anzahl) der Minuten seit der Startzeit formatieren möchten, teilen Sie diesen Wert durch `1m`:

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregationen und Zuordnung von Buckets nach Zeitintervallen

Ein weiteres gängiges Szenario besteht in der Notwendigkeit, Statistiken für einen bestimmten Zeitraum in einer bestimmten Zeiteinheit abzurufen. Für dieses Szenario können Sie einen `bin`-Operator als Teil einer `summarize`-Klausel verwenden.

Mit der folgenden Abfrage können Sie die Anzahl der Ereignisse abrufen, die in der letzten halben Stunde alle fünf Minuten aufgetreten sind:

```kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Diese Abfrage erzeugt die folgende Tabelle:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Eine weitere Möglichkeit zum Erstellen von Buckets mit Ergebnissen besteht in der Verwendung von Funktionen wie `startofday`:

```kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

So sieht die Ausgabe aus:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7\.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5\.416|


### <a name="time-zones"></a>Zeitzonen

Da alle Datums-/Uhrzeitwerte in UTC ausgedrückt werden, ist es häufig hilfreich, diese Werte in die lokale Zeitzone zu konvertieren. Verwenden Sie beispielsweise die folgende Berechnung zum Konvertieren von UTC- in PST-Zeiten:

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Aggregationen

In den folgenden Abschnitten finden Sie Beispiele für das Aggregieren der Ergebnisse einer Abfrage, wenn Sie die Kusto-Abfragesprache verwenden.

### <a name="count"></a>*count*

Zählen Sie die Anzahl der Zeilen im Resultset, nachdem alle Filter angewendet wurden. Das folgende Beispiel gibt die Gesamtzahl der Zeilen in der Tabelle `Perf` aus den letzten 30 Minuten zurück. Die Ergebnisse werden in einer Spalte namens `count_` zurückgegeben, sofern Sie der Spalte keinen spezifischen Namen zugewiesen haben:


```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Eine Zeitdiagrammvisualisierung kann hilfreich sein, um einen zeitlichen Trend zu erkennen:

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Die Ausgabe in diesem Beispiel zeigt die Trendlinie für die Anzahl der `Perf`-Datensätze in 5-Minuten-Intervallen:


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="Screenshot eines Liniendiagramms, das die Trendlinie für die Anzahl der Perf-Datensätze in 5-Minuten-Intervallen zeigt.":::

### <a name="dcount-dcountif"></a>*dcount*, *dcountif*

Verwenden Sie `dcount` und `dcountif` zum Zählen von bestimmten Werten in einer bestimmten Spalte. Die folgende Abfrage wertet aus, wie viele unterschiedliche Computer in der letzten Stunde Takte gesendet haben:

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Verwenden Sie `dcountif`, um nur die Linux-Computer zu zählen, die Heartbeats gesendet haben:

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluate-subgroups"></a>Auswerten von Untergruppen

Verwenden Sie das Schlüsselwort `by`, um eine Zählung oder Aggregationen auf Untergruppen in Ihren Daten auszuführen. Beispielsweise um die Anzahl der unterschiedlichen Linux-Computer zu zählen, die in jedem Land bzw. jeder Region Heartbeats gesendet haben, verwenden Sie diese Abfrage:

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|USA    | 19                  |
|Kanada           | 3                   |
|Irland          | 0                   |
|Vereinigtes Königreich   | 0                   |
|Niederlande      | 2                   |


Um noch kleineren Untergruppen Ihrer Daten zu analysieren, fügen Sie im Abschnitt `by` Spaltennamen hinzu. Beispielsweise können Sie die unterschiedlichen Computer aus jedem Land bzw. jeder Region pro Betriebssystemtyp zählen (`OSType`):

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>Perzentil

Um den Median zu ermitteln, verwenden Sie die `percentile`-Funktion mit einem Wert zum Angeben des Quantils:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Sie können auch verschiedene Quantile angeben, um für jedes ein aggregiertes Ergebnis zu erhalten:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Die Ergebnisse zeigen möglicherweise, dass einige Computer-CPUs ähnliche Medianwerte aufweisen. Obwohl sich einige Computer stetig im Bereich des Medians befinden, haben andere wesentlich niedrigere und höhere CPU-Werte gemeldet. Der hohen und niedrigen Werte bedeuten, dass bei den Computern Spitzen aufgetreten sind.

### <a name="variance"></a>Variance

Um die Varianz eines Werts direkt zu evaluieren, verwenden Sie die Standardabweichung und Varianzmethoden:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Eine gute Möglichkeit zur Analyse der Stabilität der CPU-Auslastung besteht darin, `stdev` mit der Berechnung des Medians zu kombinieren:

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>Generieren von Listen und Sätzen

Sie können mithilfe von `makelist` Daten nach der Reihenfolge der Werte in einer bestimmten Spalte pivotieren. So können Sie beispielsweise die gängigsten Auftragsereignisse untersuchen, die auf Ihren Computern vorkommen. Sie können die Daten im Wesentlichen nach der Reihenfolge der `EventID`-Werte auf den einzelnen Computern pivotieren: 

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

So sieht die Ausgabe aus:

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` generiert eine Liste in der Reihenfolge, in der Daten an sie übergeben wurden. Verwenden Sie zum Sortieren von Ereignissen vom ältesten zum neuesten in der `order`-Anweisung `asc` anstelle von `desc`. 

Möglicherweise finden Sie es auch hilfreich, eine Liste mit nur den unterschiedlichen Werten zu erstellen. Diese Liste wird als _set_ (Satz) bezeichnet, und Sie können sie mit dem Befehl `makeset` generieren:

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

So sieht die Ausgabe aus:

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Wie `makelist` funktioniert `makeset` auch mit sortierten Daten. Der Befehl `makeset` generiert die Arrays basierend auf der Reihenfolge der an ihn übergebenen Zeilen.

### <a name="expand-lists"></a>Erweitern von Listen

Der umgekehrte Vorgang von `makelist` oder `makeset` ist `mv-expand`. Der Befehl `mv-expand` erweitert eine Liste von Werten, um Zeilen zu trennen. Er kann eine Erweiterung über eine beliebige Anzahl dynamischer Spalten erzielen, einschließlich JSON- und Array-Spalten. So können Sie beispielsweise die Tabelle `Heartbeat` auf Lösungen überprüfen, die Daten von Computern gesendet haben, die in der vergangenen Stunde einen Heartbeat gesendet haben:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

So sieht die Ausgabe aus:

| Computer | Lösungen | 
|--------------|----------------------|
| computer1 | „security“, „updates“, „changeTracking“ |
| computer2 | „security“, „updates“ |
| computer3 | „antiMalware“, „changeTracking“ |
| ... | ... |

Mit `mv-expand` können Sie die einzelnen Werte in einer separaten Zeile statt in einer durch Trennzeichen getrennten Liste anzeigen:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
```

So sieht die Ausgabe aus:

| Computer | Lösungen | 
|--------------|----------------------|
| computer1 | „security“ |
| computer1 | „updates“ |
| computer1 | „changeTracking“ |
| computer2 | „security“ |
| computer2 | „updates“ |
| computer3 | „antiMalware“ |
| computer3 | „changeTracking“ |
| ... | ... |


Sie können `makelist` zum Gruppieren von Elementen verwenden. In der Ausgabe wird die Liste der Computer pro Lösung angezeigt:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

So sieht die Ausgabe aus:

|Lösungen | list_Computer |
|--------------|----------------------|
| „security“ | ["computer1", "computer2"] |
| „updates“ | ["computer1", "computer2"] |
| „changeTracking“ | ["computer1", "computer3"] |
| „antiMalware“ | [„computer3“] |
| ... | ... |

### <a name="missing-bins"></a>Fehlende Intervalle

Eine nützliche Anwendung von `mv-expand` ist das Ausfüllen von Standardwerten für fehlende Intervalle. Angenommen beispielsweise, Sie suchen nach der Betriebszeit eines bestimmten Computers, indem Sie dessen Heartbeat untersuchen. Sie möchten auch die Quelle des Heartbeats sehen, die in der Spalte `Category` zu finden ist. Normalerweise verwenden wir eine einfache `summarize`-Anweisung:

```kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

So sieht die Ausgabe aus:

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt-Agent | 2017-06-06T17:00:00Z | 15 |
| Direkt-Agent | 2017-06-06T18:00:00Z | 60 |
| Direkt-Agent | 2017-06-06T20:00:00Z | 55 |
| Direkt-Agent | 2017-06-06T21:00:00Z | 57 |
| Direkt-Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

In der Ausgabe fehlt jedoch der Bucket, der „2017-06-06T19:00:00Z“ zugeordnet ist, weil für diese Stunde keine Heartbeatdaten vorhanden sind. Mit der Funktion `make-series` können Sie leeren Buckets einen Standardwert zuweisen. Für jede Kategorie wird eine Zeile generiert. Die Ausgabe umfasst zwei zusätzliche Arrayspalten, eine für Werte und eine für übereinstimmende Zeitbuckets:

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

So sieht die Ausgabe aus:

| Category | count_ | TimeGenerated |
|---|---|---|
| Direkt-Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Das dritte Element des *count_* -Arrays ist wie erwartet 0. Das _TimeGenerated_-Array hat einen übereinstimmenden Zeitstempel von „2017-06-06T19:00:00.0000000Z“. Dieses Arrayformat ist jedoch schwer zu lesen. Verwenden Sie `mv-expand`, um die Arrays zu erweitern und die gleiche Formatausgabe zu generieren, die von `summarize` generiert wurde:

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mv-expand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

So sieht die Ausgabe aus:

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt-Agent | 2017-06-06T17:00:00Z | 15 |
| Direkt-Agent | 2017-06-06T18:00:00Z | 60 |
| Direkt-Agent | 2017-06-06T19:00:00Z | 0 |
| Direkt-Agent | 2017-06-06T20:00:00Z | 55 |
| Direkt-Agent | 2017-06-06T21:00:00Z | 57 |
| Direkt-Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Eingrenzen von Ergebnissen auf einen Satz von Elementen: *let*, *makeset*, *toscalar*, *in*

Ein gängiges Szenario ist die Auswahl der Namen von bestimmten Entitäten, basierend auf einem Satz von Kriterien. Anschließend wird ein anderes Dataset auf diesen Satz von Entitäten gefiltert. Sie könnten beispielsweise nach Computern suchen, auf denen bekanntermaßen Updates fehlen, und IP-Adressen identifizieren, die von diesen Computern aufgerufen wurden.

Hier sehen Sie ein Beispiel:

```kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>Joins

Mithilfe von Joins (Verknüpfungen) können Sie Daten aus mehreren Tabellen in derselben Abfrage analysieren. Ein Join führt die Zeilen zweier Datasets durch Abgleich der Werte zwischen angegebenen Spalten zusammen.

Hier sehen Sie ein Beispiel:

```kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

In diesem Beispiel filtert das erste Dataset auf alle Anmeldeereignisse. Dieses Dataset wird mit einem zweiten Dataset verknüpft, das auf alle Abmeldeereignisse filtert. Die projizierten Spalten sind `Computer`, `Account`, `TargetLogonId` und `TimeGenerated`. Die Datasets werden über die gemeinsam verwendete Spalte `TargetLogonId` korreliert. Die Ausgabe ist ein einzelner Datensatz pro Korrelation, der die An- und Abmeldezeit enthält.

Wenn beide Datasets Spalten mit demselben Namen aufweisen, erhalten die Spalten des rechten Datasets eine Indexnummer. In diesem Beispiel würden die Ergebnisse `TargetLogonId` mit Werten aus der linken Tabelle und `TargetLogonId1` mit Werten aus der rechten Tabelle anzeigen. In diesem Fall wurde die zweite `TargetLogonId1`-Spalte mithilfe des `project-away`-Operators entfernt.

> [!NOTE]
> Um die Leistung zu verbessern, behalten Sie mithilfe des `project`-Operators nur die relevanten Spalten der verknüpften Datasets bei.


Verwenden Sie die folgende Syntax, um zwei Datasets zu verknüpfen, in denen der verknüpfte Schlüssel in den beiden Tabellen einen unterschiedlichen Namen besitzt:

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Nachschlagetabellen

Joins werden häufig verwendet, um `datatable` für die Zuordnung statischer Werte zu verwenden. Die Verwendung von `datatable` kann dazu beitragen, dass sich Ergebnisse besser darstellen lassen. So können Sie beispielsweise die Sicherheitsereignisdaten um den Ereignisnamen für jede Ereignis-ID erweitern:

```kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

So sieht die Ausgabe aus:

| eventName | count_ |
|:---|:---|
| Das Handle für ein Objekt wurde geschlossen. | 290,995 |
| Ein Handle für ein Objekt wurde angefordert. | 154,157 |
| Es wurde versucht, das Handle eines Objekts zu duplizieren. | 144,305 |
| Es wurde versucht, auf ein Objekt zuzugreifen. | 123,669 |
| Kryptographischer Vorgang | 153,495 |
| Schlüsseldateivorgang | 153,496 |

## <a name="json-and-data-structures"></a>JSON und Datenstrukturen

Geschachtelte Objekte sind Objekte, die andere Objekte in Form eines Arrays oder einer Zuordnung von Schlüssel-Wert-Paaren enthalten. Die Objekte werden als JSON-Zeichenfolgen dargestellt. In diesem Abschnitt wird beschrieben, wie Sie mit JSON Daten abrufen und geschachtelte Objekte analysieren können.

### <a name="work-with-json-strings"></a>Arbeiten mit JSON-Zeichenfolgen

Greifen Sie mit `extractjson` auf ein bestimmtes JSON-Element in einem bekannten Pfad zu. Zur Nutzung dieser Funktion ist ein Pfadausdruck erforderlich, für den die folgenden Konventionen gelten:

- Verwenden Sie _$_ , um auf den Stammordner zu verweisen.
- Verwenden Sie die Klammer- oder Punktnotation, um wie in den folgenden Beispielen dargestellt auf Indizes und Elemente zu verweisen.


Verwenden Sie Klammern für Indizes und Punkte zum Trennen von Elementen:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Dieses Beispiel ist ähnlich, aber es verwendet nur die Notation mit eckigen Klammern:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Für nur ein Element können Sie nur die Punktnotation verwenden:

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

Auf mehrere Elemente in Ihrer JSON-Struktur lässt sich am leichtesten über ein dynamisches Objekt zugreifen. Wandeln Sie die Textdaten mit `parsejson` in ein dynamisches Objekt um. Nachdem Sie das JSON in einen dynamischen Typ konvertiert haben, können Sie die Daten mithilfe zusätzlicher Funktionen analysieren.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```

### <a name="arraylength"></a>*arraylength*

Verwenden Sie `arraylength`, um die Anzahl der Elemente in einem Array zählen zu lassen:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mv-expand"></a>*mv-expand*

Verwenden Sie `mv-expand`, um die Eigenschaften eines Objekts in separate Zeilen aufzuteilen:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="Screenshot von „hosts_0“ mit Werten für „location“, „status“ und „rate“":::

### <a name="buildschema"></a>*buildschema*

Verwenden Sie `buildschema`, um das Schema für zulässige Objektwerte abzurufen:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Das Ergebnis ist ein Schema im JSON-Format:

```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```

Das Schema beschreibt die Namen der Objektfelder und deren entsprechende Datentypen. 

Für geschachtelte Objekte liegen möglicherweise wie im folgenden Beispiel andere Schemas vor:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Diagramme

In den folgenden Abschnitten finden Sie Beispiele für das Arbeiten mit Diagrammen, wenn Sie die Kusto-Abfragesprache verwenden.

### <a name="chart-the-results"></a>Darstellen der Ergebnisse in Diagrammen

Beginnen Sie damit, dass Sie die Anzahl der Computer pro Betriebssystem während der letzten Stunde überprüfen:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Standardmäßig werden die Ergebnisse als Tabelle angezeigt:

:::image type="content" source="images/samples/query-results-table.png" alt-text="Screenshot, der Abfrageergebnisse in einer Tabelle zeigt.":::

Für eine bessere Ansicht wählen Sie **Chart** (Diagramm) und dann die Option **Pie** (Kreisdiagramm) aus, um die Ergebnisse anzuzeigen:

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="Screenshot, der Abfrageergebnisse in einem Kreisdiagramm zeigt.":::

### <a name="timecharts"></a>Zeitdiagramme

Zeigen Sie den Durchschnitt sowie den 50. und 95. Quantil der Prozessorzeit in Intervallen von einer Stunde an. 

Mit der folgenden Abfrage werden mehrere Reihen generiert. In den Ergebnissen können Sie auswählen, welche Reihen im Zeitdiagramm angezeigt werden sollen.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wählen Sie die Anzeigeoption **Liniendiagramm** aus:

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="Screenshot, der ein Liniendiagramm mit mehreren Reihen zeigt.":::

#### <a name="reference-line"></a>Bezugslinie

Eine Bezugslinie kann Ihnen dabei helfen, einfach zu identifizieren, ob die Metrik einen bestimmten Schwellenwert überschritten hat. Um einem Diagramm eine Zeile hinzuzufügen, erweitern Sie das Dataset durch Hinzufügen einer konstanten Spalte:

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="Screenshot, der ein Liniendiagramm mit mehreren Reihen mit einer Bezugslinie für einen Schwellenwert zeigt.":::


### <a name="multiple-dimensions"></a>Mehrere Dimensionen

Mit mehreren Ausdrücken in der `by`-Klausel von `summarize` werden mehrere Zeilen in den Ergebnissen erstellt. Für jede Kombination der Werte wird eine Zeile erstellt.

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Wenn Sie die Ergebnisse als Diagramm anzeigen, verwendet das Diagramm die erste Spalte aus der `by`-Klausel. Im folgenden Beispiel ist ein gestapeltes Säulendiagramm dargestellt, das unter Verwendung des `EventID`-Werts erstellt wurde. Dimensionen müssen vom Typ `string` sein. In diesem Beispiel wird der `EventID`-Wert in `string` umgewandelt:

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="Screenshot, der ein Balkendiagramm zeigt, das auf der Spalte „EventID“ basiert.":::

Sie können zwischen Spalten wechseln, indem Sie den Dropdownpfeil für den Spaltennamen auswählen:

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="Screenshot, der ein Balkendiagramm zeigt, das auf der Spalte „AccountType“ basiert, wobei die Spaltenauswahl angezeigt wird.":::

## <a name="smart-analytics"></a>Intelligente Analysen

Dieser Abschnitt enthält Beispiele für die Nutzung intelligenter Analysefunktionen in Azure Log Analytics, um Benutzeraktivitäten zu analysieren. Sie können diese Beispiele verwenden, um Ihre eigenen Anwendungen, die von Azure Application Insights überwacht werden, zu analysieren, oder Sie können die Konzepte in diesen Abfragen für ähnliche Analysen anderer Daten verwenden. 

### <a name="cohorts-analytics"></a>Kohortenanalysen

Bei der Kohortenanalyse wird die Aktivität von bestimmten Benutzergruppen, die als _Kohorten_ bezeichnet werden, nachverfolgt. Die Kohortenanalyse versucht zu messen, wie attraktiv ein Dienst ist, indem die Rate der wiederkehrenden Benutzer gemessen wird. Benutzer werden nach der Zeit ihrer ersten Nutzung des Diensts gruppiert. Beim Analysieren von Kohorten erwarten wir eine Abnahme der Aktivität gegenüber den ersten nachverfolgten Zeiträumen. Jede Kohorte wird nach der Woche benannt, in der ihre Mitglieder zum ersten Mal identifiziert wurden.

Im folgenden Beispiel wird die Anzahl der Aktivitäten analysiert, die Benutzer im Verlauf von fünf Wochen nach ihrer ersten Nutzung des Diensts ausgeführt haben:

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```

So sieht die Ausgabe aus:

:::image type="content" source="images/samples/cohorts-table.png" alt-text="Screenshot, der eine Tabelle der Kohorten auf Grundlage ihrer Aktivitäten zeigt.":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Neue monatlich aktive Benutzer und Benutzerbindung

In den folgenden Beispielen wird die Zeitreihenanalyse mit der Funktion [series_fir](/azure/kusto/query/series-firfunction) durchgeführt. Sie können die `series_fir`-Funktion für Berechnungen mit gleitendem Fenster verwenden. Die überwachte Beispielanwendung ist ein Onlineshop, der die Aktivität der Benutzer über benutzerdefinierte Ereignisse nachverfolgt. Die Abfrage verfolgt zwei Arten von Benutzeraktivitäten: `AddToCart` und `Checkout`. Sie definiert einen aktiven Benutzer als Benutzer, der an einem bestimmten Tag mindestens einmal einen Auscheckvorgang abgeschlossen hat.

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

So sieht die Ausgabe aus:

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="Screenshot eines Diagramms, das gleitende aktive Benutzer nach Tag über einen Monat hinweg zeigt.":::

Im folgenden Beispiel wird die vorangehende Abfrage in eine wiederverwendbare Funktion umgewandelt. Im Beispiel wird dann die Abfrage verwendet, um die gleitende Benutzerbindung zu berechnen. Ein aktiver Benutzer in dieser Abfrage ist als Benutzer definiert, der an einem bestimmten Tag mindestens einen Auscheckvorgang abgeschlossen hat.

```kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mv-expand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

So sieht die Ausgabe aus:

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="Screenshot eines Diagramms, das die Benutzerbindung im zeitlichen Verlauf zeigt.":::

### <a name="regression-analysis"></a>Regressionsanalyse

Dieses Beispiel zeigt, wie Sie eine automatisierte Erkennung für Dienstunterbrechungen erstellen, die ausschließlich auf den Ablaufverfolgungsprotokollen einer Anwendung basiert. Die Erkennung sucht nach nicht normalen, plötzlichen Anstiegen bei der relativen Anzahl von Fehler- und Warnungsüberwachungen in der Anwendung.

Für die Auswertung des Dienststatus basierend auf Überwachungsprotokolldaten kommen zwei Verfahren zum Einsatz:

- Mit [make-series](/azure/kusto/query/make-seriesoperator) werden teilweise strukturierte Ablaufverfolgungsprotokolle im Textformat in eine Metrik konvertiert, die das Verhältnis zwischen positiven und negativen Ablaufverfolgungseinträgen darstellt.
- Verwenden Sie [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) und [series_fit_line](/azure/kusto/query/series-fit-linefunction) für die komplexere Erkennung von Sprüngen unter Verwendung einer Zeitreihenanalyse mit einer linearen Regression mit zwei Linien.

```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>Nächste Schritte

- Exemplarische Vorgehensweise anhand eines [Tutorials zur Kusto-Abfragesprache](tutorial.md?pivots=azuremonitor).


::: zone-end

