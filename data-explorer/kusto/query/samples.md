---
title: 'Beispiele: Azure-Daten-Explorer'
description: In diesem Artikel werden die Beispiele in Azure Daten-Explorer beschrieben.
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
ms.openlocfilehash: b448f4249c777d9b9d61e58dad993f3da1817fda
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512467"
---
# <a name="samples"></a>Beispiele

::: zone pivot="azuredataexplorer"

Im folgenden finden Sie einige häufige Abfrage Anforderungen und wie die Kusto-Abfragesprache verwendet werden kann, um Sie zu erfüllen.

## <a name="display-a-column-chart"></a>Anzeigen eines Säulen Diagramms

Projizieren Sie mindestens zwei Spalten, und verwenden Sie Sie als x-und y-Achse eines Diagramms.

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* Die erste Spalte bildet die x-Achse. Dies kann numerisch, DateTime oder String sein. 
* Verwenden `where` `summarize` Sie, und, `top` um die Datenmenge einzuschränken, die Sie anzeigen.
* Sortieren Sie die Ergebnisse, um die Reihenfolge der x-Achse zu definieren.

:::image type="content" source="images/samples/060.png" alt-text="Screenshot eines Säulen Diagramms. Die y-Achse liegt zwischen 0 und ungefähr 50. Zehn farbige Spalten stellen die entsprechenden Werte von 10 Standorten dar.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Abrufen von Sitzungen aus Start- und Stop-Ereignissen

Angenommen, Sie haben ein Ereignisprotokoll. Einige Ereignisse markieren den Anfang oder das Ende einer erweiterten Aktivität oder Sitzung. 

|Name|City|SessionID|Timestamp|
|---|---|---|---|
|Start|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Start|Manchester|4267667|2015-12-09T10:14:02.23|
|Beenden|London|2817330|2015-12-09T10:23:43.18|
|Abbrechen|Manchester|4267667|2015-12-09T10:27:26.29|
|Beenden|Manchester|4267667|2015-12-09T10:28:31.72|

Jedes Ereignis hat eine SessionID. Das Problem besteht darin, die Start-und Stoppereignisse mit der gleichen ID zu vergleichen.

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

1. Verwenden [`let`](./letstatement.md) Sie, um eine Projektion der Tabelle so weit wie möglich zu benennen, bevor Sie in den Join wechseln.
1. Verwenden [`Project`](./projectoperator.md) Sie, um die Namen der Zeitstempel so zu ändern, dass sowohl die Start-als auch die Endzeit im Ergebnis angezeigt werden können. 
   Außerdem wählt er die anderen Spalten aus, die im Ergebnis angezeigt werden sollen. 
1. Verwenden [`join`](./joinoperator.md) Sie, um die Start-und endeinträge für die gleiche Aktivität zu vergleichen und eine Zeile für jede Aktivität zu erstellen. 
1. Mit `project` wird schließlich erneut eine Spalte hinzugefügt, um die Dauer der Aktivität anzuzeigen.


|City|SessionID|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>Sitzungen ohne Sitzungs-ID erhalten

Angenommen, die Start-und Stoppereignisse haben keine Sitzungs-ID, mit der wir vergleichen können. Sie haben jedoch eine IP-Adresse des Clients, auf dem die Sitzung stattgefunden hat. Vorausgesetzt, für jede Client-Adresse wird nur jeweils eine Sitzung durchführt, dann können wir jedes Start-Ereignis dem nächsten Stop-Ereignis derselben IP-Adresse zuordnen.

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

Das Join ordnet jede Startzeit mit allen Endzeiten von der gleichen Client-IP-Adresse zu. 
1. Entfernen Sie Übereinstimmungen mit früheren Endzeiten.
1. Gruppieren Sie nach Startzeit und IP, um eine Gruppe für jede Sitzung zu erhalten. 
1. Stellen Sie eine `bin` Funktion für den startTime-Parameter bereit. Wenn Sie dies nicht tun, verwendet Kusto automatisch 1-Stunden-Behälter, die einigen Startzeiten mit den falschen Endzeit Punkten entsprechen.

`arg_min` wählt die Zeile mit der kleinsten Dauer in jeder Gruppe aus, und der `*` Parameter übergibt alle anderen Spalten. Das Argument Präfix "min_" für die einzelnen Spaltennamen. 

:::image type="content" source="images/samples/040.png" alt-text="Eine Tabelle mit den Ergebnissen mit Spalten für die Startzeit, Client I P, Duration, City und früheste Beendigung für jede Kombination aus Client-und Startzeit."::: 

Fügen Sie Code hinzu, um die Dauer in Containern mit einfacher Größenordnung zu zählen. Wenn in diesem Beispiel ein Balkendiagramm bevorzugt wird, teilen Sie durch, `1s` um die Zeiträume in Zahlen zu konvertieren. 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="Säulendiagramm, das die Anzahl der Sitzungen mit Dauer in angegebenen Bereichen darstellt. Mehr als 400 Sitzungen dauerten 10 Sekunden. Weniger als 100 waren 290 Sekunden.":::

### <a name="real-example"></a>Beispiel aus der Praxis

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

Angenommen, Sie verfügen über eine Tabelle mit Aktivitäten, deren Start-und Endzeit ist. Zeigen Sie im Laufe der Zeit ein Diagramm an, in dem angezeigt wird, wie viele Aktivitäten gleichzeitig ausgeführt werden.

Im folgenden finden Sie eine Beispiel Eingabe mit dem Namen `X` .

|SessionID | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Erstellen Sie für ein Diagramm in 1-Minuten-Containern etwas, das bei jedem 1-Minuten-Intervall eine Anzahl für jede laufende Aktivität gibt.

