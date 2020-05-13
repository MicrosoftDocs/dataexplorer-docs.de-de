---
title: 'Beispiele: Azure-Daten-Explorer'
description: In diesem Artikel werden die Beispiele in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fe44323dabb246438f18c9ab01eec0008ad4fe97
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372962"
---
# <a name="samples"></a>Beispiele

Im folgenden finden Sie einige häufige Abfrage Anforderungen und wie die Kusto-Abfragesprache verwendet werden kann, um Sie zu erfüllen.

## <a name="display-a-column-chart"></a>Anzeigen eines Säulen Diagramms

Projizieren Sie mindestens zwei Spalten, und verwenden Sie Sie als x-und y-Achse eines Diagramms:

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* Die erste Spalte bildet die x-Achse. Dies kann numerisch, DateTime oder String sein. 
* Verwenden `where` `summarize` Sie und, `top` um die Datenmenge einzuschränken, die Sie anzeigen.
* Sortieren Sie die Ergebnisse, um die Reihenfolge der x-Achse zu definieren.

:::image type="content" source="images/samples/060.png" alt-text="060":::

<a name="activities"></a>
## <a name="get-sessions-from-start-and-stop-events"></a>Abrufen von Sitzungen aus Start- und Stop-Ereignissen

Beispiel: Sie verfügen über ein Protokoll mit Ereignissen, bei denen einige Ereignisse den Anfang oder das Ende einer erweiterten Aktivität oder der Sitzung kennzeichnen. 

|name|City|SessionID|Timestamp|
|---|---|---|---|
|Start|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Start|Manchester|4267667|2015-12-09T10:14:02.23|
|Beenden|London|2817330|2015-12-09T10:23:43.18|
|Abbrechen|Manchester|4267667|2015-12-09T10:27:26.29|
|Beenden|Manchester|4267667|2015-12-09T10:28:31.72|

Jedes Ereignis hat eine SessionId, sodass das Problem darin besteht, die Start- und Stop-Ereignisse mit der gleichen ID abzugleichen.

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

Verwenden [`let`](./letstatement.md) Sie, um eine Projektion der Tabelle so weit wie möglich zu benennen, bevor Sie in den Join wechseln.
[`Project`](./projectoperator.md)wird verwendet, um die Namen der Zeitstempel so zu ändern, dass sowohl die Start-als auch die Endzeit im Ergebnis angezeigt werden können. Es werden auch die anderen Spalten ausgewählt, die im Ergebnis angezeigt werden sollen. [`join`](./joinoperator.md)Vergleicht die Start-und stoppeinträge für die gleiche Aktivität, wobei eine Zeile für jede Aktivität erstellt wird. Mit `project` wird schließlich erneut eine Spalte hinzugefügt, um die Dauer der Aktivität anzuzeigen.


|City|SessionID|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>Abrufen von Sitzungen ohne Sitzungs-ID

Nehmen Sie nun an, dass die Start- und Stop-Ereignisse keine Sitzungs-ID haben, die wir abgleichen können. Sie haben jedoch eine IP-Adresse des Clients, auf dem die Sitzung stattgefunden hat. Vorausgesetzt, für jede Client-Adresse wird nur jeweils eine Sitzung durchführt, dann können wir jedes Start-Ereignis dem nächsten Stop-Ereignis derselben IP-Adresse zuordnen.

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

Das Join ordnet jede Startzeit mit allen Endzeiten von der gleichen Client-IP-Adresse zu. Daher entfernen wir zunächst Übereinstimmungen mit früheren Endzeiten.

Anschließend gruppieren wir nach Startzeit und IP, um eine Gruppe für jede Sitzung zu erhalten. Wir müssen eine `bin` Funktion für den Parameter "StartTime" angeben: Wenn dies nicht der Fall ist, verwendet Kusto automatisch 1-Stunden-Klassen, die einigen Startzeiten mit den falschen Endzeit Punkten entsprechen.

