---
title: Beispiele für Abfragen in Azure Daten-Explorer und Azure Monitor
description: In diesem Artikel werden häufige Abfragen und Beispiele beschrieben, in denen die Kusto-Abfragesprache für Azure Daten-Explorer und Azure Monitor verwendet wird.
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
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783524"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Beispiele für Abfragen für Azure Daten-Explorer und Azure Monitor

::: zone pivot="azuredataexplorer"

Dieser Artikel identifiziert allgemeine Abfrage Anforderungen in Azure Daten-Explorer und erläutert, wie Sie die Kusto-Abfragesprache verwenden können, um Sie zu erfüllen.

## <a name="display-a-column-chart"></a>Anzeigen eines Säulen Diagramms

Um zwei oder mehr Spalten zu projizieren, und verwenden Sie dann die Spalten als x-Achse und y-Achse eines Diagramms:

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* Die erste Spalte bildet die x-Achse. Dies kann numerisch, Datum/Uhrzeit oder Zeichenfolge sein. 
* Verwenden `where` `summarize` Sie, und, `top` um die Datenmenge einzuschränken, die Sie anzeigen.
* Sortieren Sie die Ergebnisse, um die Reihenfolge der x-Achse zu definieren.

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="Screenshot eines Säulen Diagramms mit zehn farbigen Spalten, die die entsprechenden Werte von 10 Standorten darstellen.":::

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

Jedes Ereignis verfügt über eine Sitzungs-ID ( `SessionId` ). Die Herausforderung besteht darin, Ereignisse zum Starten und Abbrechen mit einer Sitzungs-ID abzugleichen.

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

So stimmen Sie Start-und Stoppereignisse mit einer Sitzungs-ID ab:

1. Verwenden [Sie Let](./letstatement.md) , um eine Projektion der Tabelle zu benennen, die so weit wie möglich vor dem Starten des Joins abgefragt wird.
1. Verwenden Sie [Project](./projectoperator.md) , um die Namen der Zeitstempel so zu ändern, dass sowohl die Startzeit als auch die Endzeit in den Ergebnissen angezeigt werden. `project` wählt auch die anderen Spalten aus, die in den Ergebnissen angezeigt werden sollen. 
1. Verwenden Sie [Join](./joinoperator.md) , um die Start-und endeinträge für dieselbe Aktivität abzugleichen. Für jede Aktivität wird eine Zeile erstellt. 
1. Verwenden `project` Sie erneut zum Hinzufügen einer Spalte, um die Dauer der Aktivität anzuzeigen.

So sieht die Ausgabe aus:

|City|SessionID|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>Sitzungen ohne Verwendung einer Sitzungs-ID erhalten

Angenommen, die Start-und Stoppereignisse haben keine Sitzungs-ID, mit der wir vergleichen können. Wir haben jedoch die IP-Adresse des Clients, auf dem die Sitzung stattfindet. Wenn jede Client Adresse jeweils nur eine Sitzung durchführt, können wir jedes Start Ereignis mit dem nächsten Ereignis der gleichen IP-Adresse vergleichen:

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

Der `join` entspricht jeder Startzeit mit allen Endzeiten der gleichen Client-IP-Adresse. Der Beispielcode:

- Entfernt Übereinstimmungen mit früheren Endzeiten.
- Gruppiert nach Startzeit und IP-Adresse, um eine Gruppe für jede Sitzung zu erhalten. 
- Stellt eine `bin` Funktion für den- `StartTime` Parameter bereit. Wenn Sie diesen Schritt nicht ausführen, werden von Kusto automatisch einstündige Container verwendet, die mit den falschen Endzeit Punkten mit einigen Startzeiten zu vergleichen sind.

`arg_min` sucht die Zeile mit der kleinsten Dauer in jeder Gruppe, und der `*` Parameter übergibt alle anderen Spalten. 

Das Argument Präfix `min_` für die einzelnen Spaltennamen. 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="Screenshot einer Tabelle, in der die Ergebnisse aufgelistet sind, mit Spalten für die Startzeit, Client-IP, Dauer, Stadt und früheste Beendigung für jede Kombination aus Client und Startzeit."::: 

Fügen Sie Code hinzu, um die Dauer in Containern mit einfacher Größenordnung zu zählen. In diesem Beispiel teilen Sie aufgrund einer Vorliebe für ein Balkendiagramm durch, `1s` um die Zeiträume in Zahlen zu konvertieren:

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="Screenshot eines Säulen Diagramms, das die Anzahl der Sitzungen mit Dauer in angegebenen Bereichen darstellt.":::

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

Angenommen, Sie verfügen über eine Tabelle mit Aktivitäten und deren Start-und Endzeit. Sie können ein Diagramm anzeigen, das anzeigt, wie viele Aktivitäten parallel ausgeführt werden.

Im folgenden finden Sie eine Beispiel Eingabe mit dem Namen `X` :

|SessionID | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Für ein Diagramm in einminütigen Containern möchten Sie jede laufende Aktivität in jedem einminütigen Intervall zählen.

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

Anstatt diese Arrays beizubehalten, erweitern Sie Sie mithilfe von [MV-Expand](./mvexpandoperator.md):

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

Gruppieren Sie nun die Ergebnisse nach Stichproben Zeit, und zählen Sie die Vorkommen der einzelnen Aktivitäten:

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Verwenden Sie, `todatetime()` weil die [MV-Expand-](./mvexpandoperator.md) Ergebnisse in einer Spalte des dynamischen Typs angezeigt werden.
* Verwenden `bin()` Sie, wenn Sie für numerische Werte und Datumsangaben, wenn Sie kein Intervall angeben, `summarize` immer eine `bin()` Funktion mithilfe eines Standard Intervalls anwenden. 

So sieht die Ausgabe aus:

| count_SessionId | Beispiele|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Sie können ein Balkendiagramm oder ein Zeitdiagramm verwenden, um die Ergebnisse zu erzeugen.

## <a name="introduce-null-bins-into-summarize"></a>NULL-Behälter zusammen *fassen*

Wenn der- `summarize` Operator auf einen Gruppenschlüssel angewendet wird, der aus einer Datums-/uhrzeitspalte besteht, werden diese Werte in einen Container mit fester Breite gruppiert:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

In diesem Beispiel wird eine Tabelle mit einer einzelnen Zeile pro Zeilen Gruppe in erstellt `T` , die in die einzelnen Klassen von fünf Minuten fallen.

Was der Code nicht tut, ist das Hinzufügen von "Null-Containern" – Zeilen für Zeit Korb Werte zwischen `StartTime` und `StopTime` , für die keine entsprechende Zeile in vorhanden ist `T` . Es ist ratsam, die Tabelle mit diesen Containern zu "auffüllen". Dies ist eine Möglichkeit, um dies zu erreichen:

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

Im folgenden finden Sie eine Schritt-für-Schritt-Erläuterung der vorhergehenden Abfrage: 