Hier ist ein Zwischenergebnis.

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` generiert ein Array von Werten in den angegebenen Intervallen.

|SessionID | StartTime | StopTime  | Beispiele|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Anstatt diese Arrays beizubehalten, erweitern Sie Sie mithilfe von [MV-Expand](./mvexpandoperator.md).

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

Gruppieren Sie diese nun nach Stichproben Zeit, wobei Sie die Vorkommen der einzelnen Aktivitäten zählen.

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Verwenden Sie "DateTime ()", da " [MV-Expand](./mvexpandoperator.md) " eine Spalte vom Typ "Dynamic" ergibt.
* Verwenden Sie bin (), da für numerische Werte und Datumsangaben zusammenfassen immer eine bin-Funktion mit einem Standardintervall anwendet, wenn Sie keines angeben. 


| count_SessionId | Beispiele|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Die Ergebnisse können als Balkendiagramm oder Zeitdiagramm gerendert werden.

## <a name="introduce-null-bins-into-summarize"></a>NULL-Behälter zusammenfassen

Wenn der- `summarize` Operator auf einen Gruppenschlüssel angewendet wird, der aus einer `datetime` Spalte besteht, "bin" diese Werte in einen Container mit fester Breite.

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Im obigen Beispiel wird eine Tabelle mit einer einzelnen Zeile pro Gruppe von Zeilen in erzeugt `T` , die in jede Gruppe von fünf Minuten fallen. Dies bedeutet nicht, dass "Null-Behälter"--Zeilen für Zeit-/Uhrzeitwerte zwischen `StartTime` und `StopTime` vorhanden sind, für die keine entsprechende Zeile in vorhanden ist `T` . 

Es ist wünschenswert, die Tabelle mit diesen Containern zu "auffüllen". Dies ist eine Möglichkeit, dies zu tun.

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

Im folgenden finden Sie eine Schritt-für-Schritt-Erläuterung der obigen Abfrage: 

1. Mit dem- `union` Operator können Sie einer Tabelle zusätzliche Zeilen hinzufügen. Diese Zeilen werden vom Ausdruck erzeugt `union` .
1. Der `range` -Operator erzeugt eine Tabelle mit einer einzelnen Zeile/Spalte.
   Die Tabelle wird nicht für die Verwendung durch verwendet `mv-expand` , um an zu arbeiten.
1. Der `mv-expand` -Operator für die `range` -Funktion erstellt so viele Zeilen, wie zwischen und fünf Minuten lang sind `StartTime` `EndTime` .
1. Verwenden Sie einen `Count` von `0` .
1. Der- `summarize` Operator gruppiert die Klassen vom ursprünglichen (linken oder äußeren) Argument zu `union` . Der Operator wird auch aus dem inneren Argument in den Container (die NULL-bin-Zeilen) eingeordnet. Durch diesen Vorgang wird sichergestellt, dass die Ausgabe eine Zeile pro bin enthält, deren Wert entweder 0 (null) oder die ursprüngliche Anzahl ist.  

## <a name="get-more-out-of-your-data-in-kusto-with-machine-learning"></a>Bringen Sie Ihre Daten in Kusto mit den Machine Learning 

Es gibt viele interessante Anwendungsfälle, die Machine Learning-Algorithmen nutzen und interessante Einblicke in die Telemetriedaten ableiten. Häufig erfordern diese Algorithmen ein sehr strukturiertes Dataset als Eingabe. Die Rohdaten für das Protokoll entsprechen in der Regel nicht der erforderlichen Struktur und Größe. 

Beginnen Sie mit der Suche nach Anomalien in der Fehlerrate eines bestimmten Dienstanschluss Dienstanbieter-Dienstanbieter. In der Tabelle Logs sind 65 b-Einträge enthalten. Durch die unten aufgeführte einfache Abfrage werden 250 KB-Fehler gefiltert, und es werden Zeitreihendaten der Fehler Anzahl erstellt, in denen die Funktion zur Anomalieerkennung [series_decompose_anomalies](series-decompose-anomaliesfunction.md) Die Anomalien werden vom Kusto-Dienst erkannt und als rote Punkte im Zeitreihen Diagramm hervorgehoben.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Der Dienst hat einige Zeitrahmen mit verdächtiger Fehlerrate identifiziert. Verwenden Sie Kusto, um diesen Zeitrahmen zu vergrößern, und führen Sie eine Abfrage aus, die in der Spalte "Message" aggregiert. Versuchen Sie, die häufigsten Fehler zu finden. 

Die relevanten Teile der gesamten Stapel Überwachung der Nachricht werden abgeschnitten, damit Sie besser auf die Seite passen. 

Sie können sehen, wie die ersten acht Fehler erfolgreich identifiziert wurden. Es folgt jedoch eine lange Reihe von Fehlern, da die Fehlermeldung durch eine Format Zeichenfolge erstellt wurde, die sich ändernde Daten enthielt. 

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

An dieser Stelle hilft der- `reduce` Operator. Der-Operator hat 63 verschiedene Fehler identifiziert, die vom gleichen Ablauf Verfolgungs Instrumentations Punkt im Code verursacht wurden, und hilft dabei, sich auf weitere sinnvolle Fehler Ablauf Verfolgungen in diesem Zeitfenster zu konzentrieren.

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
|  5112|Unerwartetes Rückschluss System error..System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt.
|  174|Inferencehostservice-Aufruf failed..System. Service Model. CommunicationException: Fehler beim Schreiben in die Pipe:...
|  63|Rückschluss System Fehler.. Microsoft. Print. Platform. Inferences. \* : Write \* zum Schreiben in das Objekt "Boss. \* : socialgraph. Boss. reques..."
|  10|Fehler bei "executealgorithmmethod" für die Methode "runcyclefrominteridata"...
|  10|Rückschluss System Fehler.. Microsoft. zeichnen. Platform. Inferences. Service. Managers. userinterims datamanagerexception:...
|  3|Inferencehostservice-Rückruf failed..System. Service Model. \* : das-Objekt, System. Service Model. Channels. \* + \* , for \* \* ist die \* ...   bei Syst...

Nun haben Sie einen guten Überblick über die wichtigsten Fehler, die zu den erkannten Anomalien beigetragen haben.

So verstehen Sie die Auswirkungen dieser Fehler auf das Beispiel System: 
* Die Tabelle "Logs" enthält zusätzliche dimensionale Daten, z. b. "Component", "Cluster" usw.
* Mit dem neuen Plug-in "Autocluster" können Sie diesen Einblick mit einer einfachen Abfrage ableiten. 
* Im folgenden Beispiel können Sie deutlich erkennen, dass jeder der ersten vier Fehler für eine Komponente spezifisch ist. Auch wenn die drei wichtigsten Fehler für den db4-Cluster spezifisch sind, geschieht der vierte in allen Clustern.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Anzahl |Prozent (%)|Komponente|Cluster|Nachricht
|---|---|---|---|---
|7125|26,64|Inferencehostservice|Db4|Executealgorithmmethod für die-Methode...
|7125|26,64|Unbekannte Komponente|Db4|Fehler beim inferencehostservice-Befehl....
|7124|26,64|Inferencealgorithmexecutor|Db4|Unerwarteter Rückschluss System Fehler...
|5112|19,11|Inferencealgorithmexecutor|*|Unerwarteter Rückschluss System Fehler... 

## <a name="map-values-from-one-set-to-another"></a>Zuordnen von Werten aus einem Satz zu einem anderen

Ein häufiger Anwendungsfall ist die statische Zuordnung von Werten, die dazu beitragen können, Ergebnisse besser darstellbar zu machen.
Sehen Sie sich beispielsweise die nächste Tabelle an. 
`DeviceModel` Gibt ein Modell des Geräts an, das keine sehr bequeme Form der Referenzierung des Geräte namens ist.  


|DeviceModel |Anzahl 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

  
Im folgenden finden Sie eine bessere Darstellung.  

|FriendlyName |Anzahl 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Die beiden folgenden Ansätze veranschaulichen, wie die Darstellung erreicht werden kann.  

### <a name="mapping-using-dynamic-dictionary"></a>Zuordnung mithilfe des dynamischen Wörterbuchs

Der Ansatz zeigt die Zuordnung mit einem dynamischen Wörterbuch und dynamischen Accessoren.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Data set definition
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

### <a name="map-using-static-table"></a>Zuordnen mithilfe statischer Tabellen

Der Ansatz zeigt die Zuordnung mit einer permanenten Tabelle und einem Joinoperator.
 
Erstellen Sie die Mapping-Tabelle nur einmal.

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Inhalt von Geräten jetzt.

|DeviceModel |FriendlyName 
|---|---
|iPhone5, 1 |iPhone 5 
|iPhone3, 2 |iPhone 4 
|iPhone7, 2 |iPhone 6 
|iPhone5, 2 |iPhone5 

Verwenden Sie für das Erstellen einer Test Tabellen Quelle denselben Trick.

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```