`arg_min`wählt die Zeile mit der kleinsten Dauer in jeder Gruppe aus, und der `*` Parameter übergibt alle anderen Spalten, wenngleich "min_" jedem Spaltennamen vorangestellt wird. 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

Anschließend können wir Code hinzufügen, um die Dauer in Containern zu zählen, die sich in Containern befinden. Wir haben eine geringfügige Vorliebe für ein Balkendiagramm, daher teilen wir durch `1s` , um die Zeiträume in Zahlen zu konvertieren. 


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 

:::image type="content" source="images/samples/050.png" alt-text="050":::

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

<a name="concurrent-activities"><a/>
## <a name="chart-concurrent-sessions-over-time"></a>Erstellen eines Diagramms für gleichzeitige Sitzungen im Verlauf der Zeit

Angenommen, Sie haben eine Tabelle der Aktivitäten mit den jeweiligen Start- und Endzeiten.  Wir möchten ein Diagramm im Verlauf der Zeit anzeigen, das angibt, wie viele zu einem beliebigen Zeitpunkt gleichzeitig ausgeführt werden.

Nachfolgend sehen Sie eine Beispieleingabe, der wir den Namen „ `X`“ geben:

|SessionID | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Wir möchten ein Diagramm in 1-Minuten-Containern. Daher möchten wir etwas erstellen, das für jede laufende Aktivität gezählt werden kann.

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

Anstatt diese Arrays beizubehalten, erweitern wir Sie mithilfe von [MV-Expand](./mvexpandoperator.md):

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

Wir können diese Beispielzeit jetzt gruppieren, indem wir zählen, wie häufig jede Aktivitäten auftritt:

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Wir benötigen "DateTime ()", da " [MV-Expand](./mvexpandoperator.md) " eine Spalte vom Typ "Dynamic" ergibt.
* Darüber hinaus ist „bin()“ erforderlich, da für numerische Werte und Daten „summarize“ immer eine bin-Funktion mit einem Standardintervall anwendet, wenn Sie keine bereitstellen. 


| count_SessionId | Beispiele|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Dies kann als Balkendiagramm oder Zeitdiagramm dargestellt werden.

## <a name="introduce-null-bins-into-summarize"></a>NULL-Behälter zusammenfassen

Wenn der `summarize` Operator auf einen Gruppenschlüssel angewendet wird, der aus einer `datetime` Spalte besteht, werden diese Werte normalerweise von einem Container mit fester Breite versehen. Zum Beispiel:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Durch diesen Vorgang wird eine Tabelle mit einer einzelnen Zeile pro Zeilen Gruppe in erzeugt `T` , die in jede Gruppe von fünf Minuten fallen. Dies bedeutet nicht, dass "Null-Behälter"--Zeilen für Zeit-/Uhrzeitwerte zwischen `StartTime` und `StopTime` vorhanden sind, für die keine entsprechende Zeile in vorhanden ist `T` . 

Häufig ist es wünschenswert, die Tabelle mit diesen Containern zu "auffüllen". Dies ist eine Möglichkeit, um dies zu erreichen:

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

1. Mithilfe des- `union` Operators können wir einer Tabelle zusätzliche Zeilen hinzufügen. Diese Zeilen werden vom Ausdruck in erzeugt `union` .
2. Verwenden des- `range` Operators zum Entwickeln einer Tabelle mit einer einzelnen Zeile/Spalte.
   Die Tabelle wird nicht für die Verwendung durch verwendet `mv-expand` , um an zu arbeiten.
3. Verwenden Sie den `mv-expand` -Operator `range` für die-Funktion, um so viele Zeilen zu erstellen, wie 5 Minuten zwischen und vorhanden sind `StartTime` `EndTime` .
4. Alle mit einem `Count` von `0` .
5. Zuletzt verwenden wir den `summarize` -Operator, um die Behälter vom ursprünglichen (linken oder äußeren) Argument zu `union` und aus dem inneren Argument zu gruppieren (nämlich die NULL-bin-Zeilen). Dadurch wird sichergestellt, dass die Ausgabe eine Zeile pro bin enthält, deren Wert entweder 0 (null) oder die ursprüngliche Anzahl ist.  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>Bringen Sie Ihre Daten in Kusto mithilfe Machine Learning 