1. Verwenden Sie den- `union` Operator, um einer Tabelle weitere Zeilen hinzuzufügen. Diese Zeilen werden vom Ausdruck erzeugt `union` .
1. Der- `range` Operator erzeugt eine Tabelle, die eine einzelne Zeile und Spalte enthält. Die Tabelle wird nicht für die Arbeit mit verwendet `mv-expand` .
1. Der `mv-expand` -Operator für die `range` -Funktion erstellt so viele Zeilen, wie fünfminütige Behälter zwischen `StartTime` und vorhanden sind `EndTime` .
1. Verwenden Sie einen `Count` von `0` .
1. Der- `summarize` Operator gruppiert die Klassen vom ursprünglichen (linken oder äußeren) Argument zu `union` . Der Operator wird auch aus dem inneren Argument in den Container (die NULL-bin-Zeilen) eingeordnet. Durch diesen Vorgang wird sichergestellt, dass die Ausgabe eine Zeile pro bin enthält, deren Wert entweder 0 (null) oder die ursprüngliche Anzahl ist.

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Nutzen Sie Kusto mit Machine Learning, um mehr von Ihren Daten zu erfahren 

Viele interessante Anwendungsfälle verwenden Machine Learning-Algorithmen und leiten interessante Einblicke aus Telemetriedaten. Häufig erfordern diese Algorithmen ein streng strukturiertes Dataset als Eingabe. Die Rohdaten des Protokolls entsprechen in der Regel nicht der erforderlichen Struktur und Größe. 

Beginnen Sie mit der Suche nach Anomalien in der Fehlerrate eines bestimmten Dienstanschluss Dienstanbieter-Dienstanbieter. Die Tabelle "Logs" weist 65 Milliarden Datensätze auf. Die folgende grundlegende Abfrage filtert 250.000-Fehler und erstellt dann eine Zeitreihe von Fehler zählungs Funktionen, die die Funktion zur Anomalieerkennung [series_decompose_anomalies](series-decompose-anomaliesfunction.md)verwenden. Die Anomalien werden vom Kusto-Dienst erkannt und als rote Punkte im Zeitreihen Diagramm hervorgehoben.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Der Dienst hat einige Zeit verbuchten mit verdächtigen Fehlerraten identifiziert. Verwenden Sie Kusto, um zu diesem Zeitrahmen zu zoomen. Führen Sie anschließend eine Abfrage aus, die in der Spalte aggregiert `Message` . Versuchen Sie, die häufigsten Fehler zu finden. 

Die relevanten Teile der gesamten Stapel Überwachung der Nachricht werden verkürzt, sodass die Ergebnisse besser auf die Seite passen. 

Sie können eine erfolgreiche Identifizierung der ersten acht Fehler sehen. Der nächste Schritt ist jedoch eine lange Reihe von Fehlern, da die Fehlermeldung mit einer Format Zeichenfolge erstellt wurde, die sich ändernde Daten enthielt:

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|Nachricht
|---|---
|7125|Fehler bei "executealgorithmmethod" für die Methode "runcyclefrominteridata"...
|7125|Inferencehostservice-Rückruf failed..System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt...
|7124|Unerwartetes Rückschluss System error..System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt... 
|5112|Unerwartetes Rückschluss System error..System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt.
|174|Inferencehostservice-Aufruf failed..System. Service Model. CommunicationException: Fehler beim Schreiben in die Pipe:...
|10|Fehler bei "executealgorithmmethod" für die Methode "runcyclefrominteridata"...
|10|Rückschluss System Fehler.. Microsoft. zeichnen. Platform. Inferences. Service. Managers. userinterims datamanagerexception:...
|3|Inferencehostservice-Aufruf failed..System. Service Model. CommunicationObjectFaultedException:...
|1|System Fehler Rückschluss... Socialgraph. Boss. OperationResponse... AIS-traceid: 8292fc561ac64bed8fa243808fe74efd...
|1|System Fehler Rückschluss... Socialgraph. Boss. OperationResponse... AIS-traceid: 5f79f7587ff943ec9b641e02e701afbf...

An diesem Punkt hilft die Verwendung des- `reduce` Operators. Der-Operator hat 63 verschiedene Fehler identifiziert, die am gleichen Ablauf Verfolgungs Instrumentations Punkt im Code entstanden sind. `reduce` hilft bei der Konzentration auf weitere sinnvolle Fehler Ablauf Verfolgungen in diesem Zeitfenster.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Anzahl|Muster
|---|---
|7125|Fehler bei "executealgorithmmethod" für die Methode "runcyclefrominteridata"...
|  7125|Inferencehostservice-Rückruf failed..System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt...
|  7124|Unerwartetes Rückschluss System error..System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt...
|  5112|Unerwartetes Rückschluss System error..System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt...
|  174|Inferencehostservice-Aufruf failed..System. Service Model. CommunicationException: Fehler beim Schreiben in die Pipe:...
|  63|Rückschluss System Fehler.. Microsoft. Print. Platform. Inferences. \* : Write \* zum Schreiben in das Objekt "Boss. \* : socialgraph. Boss. reques..."
|  10|Fehler bei "executealgorithmmethod" für die Methode "runcyclefrominteridata"...
|  10|Rückschluss System Fehler.. Microsoft. zeichnen. Platform. Inferences. Service. Managers. userinterims datamanagerexception:...
|  3|Inferencehostservice-Rückruf failed..System. Service Model. \* : das-Objekt, System. Service Model. Channels. \* + \* , for \* \* ist die \* ... bei Syst...

Nun haben Sie einen guten Überblick über die wichtigsten Fehler, die zu den erkannten Anomalien beigetragen haben.

Beachten Sie Folgendes, um die Auswirkungen dieser Fehler auf das Beispiel System zu verstehen: 
* Die `Logs` Tabelle enthält zusätzliche dimensionale Daten, wie `Component` z `Cluster` . b. und.
* Mit dem neuen Autocluster-Plug-in können Sie Komponenten-und Cluster Einblicke mit einer einfachen Abfrage ableiten. 

Im folgenden Beispiel können Sie deutlich erkennen, dass jeder der ersten vier Fehler für eine Komponente spezifisch ist. Auch wenn die ersten drei Fehler für den db4-Cluster spezifisch sind, tritt der vierte Fehler in allen Clustern auf.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Anzahl |Prozentsatz (%)|Komponente|Cluster|Nachricht
|---|---|---|---|---
|7125|26,64|Inferencehostservice|Db4|Executealgorithmmethod für die Methode...
|7125|26,64|Unbekannte Komponente|Db4|Fehler beim inferencehostservice-Befehl...
|7124|26,64|Inferencealgorithmexecutor|Db4|Unerwarteter Rückschluss System Fehler...
|5112|19,11|Inferencealgorithmexecutor|*|Unerwarteter Rückschluss System Fehler...

## <a name="map-values-from-one-set-to-another"></a>Zuordnen von Werten aus einem Satz zu einem anderen

Ein allgemeiner Abfrage Anwendungsfall ist die statische Zuordnung von Werten. Die statische Zuordnung kann dazu beitragen, dass Ergebnisse Präsentations fähig werden.

Beispielsweise gibt in der nächsten Tabelle `DeviceModel` ein Gerätemodell an. Die Verwendung des Geräte Modells ist nicht die bequeme Art, auf den Gerätenamen zu verweisen.  

|DeviceModel |Anzahl 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

 Die Verwendung eines anzeigen Amens ist bequemer:  