Join und Project.

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

Ergebnis:

|FriendlyName |Anzahl 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Erstellen und Verwenden von Abfragezeit-Dimensions Tabellen

Häufig möchten Sie die Ergebnisse einer Abfrage mit einer Ad-hoc-Dimensions Tabelle verknüpfen, die nicht in der Datenbank gespeichert ist. Es ist möglich, einen Ausdruck zu definieren, dessen Ergebnis eine Tabelle ist, die auf eine einzelne Abfrage beschränkt ist. Beispiel:

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

Im folgenden finden Sie ein etwas komplexeres Beispiel.

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
* eine `ID` Spalte, die die Entität identifiziert, der die einzelnen Zeilen zugeordnet sind, z. b. eine Benutzer-ID oder eine Knoten-ID.
* eine `timestamp` Spalte, die den Zeit Verweis für die Zeile bereitstellt.
* andere Spalten

Eine Abfrage, die die letzten beiden Datensätze für jeden Wert der `ID` Spalte zurückgibt, wobei "Latest" als "mit dem höchsten Wert von" definiert ist, `timestamp` kann mit dem [Top-netsted-Operator](topnestedoperator.md)erstellt werden.

Beispiel:

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

Die nachstehend aufgeführten Hinweise beziehen sich ganz rechts auf Zahlen im Codebeispiel.

1. Der `datatable` ist eine Möglichkeit, Testdaten zu Demonstrationszwecken zu liefern. Normalerweise verwenden Sie hier echte Daten.
1. Diese Zeile bedeutet im Wesentlichen, dass alle unterschiedlichen Werte von zurückgegeben werden `id` .
1. Diese Zeile wird dann für die ersten beiden Datensätze, die maximiert werden, zurückgegeben:
     * die `timestamp` Spalte
     * die Spalten der vorherigen Ebene (hier, einfach `id` )
     * die auf dieser Ebene angegebene Spalte (hier, `timestamp` ).
1. Diese Zeile fügt die Werte der `bla` Spalte für jeden der von der vorherigen Ebene zurückgegebenen Datensätze hinzu. Wenn die Tabelle andere relevante Spalten enthält, können Sie diese Zeile für jede dieser Spalten wiederholen.
1. Diese letzte Zeile verwendet den [Projekt entfernten Operator](projectawayoperator.md) , um die "zusätzlichen" Spalten zu entfernen, die mit eingeführt wurden `top-nested` .

## <a name="extend-a-table-with-some-percent-of-total-calculation"></a>Erweitern einer Tabelle mit einer prozentualen Gesamtberechnung

Ein Tabellen Ausdruck, der eine numerische Spalte enthält, ist für den Benutzer nützlicher, wenn er zusammen mit dem Wert als Prozentsatz des Gesamtwerts begleitet wird. Nehmen wir beispielsweise an, dass eine Abfrage vorhanden ist, die die folgende Tabelle erzeugt:

|Someseries|Someint|
|----------|-------|
|Foo       |    100|
|Balken       |    200|

Wenn Sie die folgende Tabelle anzeigen möchten:

|Someseries|Someint|P |
|----------|-------|----|
|Foo       |    100|33,3|
|Balken       |    200|66,6|

Anschließend müssen Sie die Summe (Summe) der `SomeInt` Spalte berechnen und dann die einzelnen Werte dieser Spalte durch die Summe aufteilen. Verwenden Sie für beliebige Ergebnisse den [as-Operator](asoperator.md).