Es gibt viele interessante Anwendungsfälle für die Nutzung von Machine Learning-Algorithmen und die Ableitung interessanter Einblicke in die Telemetriedaten. Obwohl diese Algorithmen häufig ein sehr strukturiertes Dataset als Eingabe erfordern, entsprechen die Rohdaten des Protokolls in der Regel nicht der erforderlichen Struktur und Größe. 

Unsere Journey beginnt mit der Suche nach Anomalien in der Fehlerrate eines bestimmten Dienstanschluss Dienstanbieter-Dienstanbieter. In der Tabelle "Logs" sind 65 b-Einträge enthalten, und die unten stehende einfache Abfrage filtert 250 KB-Fehler und erstellt Zeitreihendaten der Fehler Anzahl, die die Funktion der Anomalieerkennung [series_decompose_anomalies](series-decompose-anomaliesfunction.md)verwendet. Die Anomalien werden vom Kusto-Dienst erkannt und als rote Punkte im Zeitreihen Diagramm hervorgehoben.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Der Dienst hat einige Zeitrahmen mit verdächtiger Fehlerrate identifiziert. Ich verwende Kusto zum Vergrößern dieses Zeitrahmens und führe eine Abfrage aus, die die "Message"-Spalte aggregiert, um nach den häufigsten Fehlern zu suchen. Ich habe die relevanten Teile aus der gesamten Stapel Überwachung der Nachricht gekürzt, damit Sie besser in die Seite passt. Sie sehen, dass ich mit den ersten acht Fehlern einen guten Erfolg hatte, aber dann eine lange Fehlermeldung erreicht habe, da die Fehlermeldung durch eine Format Zeichenfolge erstellt wurde, die sich ändernde Daten enthielt. 

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
|7125|Fehler beim inferencehostservice-Befehl. System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt...
|7124|Unerwarteter Rückschluss System Fehler. System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt... 
|5112|Unerwarteter Rückschluss System Fehler. System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt.
|174|Fehler beim inferencehostservice-Aufruf.. System. Service Model. CommunicationException: Fehler beim Schreiben in die Pipe:...
|10|Fehler bei "executealgorithmmethod" für die Methode "runcyclefrominteridata"...
|10|Rückschluss System Fehler.. Microsoft. zeichnen. Platform. Inferences. Service. Managers. userinterims datamanagerexception:...
|3|Fehler beim inferencehostservice-Aufruf.. System. Service Model. CommunicationObjectFaultedException:...
|1|System Fehler Rückschluss... Socialgraph. Boss. OperationResponse... AIS-traceid: 8292fc561ac64bed8fa243808fe74efd...
|1|System Fehler Rückschluss... Socialgraph. Boss. OperationResponse... AIS-traceid: 5f79f7587ff943ec9b641e02e701afbf...

An dieser Stelle `reduce` kommt der Operator zu Hilfe. Der `reduce` Operator identifizierte 63 andere Fehler, die vom gleichen Ablauf Verfolgungs Instrumentations Punkt im Code verursacht wurden, und half mir, sich in diesem Zeitfenster auf eine weitere sinnvolle Fehler Ablauf Verfolgung zu konzentrieren.

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
|  7125|Fehler beim inferencehostservice-Befehl. System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt...
|  7124|Unerwarteter Rückschluss System Fehler. System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt...
|  5112|Unerwarteter Rückschluss System Fehler. System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt.
|  174|Fehler beim inferencehostservice-Aufruf.. System. Service Model. CommunicationException: Fehler beim Schreiben in die Pipe:...
|  63|Rückschluss System Fehler.. Microsoft. Print. Platform. Inferences. \* : Write \* zum Schreiben in das Objekt "Boss. \* : socialgraph. Boss. reques..."
|  10|Fehler bei "executealgorithmmethod" für die Methode "runcyclefrominteridata"...
|  10|Rückschluss System Fehler.. Microsoft. zeichnen. Platform. Inferences. Service. Managers. userinterims datamanagerexception:...
|  3|Fehler beim inferencehostservice-Befehl. System. Service Model. \* : das-Objekt, System. Service Model. Channels. \* + \* , for \* \* ist die \* ...   bei Syst...