|FriendlyName |Anzahl 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Die folgenden beiden Beispiele veranschaulichen, wie Sie von der Verwendung eines Geräte Modells zu einem anzeigen Amen wechseln, um ein Gerät zu identifizieren.  

### <a name="map-by-using-a-dynamic-dictionary"></a>Zuordnen mithilfe eines dynamischen Wörterbuchs

Die Zuordnung kann mithilfe eines dynamischen Wörterbuchs und dynamischer Accessoren erreicht werden. Beispiel:

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

Sie können die Zuordnung auch mit einer permanenten Tabelle und einem `join` Operator erreichen.
 
1. Erstellen Sie die Mapping-Tabelle nur einmal:

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. Erstellen Sie eine Tabelle mit dem Inhalt des Geräts:

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5, 1 |iPhone 5 
    |iPhone3, 2 |iPhone 4 
    |iPhone7, 2 |iPhone 6 
    |iPhone5, 2 |iPhone5 

1. Erstellen Sie eine Test Tabellen Quelle:

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
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Erstellen und Verwenden von Abfragezeit-Dimensions Tabellen

Häufig möchten Sie die Ergebnisse einer Abfrage mit einer Ad-hoc-Dimensions Tabelle verknüpfen, die nicht in der Datenbank gespeichert ist. Sie können einen Ausdruck definieren, dessen Ergebnis eine Tabelle ist, die auf eine einzelne Abfrage beschränkt ist. 

Beispiel:

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

Im folgenden finden Sie ein etwas komplexeres Beispiel:

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

Angenommen, Sie verfügen über eine Tabelle, die Folgendes enthält:
* Eine `ID` Spalte, die die Entität identifiziert, der die einzelnen Zeilen zugeordnet sind, z. b. eine Benutzer-ID oder eine Knoten-ID.
* Eine `timestamp` Spalte, die den Zeit Verweis für die Zeile bereitstellt.
* Andere Spalten

Sie können den [Top-netsted-Operator](topnestedoperator.md) verwenden, um eine Abfrage zu erstellen, die die letzten beiden Datensätze für jeden Wert der Spalte zurückgibt `ID` , wobei _Latest_ als _der höchste `timestamp` Wert von_ definiert ist:

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


Im folgenden finden Sie eine ausführliche Erläuterung der vorhergehenden Abfrage (die Nummerierung bezieht sich auf die Zahlen in den Code Kommentaren):

1. Der `datatable` ist eine Möglichkeit, Testdaten zu Demonstrationszwecken zu liefern. Normalerweise verwenden Sie hier echte Daten.
1. Dies bedeutet im Wesentlichen, dass _alle unterschiedlichen `id` Werte von zurückgegeben_ werden. 
1. Diese Zeile wird dann für die ersten beiden Datensätze, die maximiert werden, zurückgegeben:
     * Die `timestamp` Spalte
     * Die Spalten der vorhergehenden Ebene (hier, einfach `id` )
     * Die auf dieser Ebene angegebene Spalte (hier, `timestamp` ).
1. Diese Zeile fügt die Werte der- `bla` Spalte für jeden der von der vorangehenden Ebene zurückgegebenen Datensätze hinzu. Wenn die Tabelle andere Spalten enthält, an denen Sie interessiert sind, können Sie diese Zeile für jede dieser Spalten wiederholen.
1. In der letzten Zeile werden die "zusätzlichen" Spalten, die von eingeführt werden, mithilfe des Projekt Entfernungs [Operators](projectawayoperator.md) entfernt `top-nested` .

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>Erweitern einer Tabelle um einen Prozentsatz der Gesamtberechnung

Ein Tabellen Ausdruck, der eine numerische Spalte enthält, ist für den Benutzer nützlicher, wenn er von seinem Wert als Prozentsatz des Gesamtwerts begleitet wird.

Nehmen Sie beispielsweise an, dass eine Abfrage die folgende Tabelle erzeugt:

|Someseries|Someint|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

Sie möchten die Tabelle wie folgt anzeigen:

|Someseries|Someint|P |
|----------|-------|----|
|Apple       |    100|33,3|
|Banana       |    200|66,6|

Wenn Sie die Anzeige der Tabelle ändern möchten, berechnen Sie die Summe (Summe) der `SomeInt` Spalte, und Dividieren Sie dann jeden Wert dieser Spalte durch die Summe. Verwenden Sie für beliebige Ergebnisse den [as-Operator](asoperator.md).

Beispiel:

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

## <a name="perform-aggregations-over-a-sliding-window"></a>Ausführen von Aggregationen in einem gleitenden Fenster

Im folgenden Beispiel wird gezeigt, wie Spalten mithilfe eines gleitenden Fensters zusammengefasst werden. Verwenden Sie für die Abfrage die folgende Tabelle, die die Preise für Früchte nach Zeitstempeln enthält.

Berechnen Sie die Mindest-, höchst-und Summen Kosten der einzelnen Früchte pro Tag mithilfe eines gleitenden Fensters von sieben Tagen. Jeder Datensatz im Resultset aggregiert die vorangegangenen sieben Tage, und die Ergebnisse enthalten einen Datensatz pro Tag im Analysezeitraum.

Frucht Tabelle:

|Timestamp|Frucht|Preis|
|---|---|---|
|2018-09-24 21:00:00.0000000|Bananen|3|
|2018-09-25 20:00:00.0000000|Äpfel|9|
|2018-09-26 03:00:00.0000000|Bananen|4|
|2018-09-27 10:00:00.0000000|Plums|8|
|2018-09-28 07:00:00.0000000|Bananen|6|
|2018-09-29 21:00:00.0000000|Bananen|8|
|2018-09-30 01:00:00.0000000|Plums|2|
|2018-10-01 05:00:00.0000000|Bananen|0|
|2018-10-02 02:00:00.0000000|Bananen|0|
|2018-10-03 13:00:00.0000000|Plums|4|
|2018-10-04 14:00:00.0000000|Äpfel|8|
|2018-10-05 05:00:00.0000000|Bananen|2|
|2018-10-06 08:00:00.0000000|Plums|8|
|2018-10-07 12:00:00.0000000|Bananen|0|

Hier ist die Aggregations Abfrage für das gleitende Fenster. Weitere Informationen finden Sie in der Erläuterung nach dem Abfrageergebnis.

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
|2018-10-01 00:00:00.0000000|Plums|2|8|10|
|2018-10-02 00:00:00.0000000|Bananen|0|8|18|
|2018-10-02 00:00:00.0000000|Plums|2|8|10|
|2018-10-03 00:00:00.0000000|Plums|2|8|14|
|2018-10-03 00:00:00.0000000|Bananen|0|8|14|
|2018-10-04 00:00:00.0000000|Bananen|0|8|14|
|2018-10-04 00:00:00.0000000|Plums|2|4|6|
|2018-10-04 00:00:00.0000000|Äpfel|8|8|8|
|2018-10-05 00:00:00.0000000|Bananen|0|8|10|
|2018-10-05 00:00:00.0000000|Plums|2|4|6|
|2018-10-05 00:00:00.0000000|Äpfel|8|8|8|
|2018-10-06 00:00:00.0000000|Plums|2|8|14|
|2018-10-06 00:00:00.0000000|Bananen|0|2|2|
|2018-10-06 00:00:00.0000000|Äpfel|8|8|8|
|2018-10-07 00:00:00.0000000|Bananen|0|2|2|
|2018-10-07 00:00:00.0000000|Plums|4|8|12|
|2018-10-07 00:00:00.0000000|Äpfel|8|8|8|