```kusto
// The following table literal represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Foo",
  200, "Bar",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>Ausführen von Aggregationen in einem gleitenden Fenster

Im folgenden Beispiel wird gezeigt, wie Spalten mithilfe eines gleitenden Fensters zusammengefasst werden.
Verwenden Sie die unten stehende Tabelle, die die Preise für Früchte nach Zeitstempeln enthält. Berechnen Sie die Mindest-, höchst-und Summen Kosten pro Tag mit einem gleitenden Fenster von sieben Tagen. Jeder Datensatz im Resultset aggregiert die vorherigen sieben Tage, und das Ergebnis enthält einen Datensatz pro Tag im Analysezeitraum.  

Die Tabelle "Früchte":

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

Die Aggregations Abfrage des gleitenden Fensters.
Eine Erläuterung folgt den Abfrage Ergebnissen:

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

Abfrage Details:

Die Abfrage "gestreckt" (dupliziert) jeden Datensatz in der Eingabe Tabelle in den sieben Tagen nach der tatsächlichen Darstellung. Jeder Datensatz wird tatsächlich sieben Mal angezeigt.
Folglich umfasst die tägliche Aggregation alle Datensätze der letzten sieben Tage.

Die Schritt-für-Schritt-Erläuterung unten bezieht sich auf Zahlen im Codebeispiel, ganz rechts:
1. Jeden Datensatz in einen Tag einteilen (relativ zu _start). 
2. Bestimmen Sie das Ende des Bereichs pro Datensatz-_bin + 7D, es sei denn, dieser Wert liegt außerhalb des Bereichs _(Anfang, Ende)_ . in diesem Fall wird er angepasst. 
3. Erstellen Sie für jeden Datensatz ein Array von sieben Tagen (Timestamps), beginnend mit dem Tag des aktuellen Datensatzes. 
4. MV-erweitern Sie das Array, und Duplizieren Sie die einzelnen Datensätze in sieben Datensätze, wobei Sie einen Tag voneinander trennen. 
5. Führen Sie die Aggregations Funktion für jeden Tag aus. Aufgrund #4 werden diese tatsächlich in den _letzten_ sieben Tagen zusammengefasst. 
6. Die Daten für die ersten sieben Tage sind unvollständig. Es gibt keinen 7D-loobackzeitraum für die ersten sieben Tage. Die ersten sieben Tage werden aus dem Endergebnis ausgeschlossen. In diesem Beispiel sind Sie nur an der Aggregation für 2018-10-01 beteiligt.

## <a name="find-preceding-event"></a>Vorheriges Ereignis suchen
Im nächsten Beispiel wird veranschaulicht, wie Sie ein vorhergehenden Ereignis zwischen zwei Datasets finden.  

*Zweck:*: Es gibt zwei Datasets: A und B. Suchen Sie für jeden Datensatz in B das vorherige Ereignis in einem (d. h. den ARG_MAX Datensatz in einer, die immer noch "älter" als B ist). Im folgenden finden Sie die erwartete Ausgabe für die folgenden Beispiel Datasets. 

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

|Timestamp|ID|Eventb|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|j|Ay1|
|2019-01-01 00:00:05.0000000|j|Ay2|

</br>

|Timestamp|ID|Eventa|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|j|B|
|2019-01-01 00:02:00.0000000|z|B|

Erwartete Ausgabe: 

|ID|Timestamp|Eventb|A_Timestamp|Eventa|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|j|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Es gibt zwei verschiedene Ansätze, die für dieses Problem vorgeschlagen werden. Sie sollten beide in Ihrem speziellen DataSet testen, um das für Sie am besten geeignete DataSet zu finden.

> [!NOTE] 
> Jede Methode kann unterschiedlichen Datasets unterschiedlich ausgeführt werden.

### <a name="suggestion-1"></a>Vorschlag #1

Dieser Vorschlag serialisiert beide Datasets nach ID und TIMESTAMP, gruppiert dann alle B-Ereignisse mit all ihren vorangehenden Ereignissen und wählt den `arg_max` von allen as in der Gruppe aus.

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

### <a name="suggestion-2"></a>Vorschlag #2

Für diesen Vorschlag ist ein Max-lookbackperiod erforderlich (wie viel "älter" der Datensatz in einem im Vergleich zu "B" sein kann). Die-Methode verbindet dann die beiden Datasets mit der ID und diesem lookbackzeitraum. Der JOIN erzeugt alle möglichen Kandidaten, alle Datensätze, die älter als b und innerhalb des Nachschlage Zeitraums sind, und dann wird der nächstgelegene b-Wert nach arg_min (timestampb – timestampa) gefiltert. Der kürzerer Zeitraum ist, desto besser sind die Abfrageergebnisse.

Im folgenden Beispiel wird der Nachschlage Zeitraum auf 1 m festgelegt, und der Datensatz mit der ID "z" weist kein entsprechendes "a"-Ereignis auf, da sein "a" älter als 2 m ist.

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

|Id|B_Timestamp|A_Timestamp|Eventb|Eventa|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|j|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||

::: zone-end

::: zone pivot="azuremonitor"

## <a name="string-operations"></a>Zeichenfolgenvorgänge
Die folgenden Abschnitte beschreiben Beispiele für das Arbeiten mit Zeichen folgen in der Kusto-Abfragesprache.

### <a name="strings-and-escaping-them"></a>Zeichenfolgen und Escapezeichen
Zeichenfolgenwerte werden entweder in einfache oder doppelte Anführungszeichen eingeschlossen. Der umgekehrte Schrägstrich (\\) wird verwendet, um darauffolgende Zeichen mit einem Escapezeichen zu versehen. „\t“ wird beispielsweise für einen Tabstopp, „\n“ für einen Zeilenvorschub und \" für das Anführungszeichen selbst verwendet.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Wenn „\\“ nicht als Escapezeichen verwendet werden soll, müssen Sie der Zeichenfolge „\@“ voranstellen.

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>Zeichenfolgenvergleiche

Operator       |BESCHREIBUNG                         |Groß-/Kleinschreibung|Beispiel (ergibt `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Equals                              |Ja           |`"aBc" == "aBc"`
`!=`           |Not Equals                          |Ja           |`"abc" != "ABC"`
`=~`           |Equals                              |Nein            |`"abc" =~ "ABC"`
`!~`           |Not Equals                          |Nein            |`"aBc" !~ "xyz"`
`has`          |Rechter Ausdruck ist vollständig in linkem Ausdruck enthalten |Nein|`"North America" has "america"`
`!has`         |Rechter Ausdruck ist nicht vollständig in linkem Ausdruck enthalten       |Nein            |`"North America" !has "amer"` 
`has_cs`       |Rechter Ausdruck ist vollständig in linkem Ausdruck enthalten |Ja|`"North America" has_cs "America"`
`!has_cs`      |Rechter Ausdruck ist nicht vollständig in linkem Ausdruck enthalten       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Rechter Ausdruck ist Präfix in linkem Ausdruck         |Nein            |`"North America" hasprefix "ame"`
`!hasprefix`   |Rechter Ausdruck ist kein Präfix in linkem Ausdruck     |Nein            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Rechter Ausdruck ist Präfix in linkem Ausdruck         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Rechter Ausdruck ist kein Präfix in linkem Ausdruck     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Rechter Ausdruck ist Suffix in linkem Ausdruck         |Nein            |`"North America" hassuffix "ica"`
`!hassuffix`   |Rechter Ausdruck ist kein Suffix in linkem Ausdruck     |Nein            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Rechter Ausdruck ist Suffix in linkem Ausdruck         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Rechter Ausdruck ist kein Suffix in linkem Ausdruck     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Rechter Ausdruck tritt als Teilzeichenfolge in linkem Ausdruck auf  |Nein            |`"FabriKam" contains "BRik"`
`!contains`    |Rechter Ausdruck tritt nicht in linkem Ausdruck auf           |Nein            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Rechter Ausdruck tritt als Teilzeichenfolge in linkem Ausdruck auf  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Rechter Ausdruck tritt nicht in linkem Ausdruck auf           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Rechter Ausdruck ist eine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Nein            |`"Fabrikam" startswith "fab"`
`!startswith`  |Rechter Ausdruck ist keine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Nein        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Rechter Ausdruck ist eine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Rechter Ausdruck ist keine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Rechter Ausdruck ist eine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Nein             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Rechter Ausdruck ist keine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Nein         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Rechter Ausdruck ist eine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Rechter Ausdruck ist keine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|Linker Ausdruck enthält eine Übereinstimmung mit rechtem Ausdruck        |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Entspricht einem der Elemente       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Entspricht keinem der Elemente   |Ja           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>countof

Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Übereinstimmungen können für einfache Zeichenfolgen oder reguläre Ausdrücke ermittelt werden. Bei Übereinstimmungen für einfache Zeichenfolgen treten möglicherweise Überschneidungen auf. Bei Übereinstimmungen für reguläre Ausdrücke ist dies nicht Fall.

```
countof(text, search [, kind])
```

- `text`: die eingegebene Zeichenfolge. 
- `search`: die einfache Zeichenfolge oder der reguläre Ausdruck, die bzw. der in „text“ abgeglichen werden soll.
- `kind` - _normal_ | _regex_ (Standard: „normal“).

Gibt an, wie oft die Such Zeichenfolge im Container abgeglichen werden kann. Bei Übereinstimmungen für einfache Zeichenfolgen treten möglicherweise Überschneidungen auf. Bei Übereinstimmungen für reguläre Ausdrücke ist dies nicht Fall.

#### <a name="plain-string-matches"></a>Übereinstimmungen für einfache Zeichenfolgen

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Übereinstimmungen für reguläre Ausdrücke

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>extract