Nun, da ich einen guten Überblick über die wichtigsten Fehler habe, die zu den erkannten Anomalien beigetragen haben, möchte ich die Auswirkungen dieser Fehler auf mein System verstehen. Die Tabelle "Logs" enthält zusätzliche dimensionale Daten, z. b. "Component", "Cluster" usw... Mit dem neuen Plug-in "Autocluster" können Sie diesen Einblick mit einer einfachen Abfrage ableiten. In diesem Beispiel ist klar erkennbar, dass jeder der ersten vier Fehler für eine Komponente spezifisch ist, während die ersten drei Fehler für den db4-Cluster spezifisch sind, der vierte Fehler in allen Clustern.

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

## <a name="mapping-values-from-one-set-to-another"></a>Zuordnung von Werten aus einem Satz zu einem anderen

Ein gängiger Anwendungsfall ist die Verwendung der statischen Zuordnung von Werten, die dazu beitragen können, Ergebnisse in eine besser darstellbare Weise zu übernehmen.  
Angenommen, Sie haben die nächste Tabelle. DeviceModel gibt ein Modell des Geräts an, das keine sehr bequeme Form der Referenzierung auf den Gerätenamen ist.  


|DeviceModel |Anzahl 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

  
Eine bessere Darstellung könnte wie folgt lauten:  

|FriendlyName |Anzahl 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Die beiden folgenden Ansätze veranschaulichen, wie dies erreicht werden kann.  

### <a name="mapping-using-dynamic-dictionary"></a>Zuordnung mithilfe des dynamischen Wörterbuchs

Der folgende Ansatz zeigt, wie die Zuordnung mithilfe eines dynamischen Wörterbuchs und dynamischer Accessoren erreicht werden kann.

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



### <a name="mapping-using-static-table"></a>Zuordnung mithilfe statischer Tabellen

Der folgende Ansatz zeigt, wie die Zuordnung mithilfe eines permanenten Tabellen-und joinoperators erreicht werden kann.
 
Erstellen Sie die Mapping-Tabelle (Just Once):

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Inhalt von Geräten jetzt:

|DeviceModel |FriendlyName 
|---|---
|iPhone5, 1 |iPhone 5 
|iPhone3, 2 |iPhone 4 
|iPhone7, 2 |iPhone 6 
|iPhone5, 2 |iPhone5 


Gleicher Trick zum Erstellen einer Test Tabellen Quelle:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


Beitreten und Projekt:

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


## <a name="creating-and-using-query-time-dimension-tables"></a>Erstellen und Verwenden von Abfragezeit-Dimensions Tabellen

In vielen Fällen möchte man die Ergebnisse einer Abfrage mit einer Ad-hoc-Dimensions Tabelle verknüpfen, die nicht in der Datenbank gespeichert ist. Es ist möglich, einen Ausdruck zu definieren, dessen Ergebnis eine Tabelle ist, die auf eine einzelne Abfrage beschränkt ist. Dies geschieht wie folgt:

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>Abrufen der neuesten Datensätze (nach Zeitstempel) pro Identität

Angenommen, Sie verfügen über eine Tabelle, die eine `id` Spalte (identifiziert die Entität, der die einzelnen Zeilen zugeordnet sind, z. b. eine Benutzer-ID oder eine Knoten-ID) und eine `timestamp` Spalte (die den Zeit Verweis für die Zeile bereitstellt) sowie andere Spalten enthält. Ihr Ziel ist es, eine Abfrage zu schreiben, die die letzten 2 Datensätze für jeden Wert der Spalte zurückgibt `id` , wobei "Latest" als "mit dem höchsten Wert von" definiert ist `timestamp` .