Die Abfrage "gestreckt" (dupliziert) jeden Datensatz in der Eingabe Tabelle in den sieben Tagen nach der tatsächlichen Darstellung. Jeder Datensatz wird tatsächlich sieben Mal angezeigt. Folglich umfasst die tägliche Aggregation alle Datensätze der vorangegangenen sieben Tage.


Im folgenden finden Sie eine Schritt-für-Schritt-Erläuterung der vorhergehenden Abfrage: 

1. Jeden Datensatz auf einen Tag (relativ zu `_start` ) einfügen. 
1. Bestimmen Sie das Ende des Bereichs pro Datensatz: `_bin + 7d` , es sei denn, der Wert liegt außerhalb des Bereichs von `_start` und `_end` . in diesem Fall wird er angepasst. 
1. Erstellen Sie für jeden Datensatz ein Array von sieben Tagen (Timestamps), beginnend mit dem Tag des aktuellen Datensatzes. 
1. `mv-expand` Das Array, wodurch jeder Datensatz in sieben Datensätze duplizieren, ein Tag voneinander getrennt. 
1. Führen Sie die Aggregations Funktion für jeden Tag aus. Aufgrund #4 fasst dieser Schritt tatsächlich die _letzten_ sieben Tage zusammen. 
1. Die Daten für die ersten sieben Tage sind unvollständig, da für die ersten sieben Tage kein Zeitraum von sieben Tagen vorhanden ist. Die ersten sieben Tage werden aus dem Endergebnis ausgeschlossen. In diesem Beispiel sind Sie nur in der Aggregation für 2018-10-01 teilnehmen.

## <a name="find-the-preceding-event"></a>Vorheriges Ereignis suchen

Im nächsten Beispiel wird veranschaulicht, wie Sie ein vorhergehenden Ereignis zwischen zwei Datasets finden.  

Sie verfügen über zwei Datasets: A und B. Suchen Sie für jeden Datensatz in DataSet B das vorherige Ereignis in DataSet a (d `arg_max` . h. den Datensatz in einem, der noch _älter_ als B ist).

Im folgenden finden Sie die Beispiel Datasets: 

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

|Timestamp|id|Eventb|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|j|Ay1|
|2019-01-01 00:00:05.0000000|j|Ay2|

</br>

|Timestamp|id|Eventa|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|j|B|
|2019-01-01 00:02:00.0000000|z|B|

Erwartete Ausgabe: 

|id|Timestamp|Eventb|A_Timestamp|Eventa|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|j|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Wir empfehlen zwei unterschiedliche Ansätze für dieses Problem. Sie können beide für ihr bestimmtes Dataset testen, um das zu finden, das für Ihr Szenario am besten geeignet ist.

> [!NOTE] 
> Jeder Ansatz kann für unterschiedliche DataSets unterschiedlich ausgeführt werden.

### <a name="approach-1"></a>Ansatz 1

Bei diesem Ansatz werden Datasets nach ID und Timestamp serialisiert. Anschließend werden alle Ereignisse in DataSet B mit all ihren vorstehenden Ereignissen in DataSet a gruppiert. Schließlich werden `arg_max` alle Ereignisse in DataSet A in der Gruppe ausgewählt.

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

### <a name="approach-2"></a>Ansatz 2

Diese Vorgehensweise zum Lösen des Problems erfordert einen maximalen Such Zeitraum. Der Ansatz prüft, wie viel _älter_ der Datensatz in DataSet a mit DataSet B verglichen werden kann. Die-Methode fügt dann die beiden Datasets basierend auf der ID und diesem loobackzeitraum an.

Das `join` erzeugt alle möglichen Kandidaten, alle Datasets A-Datensätze, die älter sind als Datensätze in DataSet B und innerhalb des Such Zeitraums. Anschließend wird der am nächsten liegenden DataSet B nach gefiltert `arg_min (TimestampB - TimestampA)` . Der kürzerer Zeitraum ist, desto besser sind die Abfrageergebnisse.

Im folgenden Beispiel wird der Such Zeitraum auf festgelegt `1m` . Der Datensatz mit der ID weist `z` kein entsprechendes `A` Ereignis auf, da sein `A` Ereignis um zwei Minuten älter ist.

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

|id|B_Timestamp|A_Timestamp|Eventb|Eventa|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|j|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>Nächste Schritte