Ermittelt eine Übereinstimmung für einen regulären Ausdruck auf der Grundlage einer angegebenen Zeichenfolge. Optional kann die extrahierte Teilzeichenfolge in den angegebenen Typ konvertiert werden.

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`: ein regulärer Ausdruck.
- `captureGroup`: eine positive Integerkonstante für die zu extrahierende Erfassungsgruppe. 0 steht für die vollständige Übereinstimmung, 1 für den mit der ersten '('Klammer')' übereinstimmenden Wert im regulären Ausdruck, 2 oder höher für nachfolgende Klammern.
- `text`: die zu suchende Zeichenfolge.
- `typeLiteral`: optionales Typliteral (z.B. typeof(long)). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert.

Gibt die Teil Zeichenfolge zurück, die mit der angezeigten Erfassungs Gruppe capturegroup übereinstimmt und optional in typeliteral konvertiert wurde.
Wenn keine Übereinstimmung vorhanden ist oder bei der Typkonvertierung ein Fehler auftritt, wird NULL zurückgegeben.


Im folgenden Beispiel wird aus einem Heartbeatdatensatz das letzte Oktett von *ComputerIP* extrahiert:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Im folgenden Beispiel wird das letzte Oktett extrahiert und in den Typ *real* (Zahl) umgewandelt. Anschließend wird der nächste IP-Wert berechnet.
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Im folgenden Beispiel wird in der Zeichenfolge *Trace* nach der Definition von „Duration“ gesucht. Die Übereinstimmung wird in *real* umgewandelt und mit einer Zeitkonstante (1 s) multipliziert, *durch die „Duration“ in den Typ „timespan“ umgewandelt wird*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* gibt TRUE zurück, wenn das Argument eine leere Zeichenfolge oder NULL ist (s. auch *isnull*).
- *isnotempty* gibt TRUE zurück, wenn das Argument eine Zeichenfolge ist, die nicht leer oder NULL ist (s. auch *isnotnull*). Alias: *notempty*.


```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Beispiele

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>parseurl

Teilt eine URL in ihre jeweiligen Bestandteile (Protokoll, Host, Port usw.) auf und gibt ein Wörterbuchobjekt zurück, das diese Bestandteile als Zeichenfolgen enthält.

```
parseurl(urlstring)
```

#### <a name="examples"></a>Beispiele

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Das Ergebnis sieht wie folgt aus:
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


### <a name="replace"></a>replace

Ersetzt alle Übereinstimmungen für reguläre Ausdrücke durch eine andere Zeichenfolge. 

```
replace(regex, rewrite, input_text)
```

- `regex`: der reguläre Ausdruck, für den eine Übereinstimmung ermittelt werden soll. Er kann Erfassungsgruppen in „('Klammern')“ enthalten.
- `rewrite`: der reguläre Ersatzausdruck für alle Übereinstimmungen, die durch „regex“ ermittelt wurden. Verwenden Sie „\0“, um auf die gesamte Übereinstimmung zu verweisen, „\1“ für die erste Erfassungsgruppe, „\2“ für die zweite Erfassungsgruppe usw.
- `input_text`: die Eingabezeichenfolge, in der gesucht werden soll.

Gibt den Text zurück, nachdem alle Übereinstimmungen von Regex durch Auswertungen von Rewrite ersetzt wurden. Bei Übereinstimmungen kommt es nicht zu Überschneidungen.


```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Folgende Ergebnisse sind möglich:

Aktivität                                        |replaced
------------------------------------------------|----------------------------------------------------------
4663: Es wurde versucht, auf das Objekt zuzugreifen.  |Aktivitäts-ID 4663: Es wurde versucht, auf das Objekt zuzugreifen.


### <a name="split"></a>split

Teilt eine angegebene Zeichenfolge entsprechend des festgelegten Trennzeichens auf und gibt ein Array mit den Teilzeichenfolgen zurück.

```
split(source, delimiter [, requestedIndex])
```

- `source`: die Zeichenfolge, die entsprechend des festgelegten Trennzeichens geteilt wird.
- `delimiter`: das Trennzeichen, das zum Teilen der Quellzeichenfolge verwendet wird.
- `requestedIndex`: ein optionaler nullbasierter Index. Falls ein solcher angegeben wird, enthält das Zeichenfolgenarray nur dieses Element (sofern vorhanden).


#### <a name="examples"></a>Beispiele

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>strcat

Verkettet Zeichenfolgenargumente (unterstützt 1 bis 16 Argumente).

```
strcat("string1", "string2", "string3")
```

#### <a name="examples"></a>Beispiele
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>strlen

Gibt die Länge einer Zeichenfolge zurück.

```
strlen("text_to_evaluate")
```

#### <a name="examples"></a>Beispiele
```Kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>substring

Extrahiert ab dem angegebenen Index eine Teilzeichenfolge aus einer Quellzeichenfolge. Optional kann die Länge der angeforderten Teilzeichenfolge angegeben werden.

```
substring(source, startingIndex [, length])
```

- `source`: die Quellzeichenfolge, aus der die Teilzeichenfolge entnommen wird.
- `startingIndex`: die nullbasierte Position des Anfangszeichens der angeforderten Teilzeichenfolge.
- `length`: ein optionaler Parameter, mit dem die erforderliche Länge der zurückgegebenen Teilzeichenfolge festgelegt werden kann.

#### <a name="examples"></a>Beispiele
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>tolower, toupper

Konvertiert eine angegebene Zeichenfolge in Groß-/Kleinbuchstaben.

```
tolower("value")
toupper("value")
```

#### <a name="examples"></a>Beispiele
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>Datums- und Uhrzeitvorgänge
Die folgenden Abschnitte beschreiben Beispiele für das Arbeiten mit Datums-und Uhrzeitwerten in der Kusto-Abfragesprache.

### <a name="date-time-basics"></a>Grundlagen zu Datum und Uhrzeit
Die Abfragesprache Kusto verfügt über zwei Hauptdatentypen, die Datums- und Uhrzeitangaben zugeordnet sind: datetime und timespan. Alle Datumsangaben werden in UTC ausgedrückt. Es werden zwar mehrere datetime-Formate unterstützt, jedoch wird das ISO8601-Format bevorzugt. 

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

datetime-Werte können durch Umwandeln einer Zeichenfolge mithilfe des Operators `todatetime` erstellt werden. Beispielsweise verwenden Sie zur Überprüfung der in einer bestimmten Zeitspanne gesendeten VM-Heartbeats den Operator `between`, um einen Zeitraum anzugeben.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ein weiteres gängiges Szenario besteht in dem Vergleich eines datetime-Werts mit dem vorhandenen Wert. Wenn Sie beispielsweise sämtliche Heartbeats der letzten zwei Minuten anzeigen möchten, können Sie den Operator `now` zusammen mit einem timespan-Wert verwenden, der zwei Minuten darstellt:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Für diese Funktion ist auch eine Verknüpfung verfügbar:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Die kürzeste und am besten lesbare Methode besteht jedoch in der Verwendung des Operators `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Angenommen, Ihnen ist die Start- und Endzeit nicht bekannt, sondern die Startzeit und die Dauer. Sie können die Abfrage wie folgt erneut generieren:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="converting-time-units"></a>Konvertieren von Zeiteinheiten
Sie können einen datetime- oder timespan-Wert in einer anderen als der Standardzeiteinheit ausdrücken. Wenn Sie beispielsweise Fehlerereignisse der letzten 30 Minuten überprüfen und eine berechnete Spalte benötigen, in der angezeigt wird, wie viel Zeit seit Auftreten des Ereignisses vergangen ist, gehen Sie wie folgt vor:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Die Spalte `timeAgo` enthält Werte wie z.B. „00:09:31.5118992“. Dies bedeutet, dass sie im Format „hh:mm:ss.fffffff“ vorliegen. Wenn Sie diese Werte in `numver` von Minuten seit der Startzeit formatieren möchten, teilen Sie diesen Wert durch „1 Minute“:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


### <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregationen und Zuordnung von Buckets nach Zeitintervallen
Ein weiteres gängiges Szenario besteht in der Notwendigkeit, Statistiken über einen bestimmten Zeitraum in einem bestimmten Aggregationsintervall abzurufen. Für dieses Szenario kann ein `bin`-Operator als Teil einer Summarize-Klausel verwendet werden.

Mit der folgenden Abfrage können Sie die Anzahl der Ereignisse abrufen, die in der letzten halben Stunde alle 5 Minuten aufgetreten sind:

```Kusto
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