Dies kann mithilfe des [Top-netsted-Operators](topnestedoperator.md)erreicht werden.
Zuerst stellen wir die Abfrage bereit und erläutern Sie dann:

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // (1)
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // (2)
  top-nested 2 of timestamp by dummy1=max(timestamp),          // (3)
  top-nested   of bla       by dummy2=max(1)                   // (4)
| project-away dummy0, dummy1, dummy2                          // (5)
```

Hinweise
1. Der `datatable` ist nur eine Möglichkeit, um zu Demonstrationszwecken einige Testdaten zu entwickeln. Natürlich haben Sie die Daten hier.
2. Diese Zeile bedeutet im Wesentlichen, dass alle unterschiedlichen Werte von zurückgegeben werden `id` .
3. Diese Zeile gibt dann für die obersten 2 Datensätze, die die `timestamp` Spalte maximieren, die Spalten der vorherigen Ebene (hier, einfach `id` ) und die auf dieser Ebene (hier) angegebene Spalte zurück `timestamp` .
4. Diese Zeile fügt die Werte der `bla` Spalte für jeden der von der vorherigen Ebene zurückgegebenen Datensätze hinzu. Wenn die Tabelle über andere relevante Spalten verfügt, wird diese Zeile für jede dieser Spalten wiederholt.
5. Zum Schluss verwenden wir den [Projekt](projectawayoperator.md) Entfernungs Operator, um die "zusätzlichen" Spalten zu entfernen, die mit eingeführt wurden `top-nested` .

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>Erweitern einer Tabelle mit einer prozentualen Gesamtberechnung

Wenn eine Tabelle einen tabellarischen Ausdruck enthält, der eine numerische Spalte enthält, ist es häufig wünschenswert, dass diese Spalte zusammen mit dem Wert als Prozentsatz des Gesamtwerts für den Benutzer angezeigt werden kann. Nehmen wir beispielsweise an, dass es eine Abfrage gibt, deren Wert der folgenden Tabelle entspricht:

|Someseries|Someint|
|----------|-------|
|Foo       |    100|
|Balkendiagramm       |    200|

Und Sie möchten diese Tabelle wie folgt anzeigen:

|Someseries|Someint|P |
|----------|-------|----|
|Foo       |    100|33,3|
|Balkendiagramm       |    200|66,6|

Zu diesem Zweck muss die Summe (Summe) der `SomeInt` Spalte berechnet werden, und dann werden die einzelnen Werte dieser Spalte durch die Gesamtsumme dividiert. Sie können dies für beliebige Ergebnisse tun, indem Sie diesen Ergebnissen einen Namen mit dem [as-Operator](asoperator.md)geben:

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

## <a name="performing-aggregations-over-a-sliding-window"></a>Ausführen von Aggregationen über ein gleitender Fenster
Im folgenden Beispiel wird gezeigt, wie Spalten mithilfe eines gleitenden Fensters zusammengefasst werden. Beispielsweise wird die folgende Tabelle angenommen, die die Preise für Früchte nach Zeitstempeln enthält. Angenommen, Sie möchten die minimalen, maximalen und Summen Kosten pro Tag mit einem gleitenden Fenster von 7 Tagen berechnen. Das heißt, dass jeder Datensatz im Resultset die letzten 7 Tage aggregiert und das Ergebnis einen Datensatz pro Tag im Analysezeitraum enthält.  

Tabelle "Früchte": 

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

Aggregations Abfrage für gleitendes Fenster (Erläuterung finden Sie Unterabfrage Ergebnisse): 

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

Die Abfrage "gestreckt" (Duplikate) jeden Datensatz in der Eingabe Tabelle innerhalb von 7 Tagen nach der tatsächlichen Darstellung, sodass jeder Datensatz tatsächlich 7 Mal angezeigt wird. Folglich enthält die Aggregation beim Ausführen der Aggregation pro Tag alle Datensätze der letzten 7 Tage.

Schritt-für-Schritt-Erläuterung (Zahlen verweisen auf die Zahlen in Inline Kommentaren der Abfrage):
1. Klassifizieren Sie jeden Datensatz in 1D (relativ zu _start). 
2. Bestimmen Sie das Ende des Bereichs pro Datensatz-_bin + 7D, es sei denn, dieser Wert liegt außerhalb des Bereichs _(Anfang, Ende)_ . in diesem Fall wird er angepasst. 
3. Erstellen Sie für jeden Datensatz ein Array von 7 Tagen (Zeitstempel), beginnend mit dem Tag des aktuellen Datensatzes. 
4. MV-erweitern Sie das Array, und Duplizieren Sie die einzelnen Datensätze in sieben Datensätze, wobei Sie einen Tag voneinander trennen. 
5. Führen Sie die Aggregations Funktion für jeden Tag aus. Aufgrund #4 werden diese tatsächlich in den _letzten_ sieben Tagen zusammengefasst. 
6. Da die Daten für die erste 7D unvollständig sind (in den ersten 7 Tagen gibt es keinen 7D-loobackzeitraum), schließen wir die ersten 7 Tage aus dem Endergebnis aus (Sie sind nur an der Aggregation für 2018-10-01 beteiligt). 

## <a name="find-preceding-event"></a>Vorheriges Ereignis suchen
Im nächsten Beispiel wird veranschaulicht, wie Sie ein vorhergehenden Ereignis zwischen zwei Datasets finden.  

*Zweck:* : zwei Datasets, A und B, für jeden Datensatz in B suchen das vorangehende Ereignis in einem (anders ausgedrückt: der ARG_MAX Datensatz in einer, die immer noch "älter" als B ist). Im folgenden Beispiel wird die erwartete Ausgabe für die folgenden Beispiel Datasets aufgeführt: 

```kusto
let A = datatable(Timestamp:datetime, Id:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, Id:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Timestamp|Id|Eventb|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|Timestamp|Id|Eventa|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

Erwartete Ausgabe: 

|Id|Timestamp|Eventb|A_Timestamp|Eventa|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Es gibt zwei verschiedene Ansätze, die für dieses Problem vorgeschlagen werden. Sie sollten beide in Ihrem speziellen DataSet testen, um das für Sie am besten geeignete DataSet zu finden (die unterschiedlichen Datensätze können unterschiedlich sein). 

### <a name="suggestion-1"></a>Vorschlag #1:
Dieser Vorschlag serialisiert beide Datasets nach ID und TIMESTAMP, gruppiert dann alle B-Ereignisse mit all ihren vorangehenden Ereignissen und wählt die `arg_max` out-of-all-Werte in der Gruppe aus. 

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by Id, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != Id), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, Id
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>Vorschlag #2:
Dieser Vorschlag erfordert die Definition eines Max-lookbackzeitraums (wie viel "älter" ist, dass der Datensatz in einem mit "B" verglichen werden kann?) und dann die beiden Datasets mit der ID und diesem Nachschlage Zeitraum verbindet. Der JOIN erzeugt alle möglichen Kandidaten (alle Datensätze, die älter als b und innerhalb des Nachschlage Zeitraums sind) und filtert dann die nächstgelegene Tabelle nach b durch arg_min (timestampb – timestampa). Der kleinere loobackzeitraum ist, dass die Abfrage eine bessere Leistung erwartet. 

Im folgenden Beispiel wird der Nachschlage Zeitraum auf 1 m festgelegt, sodass der Datensatz mit der ID "z" kein entsprechendes "a"-Ereignis hat (da "a" älter als 2 m ist).

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
) on Id, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project Id, B_Timestamp, A_Timestamp, EventB, EventA
```

|Id|B_Timestamp|A_Timestamp|Eventb|Eventa|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||