- Durchlaufen Sie ein [Tutorial zur Kusto-Abfragesprache](tutorial.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Dieser Artikel identifiziert allgemeine Abfrage Anforderungen in Azure Monitor und erläutert, wie Sie die Kusto-Abfragesprache verwenden können, um Sie zu erfüllen.

## <a name="string-operations"></a>Zeichenfolgenvorgänge

In den folgenden Abschnitten erfahren Sie, wie Sie mit Zeichen folgen arbeiten, wenn Sie die Kusto-Abfragesprache verwenden.

### <a name="strings-and-how-to-escape-them"></a>Zeichen folgen und deren Escapezeichen

Zeichen folgen Werte werden entweder in einfache oder doppelte Anführungszeichen eingeschlossen. Fügen Sie den umgekehrten Schrägstrich ( \\ ) links neben einem Zeichen hinzu, um das Zeichen mit Escapezeichen zu versehen: für `\t` die Tab-Taste, für `\n` die neue Zeile und `\"` für das einfache Anführungszeichen.

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

Operator       |BESCHREIBUNG                         |Groß-/Kleinschreibung|Beispiel (ergibt `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Equals                              |Ja           |`"aBc" == "aBc"`
`!=`           |Not Equals                          |Ja           |`"abc" != "ABC"`
`=~`           |Equals                              |Nein            |`"abc" =~ "ABC"`
`!~`           |Not Equals                          |Nein            |`"aBc" !~ "xyz"`
`has`          |Der Wert auf der rechten Seite ist ein ganzer Begriff in der linken Seite. |Nein|`"North America" has "america"`
`!has`         |Der Rechte Wert ist kein vollständiger Begriff auf der linken Seite.       |Nein            |`"North America" !has "amer"` 
`has_cs`       |Der Wert auf der rechten Seite ist ein ganzer Begriff in der linken Seite. |Ja|`"North America" has_cs "America"`
`!has_cs`      |Der Rechte Wert ist kein vollständiger Begriff auf der linken Seite.       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Der Wert auf der rechten Seite ist ein Begriffs Präfix im Wert der linken Seite.         |Nein            |`"North America" hasprefix "ame"`
`!hasprefix`   |Der Wert auf der rechten Seite ist kein Begriffs Präfix in der linken Seite.     |Nein            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Der Wert auf der rechten Seite ist ein Begriffs Präfix im Wert der linken Seite.         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Der Wert auf der rechten Seite ist kein Begriffs Präfix in der linken Seite.     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Der Wert auf der rechten Seite ist ein Begriff Suffix in der linken Seite.         |Nein            |`"North America" hassuffix "ica"`
`!hassuffix`   |Der Wert der rechten Seite ist kein Begriffs Suffix in der linken Seite.     |Nein            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Der Wert auf der rechten Seite ist ein Begriff Suffix in der linken Seite.         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Der Wert der rechten Seite ist kein Begriffs Suffix in der linken Seite.     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Der Rechte Wert tritt als unter Sequenz des linksseitigen Werts auf.  |Nein            |`"FabriKam" contains "BRik"`
`!contains`    |Der rechtsseitige Wert tritt nicht in der linken Seite auf.           |Nein            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Der Rechte Wert tritt als unter Sequenz des linksseitigen Werts auf.  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Der rechtsseitige Wert tritt nicht in der linken Seite auf.           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Der Wert auf der rechten Seite ist eine anfängliche unter Sequenz des Werts auf der linken Seite.|Nein            |`"Fabrikam" startswith "fab"`
`!startswith`  |Der Rechte Wert ist keine anfängliche unter Sequenz des Werts auf der linken Seite.|Nein        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Der Wert auf der rechten Seite ist eine anfängliche unter Sequenz des Werts auf der linken Seite.|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Der Rechte Wert ist keine anfängliche unter Sequenz des Werts auf der linken Seite.|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Der Wert auf der rechten Seite ist eine schließende unter Sequenz des Werts auf der linken Seite.|Nein             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Der Rechte Wert ist keine schließende unter Sequenz des Werts auf der linken Seite.|Nein         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Der Wert auf der rechten Seite ist eine schließende unter Sequenz des Werts auf der linken Seite.|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Der Rechte Wert ist keine schließende unter Sequenz des Werts auf der linken Seite.|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|Der Wert auf der linken Seite enthält eine Entsprechung für den Wert auf der rechten Seite.|Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Entspricht einem der Elemente       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Entspricht keinem der Elemente   |Ja           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

Zählt Vorkommen einer Teil Zeichenfolge innerhalb einer Zeichenfolge. Kann einfache Zeichen folgen vergleichen oder einen regulären Ausdruck (Regex) verwenden. Einfache Zeichen folgen Übereinstimmungen können sich überlappen, aber Regex-Übereinstimmungen überlappen

```
countof(text, search [, kind])
```

- `text`: Die Eingabe Zeichenfolge 
- `search`: Einfache Zeichenfolge oder Regex für die Suche in Text
- `kind`: _normaler_ regulärer Ausdruck  |  _regex_ (Standard: normal).

Gibt an, wie oft die Such Zeichenfolge im Container abgeglichen werden kann. Einfache Zeichen folgen Übereinstimmungen können sich überlappen, aber Regex-Übereinstimmungen überlappen

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

Ruft eine Entsprechung für einen regulären Ausdruck aus einer bestimmten Zeichenfolge ab. Optional kann die extrahierte Teil Zeichenfolge in den angegebenen Typ konvertieren.

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`: Ein regulärer Ausdruck.
- `captureGroup`: Eine positive ganzzahlige Konstante, die die zu Extra hier gende Erfassungs Gruppe angibt. Verwenden Sie 0 für die gesamte Übereinstimmung, 1 für den Wert, der mit der ersten Klammer \( \) im regulären Ausdruck übereinstimmt, und 2 oder mehr für nachfolgende Klammern.
- `text` : Die zu durchsuchende Zeichenfolge.
- `typeLiteral` : Ein optionales typliteralzeichen (z `typeof(long)` . b.). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert.

Gibt die Teil Zeichenfolge zurück, die mit der angeordneten Erfassungs Gruppe übereinstimmt `captureGroup` , optional konvertiert in `typeLiteral` Wenn keine Entsprechung vorhanden ist oder die Typkonvertierung fehlschlägt, gibt NULL zurück.

Im folgenden Beispiel wird das letzte Oktett von `ComputerIP` aus einem Takt Daten Satz extrahiert:

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Im folgenden Beispiel wird das letzte Oktett extrahiert, in einen *reellen* Typ (Number) umgewandelt und dann der nächste IP-Wert berechnet:

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Im nächsten Beispiel wird die Zeichenfolge `Trace` nach einer Definition von durchsucht `Duration` . Die Entsprechung wird in umgewandelt `real` und mit einer Zeitkonstante (1 s) multipliziert, die dann `Duration` in den Typ umgewandelt wird `timespan` .

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*IsEmpty*, *isnotempty*, *notEmpty*

- `isempty` Gibt zurück, `true` Wenn das Argument eine leere Zeichenfolge oder NULL ist (siehe `isnull` ).
- `isnotempty` Gibt zurück, `true` Wenn das Argument keine leere Zeichenfolge oder NULL ist (siehe `isnotnull` ). Alias: `notempty` .


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


### <a name="parseurl"></a>*Analyse-URL*

Teilt eine URL in ihre Teile auf, wie z. b. Protokoll, Host und Port, und gibt dann ein Dictionary-Objekt zurück, das die Teile als Zeichen folgen enthält.

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

- `regex`: Der reguläre Ausdruck, mit dem abgeglichen werden soll. Sie kann Erfassungs Gruppen in Klammern enthalten \( \) .
- `rewrite`: Der Ersetzungs Ausdruck für jede Übereinstimmung, die durch die Übereinstimmung mit einem regulären Ausdruck erzielt wird. Verwenden Sie "\ 0", um auf die gesamte Entsprechung (\ 1 für die erste Erfassungs Gruppe, \ 2 usw.) für nachfolgende Erfassungs Gruppen zu verweisen.
- `input_text`: Die Eingabe Zeichenfolge, die gesucht werden soll.

Gibt den Text zurück, nachdem alle Übereinstimmungen von Regex durch Auswertungen von Rewrite ersetzt wurden. Bei Übereinstimmungen kommt es nicht zu Überschneidungen.

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

Unterteilt eine bestimmte Zeichenfolge gemäß einem angegebenen Trennzeichen und gibt dann ein Array der resultierenden Teil Zeichenfolgen zurück.

```
split(source, delimiter [, requestedIndex])
```

- `source`: Die Zeichenfolge, die entsprechend dem angegebenen Trennzeichen aufgeteilt werden soll.
- `delimiter`: Das Trennzeichen, das zum Aufteilen der Quell Zeichenfolge verwendet wird.
- `requestedIndex`: Ein optionaler NULL basierter Index. Wenn bereitgestellt, enthält das zurückgegebene Zeichen folgen Array nur dieses Element (sofern vorhanden).


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

Extrahiert eine Teil Zeichenfolge aus einer bestimmten Quell Zeichenfolge, beginnend am angegebenen Index. Optional können Sie die Länge der angeforderten Teil Zeichenfolge angeben.

```
substring(source, startingIndex [, length])
```

- `source`: Die Quell Zeichenfolge, aus der die Teil Zeichenfolge stammt.
- `startingIndex`: Die null basierte anfangs Zeichenposition der angeforderten Teil Zeichenfolge.
- `length`: Ein optionaler Parameter, den Sie verwenden können, um die angeforderte Länge der zurückgegebenen Teil Zeichenfolge anzugeben.

#### <a name="example"></a>Beispiel

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*ToLower*, *ToUpper*

Konvertiert eine bestimmte Zeichenfolge in Kleinbuchstaben oder in Großbuchstaben.

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

In den folgenden Abschnitten erfahren Sie, wie Sie mit Datums-und Uhrzeitwerten arbeiten, wenn Sie die Kusto-Abfragesprache verwenden.

### <a name="date-time-basics"></a>Grundlagen zu Datum/Uhrzeit

Die Kusto-Abfragesprache verfügt über zwei Haupt Datentypen für Datums-und Uhrzeitangaben: `datetime` und `timespan` . Alle Datumsangaben werden in UTC ausgedrückt. Obwohl mehrere Datums-/Uhrzeitformate unterstützt werden, wird das ISO-8601-Format bevorzugt. 

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

Sie können Datums-/Uhrzeitwerte erstellen, indem Sie mithilfe des-Operators eine Zeichenfolge umwandeln `todatetime` . Um beispielsweise die in einem bestimmten Zeitraum gesendeten VM-Takte zu überprüfen, verwenden Sie den- `between` Operator, um einen Zeitbereich anzugeben:

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ein weiteres häufiges Szenario besteht darin, einen Datums-/Uhrzeitwert mit dem aktuellen zu vergleichen. Wenn Sie beispielsweise sämtliche Heartbeats der letzten zwei Minuten anzeigen möchten, können Sie den Operator `now` zusammen mit einem timespan-Wert verwenden, der zwei Minuten darstellt:

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Für diese Funktion ist auch eine Verknüpfung verfügbar:

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Die kürzeste und besser lesbare Methode ist die Verwendung des- `ago` Operators:

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Angenommen, Sie kennen die Startzeit und die Dauer, anstatt die Start-und Endzeiten zu kennen. Sie können die Abfrage neu schreiben:

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>Konvertieren von Zeiteinheiten

Möglicherweise möchten Sie einen Datums-/Uhrzeitwert oder einen TimeSpan-Wert in einer anderen Zeiteinheit als der Standardwert Ausdrücken. Wenn Sie z. b. Fehlerereignisse in den letzten 30 Minuten überprüfen und eine berechnete Spalte benötigen, die anzeigt, wie lange das Ereignis aufgetreten ist, können Sie die folgende Abfrage verwenden:

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Die `timeAgo` Spalte enthält Werte wie `00:09:31.5118992` , die als hh: mm: SS. fffffff formatiert sind. Wenn Sie diese Werte in `number` Minuten seit der Startzeit formatieren möchten, teilen Sie den Wert durch `1m` :

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregationen und Zuordnung von Buckets nach Zeitintervallen

Ein weiteres häufiges Szenario besteht darin, dass Sie Statistiken für einen bestimmten Zeitraum in einer bestimmten Zeiteinheit abrufen müssen. In diesem Szenario können Sie einen `bin` Operator als Teil einer-Klausel verwenden `summarize` .

Verwenden Sie die folgende Abfrage, um die Anzahl der Ereignisse zu erhalten, die während der letzten halben Stunde alle fünf Minuten aufgetreten sind:

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

Eine weitere Möglichkeit zum Erstellen von bucketergebnissen ist die Verwendung von Funktionen wie `startofday` :

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

In den folgenden Abschnitten wird erläutert, wie die Ergebnisse einer Abfrage bei Verwendung der Kusto-Abfragesprache aggregiert werden.

### <a name="count"></a>*count*

Zählen Sie die Anzahl der Zeilen im Resultset, nachdem alle Filter angewendet wurden. Im folgenden Beispiel wird die Gesamtzahl der Zeilen in der- `Perf` Tabelle der letzten 30 Minuten zurückgegeben. Die Ergebnisse werden in einer Spalte mit dem Namen zurückgegeben `count_` , es sei denn, Sie weisen der Spalte einen bestimmten Namen zu:


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

Eine Zeitdiagramm Visualisierung kann nützlich sein, um einen Trend im Lauf der Zeit anzuzeigen:

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

In der Ausgabe dieses Beispiels wird die `Perf` Trendlinie für die Daten Satz Anzahl in fünf-Minuten-Intervallen angezeigt:


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="Screenshot eines Linien Diagramms, das die Trendlinie der Leistungsdaten Satz Anzahl in fünfminütigen Intervallen anzeigt.":::

### <a name="dcount-dcountif"></a>*DCount*, *dantif*

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

### <a name="evaluate-subgroups"></a>Untergruppen auswerten

Verwenden Sie das Schlüsselwort `by`, um eine Zählung oder Aggregationen auf Untergruppen in Ihren Daten auszuführen. Verwenden Sie beispielsweise die folgende Abfrage, um die Anzahl von unterschiedlichen Linux-Computern zu zählen, die Takte in den einzelnen Ländern oder Regionen gesendet haben:

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
|United Kingdom   | 0                   |
|Niederlande      | 2                   |


Fügen Sie dem Abschnitt Spaltennamen hinzu, um noch kleinere Untergruppen Ihrer Daten zu analysieren `by` . Beispielsweise kann es vorkommen, dass Sie die einzelnen Computer pro Land oder Region pro Betriebs Systemtyp () zählen `OSType` :

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

Sie können auch unterschiedliche Quantilen angeben, um ein aggregiertes Ergebnis für jedes Ergebnis zu erhalten:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Die Ergebnisse zeigen möglicherweise, dass einige Computer CPUs ähnliche Median Werte aufweisen. Obwohl sich einige Computer auf dem Median befinden, haben andere Benutzer viel niedrigere CPU-Werte gemeldet. Der höchst-und Tiefstwert bedeutet, dass auf den Computern Spitzen auftreten.

### <a name="variance"></a>Variance

Um die Varianz eines Werts direkt zu evaluieren, verwenden Sie die Standardabweichung und Varianzmethoden:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Eine gute Möglichkeit, die Stabilität der CPU-Auslastung zu analysieren, ist die Kombination `stdev` mit der Median Berechnung:

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>Generieren von Listen und Sätzen

Sie können verwenden `makelist` , um Daten nach der Reihenfolge der Werte in einer bestimmten Spalte zu pivotieren. Beispielsweise können Sie die am häufigsten vorkommenden Bestell Ereignisse untersuchen, die auf ihren Computern stattfinden. Im Wesentlichen können Sie die Daten nach der Reihenfolge der `EventID` Werte auf den einzelnen Computern pivotieren: 

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

`makelist` generiert eine Liste in der Reihenfolge, in der Daten an sie übergeben wurden. Verwenden Sie `asc` in der- `order` Anweisung anstelle von, um Ereignisse von der ältesten zu den neuesten zu sortieren `desc` . 

Es kann sinnvoll sein, eine Liste nur unterschiedlicher Werte zu erstellen. Diese Liste wird als _Satz_ bezeichnet und kann mit dem folgenden Befehl generiert werden `makeset` :

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

Wie `makelist` `makeset` funktioniert auch mit geordneten Daten. Der `makeset` Befehl generiert Arrays basierend auf der Reihenfolge der Zeilen, die an die Zeile weitergegeben werden.

### <a name="expand-lists"></a>Listen erweitern

Der umgekehrte Vorgang von `makelist` oder `makeset` ist `mv-expand` . Der `mv-expand` Befehl erweitert eine Liste von Werten in getrennte Zeilen. Sie kann über eine beliebige Anzahl dynamischer Spalten, einschließlich JSON-und Array Spalten, erweitert werden. Beispielsweise können Sie die `Heartbeat` Tabelle auf Lösungen prüfen, die Daten von Computern gesendet haben, von denen in der letzten Stunde ein Takt gesendet wurde:

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

Verwenden `mv-expand` Sie, um jeden Wert in einer separaten Zeile anstelle einer durch Trennzeichen getrennten Liste anzuzeigen:

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


Mit können Sie `makelist` Elemente gruppieren. In der Ausgabe wird die Liste der Computer pro Lösung angezeigt:

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

### <a name="missing-bins"></a>Fehlende Container

Bei einer nützlichen Anwendung von werden `mv-expand` Standardwerte für fehlende Behälter ausgefüllt. Nehmen wir beispielsweise an, dass Sie die Betriebszeit eines bestimmten Computers durch Untersuchen des Heartbeat suchen. Außerdem soll die Quelle des Heartbeat angezeigt werden, die in der Spalte angezeigt wird `Category` . Normalerweise verwenden wir eine einfache `summarize` Anweisung:

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

In der Ausgabe fehlt der Bucket, der "2017-06-06t19:00:00Z" zugeordnet ist, da keine Takt Daten für diese Stunde vorhanden sind. Mit der Funktion `make-series` können Sie leeren Buckets einen Standardwert zuweisen. Für jede Kategorie wird eine Zeile generiert. Die Ausgabe beinhaltet zwei zusätzliche Array Spalten, eine für Werte und eine für den passenden Zeitrahmen:

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

So sieht die Ausgabe aus:

| Category | count_ | TimeGenerated |
|---|---|---|
| Direkt-Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Das dritte Element des *count_* Arrays ist wie erwartet 0 (null). Das _TimeGenerated_ -Array verfügt über einen übereinstimmenden Zeitstempel von "2017-06-06t19:00:00.0000000 z". Dieses Array Format ist jedoch schwierig zu lesen. Verwenden Sie `mv-expand`, um die Arrays zu erweitern und die gleiche Formatausgabe zu generieren, die von `summarize` generiert wurde:

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



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Schmale Ergebnisse in eine Gruppe von Elementen: *Let*, *makeset*, *chanar*, *in*

Ein gängiges Szenario besteht darin, die Namen bestimmter Entitäten auf Grundlage eines kriteriensatzes auszuwählen und dann ein anderes Dataset auf diese Entitätenmenge zu filtern. Beispielsweise kann es vorkommen, dass Computer, für die bekanntermaßen Updates vorhanden sind, über fehlende Updates verfügen und die IP-Adressen identifizieren, für die diese Computer aufgerufen wurden.

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

Sie können Joins verwenden, um Daten aus mehreren Tabellen in derselben Abfrage zu analysieren. Bei einem Join werden die Zeilen zweier Datasets mit den Werten der angegebenen Spalten zusammengeführt.

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

Im Beispiel filtert das erste Dataset alle Anmelde Ereignisse. Dieses DataSet wird mit einem zweiten DataSet verknüpft, das nach allen Abmelde Ereignissen filtert. Die projizierten Spalten lauten `Computer` , `Account` , `TargetLogonId` und `TimeGenerated` . Die Datasets werden durch eine freigegebene Spalte () korreliert `TargetLogonId` . Bei der Ausgabe handelt es sich um einen einzelnen Datensatz pro Korrelation mit der Anmelde-und Abmelde Zeit.

Wenn beide Datasets Spalten mit demselben Namen aufweisen, erhalten die Spalten des rechtsseitigen Datasets eine Indexnummer. In diesem Beispiel werden die Ergebnisse `TargetLogonId` mit Werten aus der linksseitigen Tabelle und `TargetLogonId1` mit Werten aus der rechten Tabelle angezeigt. In diesem Fall wurde die zweite `TargetLogonId1` Spalte mithilfe des-Operators entfernt `project-away` .

> [!NOTE]
> Um die Leistung zu verbessern, behalten Sie mithilfe des-Operators nur die relevanten Spalten der verbundenen Datasets `project` .


Verwenden Sie die folgende Syntax, um zwei Datasets zu verknüpfen, in denen der verknüpfte Schlüssel einen anderen Namen zwischen den beiden Tabellen hat:

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Nachschlage Tabellen

Joins werden häufig verwendet, um die `datatable` Zuordnung statischer Werte zu verwenden. Die Verwendung von `datatable` kann zu einer ausführlicheren Darstellung von Ergebnissen führen. Beispielsweise können Sie Sicherheits Ereignisdaten mit dem Ereignis Namen für jede Ereignis-ID erweitern:

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
| Das Handle für ein Objekt wurde geschlossen. | 290.995 |
| Ein Handle für ein Objekt wurde angefordert. | 154.157 |
| Es wurde versucht, ein Handle für ein Objekt zu duplizieren. | 144.305 |
| Es wurde versucht, auf ein Objekt zuzugreifen. | 123.669 |
| Kryptografischer Vorgang | 153.495 |
| Schlüsseldatei Vorgang | 153.496 |

## <a name="json-and-data-structures"></a>JSON und Datenstrukturen

Bei den-Objekten handelt es sich um Objekte, die andere Objekte in einem Array oder in einer Zuordnung von Schlüssel-Wert-Paaren enthalten. Die Objekte werden als JSON-Zeichen folgen dargestellt. In diesem Abschnitt wird beschrieben, wie sie JSON zum Abrufen von Daten und Analysieren von geschposteten Objekten verwenden können.

### <a name="work-with-json-strings"></a>Arbeiten mit JSON-Zeichen folgen

Greifen Sie mit `extractjson` auf ein bestimmtes JSON-Element in einem bekannten Pfad zu. Diese Funktion erfordert einen Pfad Ausdruck, der die folgenden Konventionen verwendet:

- Verwenden _$_ Sie, um auf den Stamm Ordner zu verweisen.
- Verwenden Sie die Klammer- oder Punktnotation, um wie in den folgenden Beispielen dargestellt auf Indizes und Elemente zu verweisen.


Verwenden Sie Klammern für Indizes und Punkte zum Trennen von Elementen:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Dieses Beispiel ähnelt, verwendet jedoch nur die Notation "eckige Klammern":

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Nur für ein Element können Sie nur die Punkt Notation verwenden:

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

Es ist am einfachsten, auf mehrere Elemente in ihrer JSON-Struktur als dynamisches Objekt zuzugreifen. Wandeln Sie die Textdaten mit `parsejson` in ein dynamisches Objekt um. Nachdem Sie die JSON-Datei in einen dynamischen Typ konvertiert haben, können Sie zusätzliche Funktionen verwenden, um die Daten zu analysieren.

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

Verwenden `mv-expand` Sie, um die Eigenschaften eines Objekts in separate Zeilen zu unterteilen:

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

Das Schema beschreibt die Namen der Objekt Felder und deren übereinstimmenden Datentypen. 

Bei den Sch-Objekten kann es sich um unterschiedliche Schemas wie im folgenden Beispiel:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Diagramme

In den folgenden Abschnitten werden Beispiele für das Arbeiten mit Diagrammen bei Verwendung der Kusto-Abfragesprache beschrieben.

### <a name="chart-the-results"></a>Diagramm Ergebnisse

Überprüfen Sie zunächst die Anzahl der Computer pro Betriebssystem in der letzten Stunde:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Standardmäßig werden die Ergebnisse als Tabelle angezeigt:

:::image type="content" source="images/samples/query-results-table.png" alt-text="Screenshot, der Abfrageergebnisse in einer Tabelle anzeigt.":::

Wählen Sie für eine nützlichere Ansicht **Diagramm** aus, und wählen Sie dann die Option Kreis aus, **um die Ergebnisse** anzuzeigen:

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="Screenshot, der Abfrageergebnisse in einem Kreis Diagramm anzeigt.":::

### <a name="timecharts"></a>Zeitdiagramme

Zeigen Sie den Durchschnitt und die 50. und 95. Quantile der Prozessorzeit in Containern von einer Stunde an. 

Mit der folgenden Abfrage werden mehrere Reihen generiert. In den Ergebnissen können Sie auswählen, welche Reihe im Zeitdiagramm angezeigt werden soll.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wählen Sie die Anzeigeoption für das **Linien** Diagramm aus:

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="Screenshot, der ein Liniendiagramm mit mehreren Reihen anzeigt.":::

#### <a name="reference-line"></a>Bezugslinie

Mithilfe einer Bezugslinie können Sie leicht erkennen, ob die Metrik einen bestimmten Schwellenwert überschritten hat. Um einem Diagramm eine Linie hinzuzufügen, erweitern Sie das Dataset durch Hinzufügen einer Konstanten Spalte:

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="Screenshot, der ein Liniendiagramm mit mehreren Reihen mit einer Schwellenwert-Verweis Zeile anzeigt.":::


### <a name="multiple-dimensions"></a>Mehrere Dimensionen

Mehrere Ausdrücke in der- `by` Klausel von `summarize` erstellen mehrere Zeilen in den Ergebnissen. Für jede Kombination von Werten wird eine Zeile erstellt.

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Wenn Sie die Ergebnisse als Diagramm anzeigen, verwendet das Diagramm die erste Spalte aus der- `by` Klausel. Das folgende Beispiel zeigt ein gestapeltes Säulendiagramm, das mit dem-Wert erstellt wird `EventID` . Dimensionen müssen vom Typ sein `string` . In diesem Beispiel wird der `EventID` Wert in umgewandelt `string` :

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="Screenshot, der ein Balkendiagramm auf der Grundlage der EventId-Spalte anzeigt.":::

Sie können zwischen Spalten umschalten, indem Sie den Dropdown Pfeil für den Spaltennamen auswählen:

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="Screenshot, der ein Balkendiagramm auf der Grundlage der AccountType-Spalte anzeigt, wobei die Spaltenauswahl sichtbar ist.":::

## <a name="smart-analytics"></a>Intelligente Analysen

Dieser Abschnitt enthält Beispiele für die Verwendung von intelligenten Analysefunktionen in Azure Log Analytics, um die Benutzeraktivität zu analysieren. Sie können diese Beispiele verwenden, um Ihre eigenen Anwendungen zu analysieren, die durch Azure-Anwendung Insights überwacht werden, oder die Konzepte in diesen Abfragen für eine ähnliche Analyse anderer Daten verwenden. 

### <a name="cohorts-analytics"></a>Kohortenanalysen

Bei der Kohorte-Analyse werden die Aktivitäten bestimmter Gruppen von Benutzern nachverfolgt, die als _Kohorten_ bezeichnet werden. Die Analyse der Kohorte versucht, zu messen, wie ansprechend ein Dienst ist, indem die Rate der zurückgegebenen Benutzer gemessen wird. Benutzer werden nach dem Zeitpunkt gruppiert, zu dem Sie den Dienst zum ersten Mal verwendet haben. Beim Analysieren von Kohorten erwarten wir eine Abnahme der Aktivität gegenüber den ersten nachverfolgten Zeiträumen. Jede Kohorte wird nach der Woche benannt, in der ihre Mitglieder zum ersten Mal identifiziert wurden.

Im folgenden Beispiel wird die Anzahl der Aktivitäten analysiert, die Benutzer seit fünf Wochen nach der ersten Verwendung des-Dienstanbieter abgeschlossen haben:

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

:::image type="content" source="images/samples/cohorts-table.png" alt-text="Screenshot, der eine Tabelle mit Kohorten auf der Grundlage von Aktivitäten anzeigt.":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Neue monatlich aktive Benutzer und Benutzerbindung

Im folgenden Beispiel wird die Zeitreihenanalyse mit der [series_fir](/azure/kusto/query/series-firfunction) -Funktion verwendet. Sie können die- `series_fir` Funktion für das Verschieben von Fenster Berechnungen verwenden. Die überwachte Beispielanwendung ist ein Onlineshop, der die Aktivität der Benutzer über benutzerdefinierte Ereignisse nachverfolgt. Mit der Abfrage werden zwei Arten von Benutzeraktivitäten nachverfolgt: `AddToCart` und `Checkout` . Es definiert einen aktiven Benutzer als Benutzer, der ein Auschecken mindestens einmal an einem bestimmten Tag abgeschlossen hat.

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

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="Screenshot eines Diagramms, das aktive aktive Benutzer nach Tag über einen Monat anzeigt":::

Im folgenden Beispiel wird die vorherige Abfrage in eine wiederverwendbare Funktion umgewandelt. Im Beispiel wird dann die Abfrage verwendet, um die Benutzerfreundlichkeit zu berechnen. Ein aktiver Benutzer in dieser Abfrage ist als ein Benutzer definiert, der ein Auschecken mindestens einmal an einem bestimmten Tag abgeschlossen hat.

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

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="Screenshot eines Diagramms, das die Benutzer Bindung im Zeitverlauf anzeigt.":::

### <a name="regression-analysis"></a>Regressionsanalyse

Dieses Beispiel zeigt, wie Sie eine automatisierte Erkennung für Dienstunterbrechungen erstellen, die ausschließlich auf den Ablaufverfolgungsprotokollen einer Anwendung basiert. Das Erkennungs Modul sucht ungewöhnliche, plötzliche Zunahme der relativen Menge an Fehler-und Warnungs Ablauf Verfolgungen in der Anwendung.

Für die Auswertung des Dienststatus basierend auf Überwachungsprotokolldaten kommen zwei Verfahren zum Einsatz:

- Mit [make-series](/azure/kusto/query/make-seriesoperator) werden teilweise strukturierte Ablaufverfolgungsprotokolle im Textformat in eine Metrik konvertiert, die das Verhältnis zwischen positiven und negativen Ablaufverfolgungseinträgen darstellt.
- Verwenden Sie [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) und [series_fit_line](/azure/kusto/query/series-fit-linefunction) für die erweiterte Schritt Sprung Erkennung mithilfe der Zeitreihenanalyse mit einer zweizeiligen linearen Regression.

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

- Durchlaufen Sie ein [Tutorial zur Kusto-Abfragesprache](tutorial.md?pivots=azuremonitor).


::: zone-end