Eine weitere Möglichkeit zum Erstellen von Buckets für die Ergebnisse besteht in der Verwendung von Funktionen, wie z.B. `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Diese Abfrage erzeugt die folgenden Ergebnisse:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7\.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5\.416|


### <a name="time-zones"></a>Zeitzonen
Da alle datetime-Werte in UTC ausgedrückt werden, ist es häufig hilfreich, diese Werte in die lokale Zeitzone zu konvertieren. Verwenden Sie beispielsweise die folgende Berechnung zum Konvertieren von UTC- in PST-Zeiten:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Aggregationen
In den folgenden Abschnitten sind Beispiele für die Aggregation der Ergebnisse einer Abfrage in der Kusto-Abfragesprache aufgeführt.

### <a name="count"></a>count
Zählen Sie die Anzahl der Zeilen im Resultset, nachdem alle Filter angewendet wurden. Das folgende Beispiel gibt die Gesamtanzahl der Zeilen in der Tabelle _Perf_ der letzten 30 Minuten aus. Das Ergebnis wird in einer Spalte namens *count_* zurückgegeben, sofern Sie keinen bestimmten Namen zuweisen:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Eine Zeitdiagrammvisualisierung kann hilfreich sein, um im Zeitverlauf einen Trend zu erkennen:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Die Ausgabe in diesem Beispiel zeigt die Trendlinie der Anzahl der Perf-Datensätze in Intervallen von 5 Minuten:

![Anzahl der Trends](images/samples/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Verwenden Sie `dcount` und `dcountif` zum Zählen von bestimmten Werten in einer bestimmten Spalte. Die folgende Abfrage wertet aus, wie viele unterschiedliche Computer in der letzten Stunde Takte gesendet haben:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Verwenden Sie `dcountif`, um nur die Linux-Computer zu zählen, die Heartbeats gesendet haben:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Auswerten von Untergruppen
Verwenden Sie das Schlüsselwort `by`, um eine Zählung oder Aggregationen auf Untergruppen in Ihren Daten auszuführen. Beispielsweise um die Anzahl der unterschiedlichen Linux-Computer zu zählen, die in jedem Land bzw. jeder Region Heartbeats gesendet haben:

```Kusto
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


Um noch kleineren Untergruppen von Daten zu analysieren, fügen Sie zusätzliche Spaltennamen in den Abschnitt `by` ein. Beispielsweise möchten Sie die unterschiedlichen Computer von jedem Land bzw. jeder Region pro OSType zählen:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>Perzentil
Um den Median zu ermitteln, verwenden Sie die `percentile`-Funktion mit einem Wert zum Angeben des Quantils:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Sie können auch verschiedene Quantile angeben, um für jedes ein aggregiertes Ergebnis zu erhalten:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Dadurch wird möglicherweise deutlich, dass einige CPUs des Computers einen ähnlichen Medianwert haben. Während sich dieser bei manchen stabil in der Nähe des Medians bewegt, melden andere viel niedrigere und höhere CPU-Werte, die von Spitzen verursacht wurden.

### <a name="variance"></a>Variance
Um die Varianz eines Werts direkt zu evaluieren, verwenden Sie die Standardabweichung und Varianzmethoden:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Eine gute Möglichkeit zur Analyse der Stabilität der CPU-Auslastung besteht darin, stdev mit der Berechnung des Medians zu kombinieren:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generating-lists-and-sets"></a>Generieren von Listen und Sätzen
Sie können mithilfe von `makelist` Daten nach der Reihenfolge der Werte in einer bestimmten Spalte pivotieren. So sollten Sie beispielsweise die Auftragsereignisse untersuchen, die am häufigsten auf Ihren Computern ausgeführt werden. Sie können die Daten im Wesentlichen nach der Reihenfolge der Ereignis-IDs auf den einzelnen Computern pivotieren. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` generiert eine Liste in der Reihenfolge, in der Daten an sie übergeben wurden. Verwenden Sie zum Sortieren von Ereignissen vom ältesten bis zum neuesten in der Auftragsanweisung `asc` anstelle von `desc`. 

Es ist auch sinnvoll, eine Liste der unterschiedlichen Werte zu erstellen. Diese wird als _Satz_ bezeichnet und kann mit `makeset` generiert werden:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Wie `makelist` kann `makeset` auch mit sortierten Daten ausgeführt werden und generiert die Arrays basierend auf der Reihenfolge der übergebenen Zeilen.

### <a name="expanding-lists"></a>Erweitern von Listen
Der Inverse-Vorgang von `makelist` oder `makeset` lautet `mvexpand` und erweitert eine Liste von Werten zum Separieren von Zeilen. Der Vorgang kann, bei JSON und Array, eine Erweiterung über eine beliebige Anzahl von dynamischen Spalten erzielen. Sie könnten beispielsweise die Tabelle *Heartbeat* auf Lösungen überprüfen, indem Sie Daten von Computern senden, die in der vergangenen Stunde einen Heartbeat gesendet haben:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Lösungen | 
|--------------|----------------------|
| computer1 | „security“, „updates“, „changeTracking“ |
| computer2 | „security“, „updates“ |
| computer3 | „antiMalware“, „changeTracking“ |
| ... | ... |

Mit `mvexpand` können Sie die einzelnen Werte in einer separaten Zeile statt in einer durch Trennzeichen getrennten Liste anzeigen:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

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


Anschließend können Sie erneut `makelist` verwenden, um Elemente zu gruppieren. Zeigen Sie dieses Mal die Liste der Computer pro Lösung an:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Lösungen | list_Computer |
|--------------|----------------------|
| „security“ | ["computer1", "computer2"] |
| „updates“ | ["computer1", "computer2"] |
| „changeTracking“ | ["computer1", "computer3"] |
| „antiMalware“ | [„computer3“] |
| ... | ... |

### <a name="handling-missing-bins"></a>Behandeln fehlender Klassen
Eine nützliche Anwendung von `mvexpand` ist die Notwendigkeit, Standardwerte für fehlende Klassen einzugeben. Angenommen beispielsweise, Sie suchen nach der Betriebszeit eines bestimmten Computers, indem Sie dessen Heartbeat untersuchen. Sie möchten auch die Quelle des Heartbeats sehen, die in der Spalte _Kategorie_ zu finden ist. Normalerweise würde eine einfache Summarize-Anweisung wie folgt verwendet:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt-Agent | 2017-06-06T17:00:00Z | 15 |
| Direkt-Agent | 2017-06-06T18:00:00Z | 60 |
| Direkt-Agent | 2017-06-06T20:00:00Z | 55 |
| Direkt-Agent | 2017-06-06T21:00:00Z | 57 |
| Direkt-Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

In diesen Ergebnissen fehlt jedoch der Bucket, der „2017-06-06T19:00:00Z“ zugeordnet wurde, da für diese Stunde keine Heartbeatdaten vorhanden sind. Mit der Funktion `make-series` können Sie leeren Buckets einen Standardwert zuweisen. Dadurch wird für jede Kategorie eine Zeile mit zwei zusätzlichen Arrayspalten generiert, von denen eine Spalte für Werte und die andere für übereinstimmende Zeitrahmen vorgesehen ist:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| Direkt-Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Das dritte Element des Arrays *count_* ist wie erwartet eine 0 (null). Zudem gibt es im Array _TimeGenerated_ einen übereinstimmenden Zeitrahmen von „2017-06-06T19:00:00.0000000Z“. Dieses Arrayformat ist jedoch schwer zu lesen. Verwenden Sie `mvexpand`, um die Arrays zu erweitern und die gleiche Formatausgabe zu generieren, die von `summarize` generiert wurde:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt-Agent | 2017-06-06T17:00:00Z | 15 |
| Direkt-Agent | 2017-06-06T18:00:00Z | 60 |
| Direkt-Agent | 2017-06-06T19:00:00Z | 0 |
| Direkt-Agent | 2017-06-06T20:00:00Z | 55 |
| Direkt-Agent | 2017-06-06T21:00:00Z | 57 |
| Direkt-Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Einschränken von Ergebnissen auf eine Reihe von Elementen: `let`, `makeset`, `toscalar`, `in`
In einem häufig auftretenden Szenario werden die Namen einiger bestimmter Entitäten basierend auf einer Reihe von Kriterien ausgewählt. Anschließend wird ein anderes Dataset für diese Reihe von Entitäten weiter gefiltert. Sie könnten beispielsweise nach Computern suchen, auf denen bekanntermaßen Updates fehlen, und IP-Adressen identifizieren, die von diesen Computern aufgerufen wurden:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>Joins
Mithilfe von Joins können Sie Daten aus mehreren Tabellen in derselben Abfrage analysieren. Diese führen die Zeilen zweier Datasets anhand von übereinstimmenden Werten der angegebenen Spalte zusammen.


```Kusto
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

