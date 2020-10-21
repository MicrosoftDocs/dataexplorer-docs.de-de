---
title: 'Beispiele: Azure-Daten-Explorer'
description: In diesem Artikel werden die Beispiele in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0db8c472ed3b23a1bf46f8fce9cbd38b0ca960b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251024"
---
# <a name="samples"></a>Proben

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

:::image type="content" source="images/samples/040.png" alt-text="Screenshot eines Säulen Diagramms. Die y-Achse liegt zwischen 0 und ungefähr 50. Zehn farbige Spalten stellen die entsprechenden Werte von 10 Standorten dar."::: 

Fügen Sie Code hinzu, um die Dauer in Containern mit einfacher Größenordnung zu zählen. Wenn in diesem Beispiel ein Balkendiagramm bevorzugt wird, teilen Sie durch, `1s` um die Zeiträume in Zahlen zu konvertieren. 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="Screenshot eines Säulen Diagramms. Die y-Achse liegt zwischen 0 und ungefähr 50. Zehn farbige Spalten stellen die entsprechenden Werte von 10 Standorten dar.":::

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
| k | 10:01:29 | 10:03:10 |
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
| k | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
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
| k | 10:02:29 | 10:03:45 | 10:02:00|
| k | 10:02:29 | 10:03:45 | 10:03:00|
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

|count_|`Message`
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

|Anzahl |Prozent (%)|Komponente|Cluster|`Message`
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