In diesem Beispiel filtert das erste Dataset alle Anmeldeereignisse. Dieses Dataset ist mit einem zweiten Dataset verknüpft, das alle Abmeldeereignisse filtert. Die projizierten Spalten sind _Computer_, _Account_, _TargetLogonId_ und _TimeGenerated_. Die Datasets werden von einer freigegebenen Spalte (_TargetLogonId_) korreliert. Die Ausgabe ist ein einzelnes Dataset pro Korrelation, das die An- und Abmeldezeit enthält.

Wenn beide Datasets Spalten mit dem gleichen Namen enthalten, wird den Spalten des rechten Datasets eine Indexnummer hinzugefügt. In diesem Beispiel würden die Ergebnisse _TargetLogonId_ mit Werten der linken Tabelle und _TargetLogonId1_ mit Werten der rechten Tabelle anzeigen. In diesem Fall wurde die zweite _TargetLogonId1_-Spalte mithilfe des `project-away`-Operators entfernt.

> [!NOTE]
> Behalten Sie mithilfe des `project`-Operators nur die relevanten Spalten der verknüpften Datasets bei, um die Leistung zu verbessern.


Verwenden Sie die folgende Syntax, um zwei Datasets zu verknüpfen. Der verknüpfte Schlüssel hat in den beiden Tabellen einen unterschiedlichen Namen:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Nachschlage Tabellen
Joins werden häufig verwendet, um die statische Zuordnung von Werten mithilfe von `datatable` zu verwenden. Diese kann bei der Transformation der Ergebnisse hilfreich sein, um diese besser darstellen zu können. So können Sie beispielsweise den Sicherheitsereignisdaten den Ereignisnamen für jede Ereignis-ID hinzufügen.

```Kusto
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

| eventName | count_ |
|:---|:---|
| Das Handle für ein Objekt wurde geschlossen. | 290.995 |
| Ein Handle für ein Objekt wurde angefordert. | 154.157 |
| Es wurde versucht, ein Handle für ein Objekt zu duplizieren. | 144.305 |
| Es wurde versucht, auf ein Objekt zuzugreifen. | 123.669 |
| Kryptografischer Vorgang | 153.495 |
| Schlüsseldatei Vorgang | 153.496 |

## <a name="json-and-data-structures"></a>JSON und Datenstrukturen

Geschachtelte Objekte sind Objekte, die andere Objekte in Form eines Arrays oder einer Zuordnung von Schlüssel-Wert-Paaren enthalten. Diese Objekte werden als JSON-Zeichenfolgen dargestellt. In diesem Abschnitt wird beschrieben, wie JSON zum Abrufen von Daten und Analysieren von geschposteten Objekten verwendet wird.

### <a name="working-with-json-strings"></a>Arbeiten mit JSON-Zeichenfolgen
Greifen Sie mit `extractjson` auf ein bestimmtes JSON-Element in einem bekannten Pfad zu. Zur Nutzung dieser Funktion ist ein Pfadausdruck erforderlich, für den die folgenden Konventionen gelten:

- _$_ verweist auf den Stammordner.
- Verwenden Sie die Klammer- oder Punktnotation, um wie in den folgenden Beispielen dargestellt auf Indizes und Elemente zu verweisen.


Verwenden Sie Klammern für Indizes und Punkte zum Trennen von Elementen:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Das gleiche Ergebnis mit der Klammernotation erhalten Sie wie folgt:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Wenn nur ein Element vorhanden ist, können Sie nur die Punktnotation verwenden:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>parsejson
Sie können besonders leicht auf mehrere Elemente in Ihrer JSON-Struktur zugreifen, wenn der Zugriff über ein dynamisches Objekt erfolgt. Wandeln Sie die Textdaten mit `parsejson` in ein dynamisches Objekt um. Anschließend können Sie die Daten mithilfe zusätzlicher Funktionen analysieren.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Verwenden Sie `arraylength`, um die Anzahl der Elemente in einem Array zählen zu lassen:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Verwenden Sie `mvexpand`, um die Eigenschaften eines Objekts in separate Zeilen aufzuteilen.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![Screenshot von „hosts_0“ mit Werten für „location“, „status“ und „rate“](images/samples/mvexpand.png)

### <a name="buildschema"></a>buildschema
Verwenden Sie `buildschema`, um das Schema für zulässige Objektwerte abzurufen:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Die Ausgabe ist ein Schema im JSON-Format:
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
Diese Ausgabe enthält die Namen der Objektfelder und die übereinstimmenden Datentypen. 

Für geschachtelte Objekte liegen möglicherweise wie im folgenden Beispiel verschiedene Schemas vor:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Diagramme
In den folgenden Abschnitten werden Beispiele für das Arbeiten mit Diagrammen in der Kusto-Abfragesprache beschrieben.

### <a name="chart-the-results"></a>Diagramm Ergebnisse
Beginnen Sie mit der Überprüfung, wie viele Computer pro Betriebssystem während der letzten Stunde vorhanden waren:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Standardmäßig werden Ergebnisse als Tabelle angezeigt:

![Tabelle](images/samples/table-display.png)

Wählen Sie **Chart** (Diagramm) und dann die Option **Pie** (Kreisdiagramm), um die Ergebnisse anzuzeigen, damit Sie eine bessere Ansicht erhalten:

![Kreisdiagramm](images/samples/charts-and-diagrams-pie.png)


### <a name="timecharts"></a>Zeitdiagramme
Zeigen Sie die durchschnittliche, 50. und 95. Quantile der Prozessorzeit in Containern von einer Stunde an. Die Abfrage generiert mehrere Reihen. Sie können auswählen, welche Reihe im Zeitdiagramm angezeigt wird.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wählen Sie die Anzeigeoption für das **Linien** Diagramm aus:

![Liniendiagramm](images/samples/charts-and-diagrams-multiple-series.png)

#### <a name="reference-line"></a>Bezugslinie

Eine Bezugslinie kann Ihnen dabei helfen, einfach zu identifizieren, ob die Metrik einen bestimmten Schwellenwert überschritten hat. Erweitern Sie das Dataset mit einer konstanten Spalte, um dem Diagramm eine Zeile hinzuzufügen:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Bezugslinie](images/samples/charts-and-diagrams-multiple-series-threshold.png)

### <a name="multiple-dimensions"></a>Mehrere Dimensionen
Mit mehreren Ausdrücken in der `by`-Klausel von `summarize` werden mehrere Zeilen in den Ergebnissen erstellt, und zwar eine für jede Kombination der Werte.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Wenn Sie die Ergebnisse als Diagramm anzeigen, wird die erste Zeile aus der `by`-Klausel verwendet. Im folgenden Beispiel ist ein Diagramm mit gestapelten Säulen mithilfe der _EventID_ dargestellt. Dimensionen müssen vom Typ `string` sein. In diesem Beispiel wird die _EventID_ also in eine Zeichenfolge umgewandelt. 

![Balkendiagramm, EventID](images/samples/charts-and-diagrams-multiple-dimension-1.png)

Sie können zwischen diesen wechseln, indem Sie die Dropdownliste mit dem Spaltennamen auswählen. 

![Balkendiagramm, AccountType](images/samples/charts-and-diagrams-multiple-dimension-2.png)

## <a name="smart-analytics"></a>Intelligente Analysen
Dieser Abschnitt enthält Beispiele für die Verwendung von intelligenten Analysefunktionen in Log Analytics, um die Analyse der Benutzeraktivität auszuführen. Sie können diese Beispiele verwenden, um Ihre eigenen Anwendungen, die von Application Insights überwacht werden, zu analysieren. Sie können die Konzepte in diesen Abfragen auch für ähnliche Analysen anderer Daten verwenden. 

### <a name="cohorts-analytics"></a>Kohortenanalysen

Bei der Kohortenanalyse wird die Aktivität von bestimmten Benutzergruppen, die als Kohorten bezeichnet werden, nachverfolgt. Damit soll gemessen werden, wie attraktiv ein Dienst ist, indem die Rate der wiederkehrenden Benutzer gemessen wird. Die Benutzer werden nach der Zeit ihrer ersten Nutzung des Diensts gruppiert. Beim Analysieren von Kohorten erwarten wir eine Abnahme der Aktivität gegenüber den ersten nachverfolgten Zeiträumen. Jede Kohorte wird nach der Woche benannt, in der ihre Mitglieder zum ersten Mal identifiziert wurden.

Im folgenden Beispiel wird die Anzahl der Aktivitäten analysiert, die Benutzer im Verlauf von 5 Wochen nach ihrer ersten Nutzung des Diensts ausführen.

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
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
Durch dieses Beispiel wird die folgende Ausgabe zurückgegeben.

:::image type="content" source="images/samples/cohorts.png" alt-text="Ausgabe der Kohortenanalyse":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Neue monatlich aktive Benutzer und Benutzerbindung
In den folgenden Beispielen wird die Zeitreihenanalyse mit der Funktion [series_fir](/azure/kusto/query/series-firfunction) veranschaulicht, die Berechnungen für gleitende Fenster ermöglicht. Die überwachte Beispielanwendung ist ein Onlineshop, der die Aktivität der Benutzer über benutzerdefinierte Ereignisse nachverfolgt. Die Abfrage verfolgt zwei Arten von Benutzeraktivitäten: _AddToCart_ und _Checkout_. Außerdem definiert sie _aktive Benutzer_ als diejenigen, die an einem bestimmten Tag mindestens einen Check-Out ausgeführt haben.



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

Durch dieses Beispiel wird die folgende Ausgabe zurückgegeben.

:::image type="content" source="images/samples/rolling-mau.png" alt-text="Ausgabe für neue monatlich aktive Benutzer":::

Im folgenden Beispiel wird die obige Abfrage in eine wiederverwendbare Funktion umgewandelt und verwendet, um die Benutzerfreundlichkeit zu berechnen. Aktive Benutzer werden in dieser Abfrage als solche Benutzer definiert, die an einem bestimmten Tag mindestens einen Check-Out ausgeführt haben.

``` Kusto
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
    | mvexpand RollingUserClicksByDay
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

Durch dieses Beispiel wird die folgende Ausgabe zurückgegeben.

:::image type="content" source="images/samples/user-stickiness.png" alt-text="Ausgabe für Benutzerbindung":::

### <a name="regression-analysis"></a>Regressionsanalyse
Dieses Beispiel zeigt, wie Sie eine automatisierte Erkennung für Dienstunterbrechungen erstellen, die ausschließlich auf den Ablaufverfolgungsprotokollen einer Anwendung basiert. Die Erkennung sucht nach abweichenden und plötzlichen Anstiegen bei der relativen Anzahl von Ablaufverfolgungen mit Fehlern und Warnungen in der Anwendung.

Für die Auswertung des Dienststatus basierend auf Überwachungsprotokolldaten kommen zwei Verfahren zum Einsatz:

- Mit [make-series](/azure/kusto/query/make-seriesoperator) werden teilweise strukturierte Ablaufverfolgungsprotokolle im Textformat in eine Metrik konvertiert, die das Verhältnis zwischen positiven und negativen Ablaufverfolgungseinträgen darstellt.
- Mit [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) und [series_fit_line](/azure/kusto/query/series-fit-linefunction) wird eine erweiterte Erkennung in einzelnen Schritten über eine Zeitreihenanalyse mit einer zweizeiligen linearen Regression durchgeführt.

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```


## <a name="next-steps"></a>Nächste Schritte

- Führen Sie [ein Tutorial zur Kusto-Abfragesprache durch](tutorial.md?pivots=azuremonitor).


::: zone-end

