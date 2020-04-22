---
title: Beispiele - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Beispiele in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 746017d41b5f1a13ce73f2c27df9cac5b5982ff0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663588"
---
# <a name="samples"></a>Beispiele

Im Folgenden finden Sie einige häufige Abfrageanforderungen und wie die Kusto-Abfragesprache verwendet werden kann, um sie zu erfüllen.

## <a name="display-a-column-chart"></a>Anzeigen eines Säulendiagramms

Projizieren Sie zwei oder mehr Spalten, und verwenden Sie sie als x- und y-Achse eines Diagramms:

```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* Die erste Spalte bildet die x-Achse. Es kann sich um numerische, Datums- oder Zeichenfolgen handelt. 
* Verwenden `where` `summarize` Sie `top` , und begrenzen Sie die angezeigte Datenmenge.
* Sortieren Sie die Ergebnisse, um die Reihenfolge der x-Achse zu definieren.

:::image type="content" source="images/samples/060.png" alt-text="060":::

<a name="activities"></a>
## <a name="get-sessions-from-start-and-stop-events"></a>Abrufen von Sitzungen aus Start- und Stop-Ereignissen

Beispiel: Sie verfügen über ein Protokoll mit Ereignissen, bei denen einige Ereignisse den Anfang oder das Ende einer erweiterten Aktivität oder der Sitzung kennzeichnen. 

|Name|City|SessionID|Timestamp|
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

Verwenden [`let`](./letstatement.md) Sie diese Datei, um eine Projektion der Tabelle zu benennen, die so weit wie möglich abgespeckt wird, bevor Sie in die Verknüpfung wechseln.
[`Project`](./projectoperator.md)wird verwendet, um die Namen der Zeitstempel so zu ändern, dass sowohl die Start- als auch die Stoppzeiten im Ergebnis angezeigt werden können. Es werden auch die anderen Spalten ausgewählt, die im Ergebnis angezeigt werden sollen. [`join`](./joinoperator.md)entspricht den Start- und Stoppeinträgen für dieselbe Aktivität, wodurch für jede Aktivität eine Zeile erstellt wird. Mit `project` wird schließlich erneut eine Spalte hinzugefügt, um die Dauer der Aktivität anzuzeigen.


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

Dann gruppieren wir nach Startzeit und IP, um eine Gruppe für jede Sitzung zu erhalten. Wir müssen `bin` eine Funktion für den StartTime-Parameter bereitstellen: Wenn wir das nicht tun, verwendet Kusto automatisch 1-Stunden-Behälter, die einige Startzeiten mit den falschen Stoppzeiten übereinstimmen.

`arg_min`wählt die Zeile mit der kleinsten Dauer `*` in jeder Gruppe aus, und der Parameter durchläuft alle anderen Spalten, obwohl er jedem Spaltennamen "min_" voranstellt. 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

Dann können wir Code hinzufügen, um die Dauern in Abschnitten mit bequemer Größe zu zählen. Wir haben eine leichte Präferenz für ein Balkendiagramm, also dividieren wir uns, `1s` um die Zeiträume in Zahlen umzuwandeln. 


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

Wir wollen ein Diagramm in 1-Minuten-Abschnitte, also wollen wir etwas erstellen, das wir bei jedem 1m-Intervall für jede laufende Aktivität zählen können.

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

Aber anstatt diese Arrays zu behalten, erweitern wir sie mit [mv-expand](./mvexpandoperator.md):

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

* Wir müssen todatetime() da [mv-expand](./mvexpandoperator.md) eine Spalte des dynamischen Typs ergibt.
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

## <a name="introduce-null-bins-into-summarize"></a>Null-Abschnitte in die Zusammenfassung einführen

Wenn `summarize` der Operator auf einen Gruppenschlüssel `datetime` angewendet wird, der aus einer Spalte besteht, "wird" diese Werte normalerweise in Lagerplätze mit fester Breite "geteilt". Zum Beispiel:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Bei diesem Vorgang wird eine Tabelle mit `T` einer einzelnen Zeile pro Zeilengruppe erstellt, die in jeden Abschnitt mit fünf Minuten fällt. Was sie nicht tut, ist "null bins" hinzuzufügen -- `StopTime` Zeilen für Zeitkorbwerte `T`zwischen `StartTime` und für die es keine entsprechende Zeile in gibt. 

Oft ist es wünschenswert, den Tisch mit diesen Behältern zu "paddeln". Hier ist eine Möglichkeit, es zu tun:

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

Hier ist eine schritt-für-Schritt-Erklärung der obigen Abfrage: 

1. Mit `union` dem Operator können wir zusätzliche Zeilen zu einer Tabelle hinzufügen. Diese Zeilen werden durch `union`den Ausdruck zu erzeugt.
2. Verwenden `range` des Operators zum Erstellen einer Tabelle mit einer einzelnen Zeile/Spalte.
   Die Tabelle wird nicht für `mv-expand` etwas anderes als für die Arbeit verwendet.
3. Verwenden `mv-expand` des Operators `range` über die Funktion, um so viele Zeilen `StartTime` `EndTime`zu erstellen, wie 5-Minuten-Abschnitte zwischen und vorhanden sind.
4. Alle mit `Count` `0`einem von .
5. Schließlich verwenden wir `summarize` den Operator, um Lagerplätze aus dem ursprünglichen (linken oder äußeren) Argument zu `union` gruppieren, und bins aus dem inneren Argument zu ihm (nämlich die Null-Bin-Zeilen). Dadurch wird sichergestellt, dass die Ausgabe eine Zeile pro Lagerplatz hat, deren Wert entweder Null oder die ursprüngliche Anzahl ist.  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>Holen Sie mehr aus Ihren Daten in Kusto mit Machine Learning 

Es gibt viele interessante Anwendungsfälle für die Nutzung von Algorithmen für maschinelles Lernen und interessante Erkenntnisse aus Telemetriedaten. Während diese Algorithmen häufig ein sehr strukturiertes Dataset als Eingabe erfordern, stimmen die Rohprotokolldaten in der Regel nicht mit der erforderlichen Struktur und Größe überein. 

Unsere Reise beginnt mit der Suche nach Anomalien in der Fehlerrate eines bestimmten Bing-Inferenzdienstes. Die Tabelle Protokolle enthält 65B-Datensätze, und die einfache Abfrage unten filtert 250K-Fehler und erstellt eine Zeitreihendatenanzahl von Fehler, die die Anomalieerkennungsfunktion [series_decompose_anomalies](series-decompose-anomaliesfunction.md)verwendet. Die Anomalien werden vom Kusto-Dienst erkannt und als rote Punkte im Zeitreihendiagramm hervorgehoben.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Der Dienst identifizierte nur wenige Zeit-Buckets mit verdächtiger Fehlerrate. Ich verwende Kusto, um in diesen Zeitrahmen zu zoomen und eine Abfrage auszuführen, die in der Spalte "Nachricht" aggregiert und versucht, nach den obersten Fehlern zu suchen. Ich habe die relevanten Teile aus der gesamten Stapelablaufverfolgung der Nachricht getrimmt, um besser in die Seite zu passen. Sie können sehen, dass ich mit den ersten acht Fehlern einen schönen Erfolg hatte, aber dann einen langen Fehlerschwanz erreichte, da die Fehlermeldung von einer Formatzeichenfolge erstellt wurde, die sich ändernde Daten enthielt. 

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
|7125|ExecuteAlgorithmMethod für die Methode 'RunCycleFromInterimData' ist fehlgeschlagen...
|7125|InferenceHostService-Aufruf fehlgeschlagen. System.NullReferenceException: Objektverweis ist nicht auf eine Instanz eines Objekts festgelegt...
|7124|Unerwarteter Inferenzsystemfehler.. System.NullReferenceException: Objektverweis ist nicht auf eine Instanz eines Objekts festgelegt... 
|5112|Unerwarteter Inferenzsystemfehler.. System.NullReferenceException: Objektverweis ist nicht auf eine Instanz eines Objekts festgelegt.
|174|InferenceHostService-Aufruf failed..System.ServiceModel.CommunicationException: Fehler beim Schreiben in die Pipe:...
|10|ExecuteAlgorithmMethod für die Methode 'RunCycleFromInterimData' ist fehlgeschlagen...
|10|Inferenz Systemfehler.. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|3|InferenceHostService-Aufruf failed..System.ServiceModel.CommunicationObjectFaultedException:...
|1|InferenzSystemfehler... SocialGraph.BOSS.OperationResponse... AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|InferenzSystemfehler... SocialGraph.BOSS.OperationResponse... AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF...

Hier kommt `reduce` der Bediener zu Hilfe. Der `reduce` Operator identifizierte 63 verschiedene Fehler, die vom gleichen Ablaufverfolgungsmesspunkt im Code stammen, und half mir, mich auf zusätzliche sinnvolle Fehlerspuren in diesem Zeitfenster zu konzentrieren.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Anzahl|Muster
|---|---
|7125|ExecuteAlgorithmMethod für die Methode 'RunCycleFromInterimData' ist fehlgeschlagen...
|  7125|InferenceHostService-Aufruf fehlgeschlagen. System.NullReferenceException: Objektverweis ist nicht auf eine Instanz eines Objekts festgelegt...
|  7124|Unerwarteter Inferenzsystemfehler.. System.NullReferenceException: Objektverweis ist nicht auf eine Instanz eines Objekts festgelegt...
|  5112|Unerwarteter Inferenzsystemfehler.. System.NullReferenceException: Objektverweis ist nicht auf eine Instanz eines Objekts festgelegt.
|  174|InferenceHostService-Aufruf failed..System.ServiceModel.CommunicationException: Fehler beim Schreiben in die Pipe:...
|  63|Inferenz Systemfehler.. Microsoft.Bing.Platform.Inschlusses. \*: \* Schreiben Sie, um in das Objekt BOSS zu schreiben. \*: SocialGraph.BOSS.Reques...
|  10|ExecuteAlgorithmMethod für die Methode 'RunCycleFromInterimData' ist fehlgeschlagen...
|  10|Inferenz Systemfehler.. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|  3|InferenceHostService-Aufruf fehlgeschlagen. System.ServiceModel. \*: Das Objekt, System.ServiceModel.Channels. \* \* \*, \* für ist die ... + \*   bei Syst...

Nun, da ich einen guten Blick auf die Top-Fehler habe, die zu den erkannten Anomalien beigetragen haben, möchte ich die Auswirkungen dieser Fehler in meinem System verstehen. Die Tabelle "Logs" enthält zusätzliche Dimensionsdaten wie 'Komponente', 'Cluster' usw. Das neue 'Autocluster' Plugin kann mir helfen, diese Einsicht mit einer einfachen Abfrage abzuleiten. In diesem Beispiel unten kann ich deutlich sehen, dass jeder der vier wichtigsten Fehler spezifisch für eine Komponente ist, und während die drei wichtigsten Fehler spezifisch für DB4-Cluster sind, tritt der vierte Fehler in allen Clustern auf.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Anzahl |Prozent (%)|Komponente|Cluster|`Message`
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|ExecuteAlgorithmMethod für Methode ....
|7125|26.64|Unbekannte Komponente|DB4|InferenceHostService-Aufruf fehlgeschlagen....
|7124|26.64|InferenceAlgorithmExecutor|DB4|Unerwarteter Inferenzsystemfehler...
|5112|19.11|InferenceAlgorithmExecutor|*|Unerwarteter Inferenzsystemfehler... 

## <a name="mapping-values-from-one-set-to-another"></a>Zuordnen von Werten von einem Satz zu einem anderen

Ein häufiger Anwendungsfall ist die statische Zuordnung von Werten, die bei der Annahme von Ergebnissen auf eine vorzederbarere Weise helfen können.  
Erwägen Sie z. B. die nächste Tabelle. DeviceModel gibt ein Modell des Geräts an, das keine sehr bequeme Form der Referenzierung auf den Gerätenamen ist.  


|DeviceModel |Anzahl 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

  
Eine bessere Darstellung kann sein:  

|FriendlyName |Anzahl 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Die beiden folgenden Ansätze zeigen, wie dies erreicht werden kann.  

### <a name="mapping-using-dynamic-dictionary"></a>Zuordnen mit dynamischem Wörterbuch

Der folgende Ansatz zeigt, wie die Zuordnung mit einem dynamischen Wörterbuch und dynamischen Accessoren erreicht werden kann.

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



### <a name="mapping-using-static-table"></a>Mapping mit statischer Tabelle

Der folgende Ansatz zeigt, wie die Zuordnung mithilfe eines persistenten Tabellen- und Verknüpfungsoperators erreicht werden kann.
 
Erstellen Sie die Zuordnungstabelle (nur einmal):

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Inhalt der Geräte jetzt:

|DeviceModel |FriendlyName 
|---|---
|iPhone5,1 |iPhone 5 
|iPhone3,2 |iPhone 4 
|iPhone7,2 |iPhone 6 
|iPhone5,2 |iPhone5 


Derselbe Trick zum Erstellen der Testtabelle Quelle:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


Mitmachen und Projekt:

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


## <a name="creating-and-using-query-time-dimension-tables"></a>Erstellen und Verwenden von Abfragezeitdimensionstabellen

In vielen Fällen möchte man die Ergebnisse einer Abfrage mit einer Ad-hoc-Dimensionstabelle verbinden, die nicht in der Datenbank gespeichert ist. Es ist möglich, einen Ausdruck zu definieren, dessen Ergebnis eine Tabelle ist, die auf eine einzelne Abfrage beschränkt ist, indem Sie so etwas wie folgt tun:

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

Hier ist ein etwas komplexeres Beispiel:

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

Angenommen, Sie verfügen über `id` eine Tabelle, die eine Spalte (die die Entität identifiziert, der `timestamp` jede Zeile zugeordnet ist, z. B. eine Benutzer-ID oder eine Knoten-ID) und eine Spalte (die den Zeitverweis für die Zeile bereitstellt) sowie andere Spalten identifiziert. Ihr Ziel ist es, eine Abfrage zu schreiben, `id` die die letzten 2 Datensätze für jeden Wert `timestamp`der Spalte zurückgibt, wobei "zuletzt" als "mit dem höchsten Wert von " definiert ist.

Dies kann mit dem [top-verschachtelten Operator](topnestedoperator.md)erfolgen.
Zuerst stellen wir die Abfrage zur Verfügung, und dann erklären wir sie:

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
1. Die `datatable` ist nur eine Möglichkeit, einige Testdaten für Demonstrationszwecke zu erstellen. In Wirklichkeit hätten Sie die Daten hier natürlich.
2. Diese Zeile bedeutet im Wesentlichen `id`"alle unterschiedlichen Werte von zurückgeben".
3. Diese Zeile gibt dann für die obersten `timestamp` 2 Datensätze, die die Spalte `id`maximieren, die Spalten der vorherigen `timestamp`Ebene (hier, nur) und die auf dieser Ebene angegebene Spalte (hier, ) zurück.
4. Diese Zeile fügt die `bla` Werte der Spalte für jeden datensatz hinzu, der von der vorherigen Ebene zurückgegeben wird. Wenn die Tabelle andere Spalten von Interesse hat, würde man diese Zeile für jede solche Spalte wiederholen.
5. Schließlich verwenden wir den [Projekt-Away-Operator,](projectawayoperator.md) um die `top-nested`"extra"-Spalten zu entfernen, die von eingeführt wurden.

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>Erweitern einer Tabelle mit einer Gesamtberechnung von einigen Prozenten

Wenn man einen tabellenförmigen Ausdruck hat, der eine numerische Spalte enthält, ist es häufig erforderlich, diese Spalte dem Benutzer neben seinem Wert als Prozentsatz der Summe darzustellen. Angenommen, es gibt eine Abfrage, deren Wert die folgende Tabelle ist:

|SomeSeries|SomeInt|
|----------|-------|
|Foo       |    100|
|Balkendiagramm       |    200|

Und Sie möchten diese Tabelle wie:

|SomeSeries|SomeInt|Pct |
|----------|-------|----|
|Foo       |    100|33,3|
|Balkendiagramm       |    200|66,6|

Dazu muss man die Summe (Summe) der `SomeInt` Spalte berechnen und dann jeden Wert dieser Spalte durch die Summe dividieren. Es ist möglich, dies für willkürliche Ergebnisse zu tun, indem diesen Ergebnissen ein Name mit dem [as-Operator](asoperator.md)gegeben wird:

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

## <a name="performing-aggregations-over-a-sliding-window"></a>Durchführen von Aggregationen über ein Schiebefenster
Das folgende Beispiel zeigt, wie Spalten mithilfe eines Schiebefensters zusammengefasst werden. Nehmen wir zum Beispiel die folgende Tabelle, die die Preise für Früchte nach Zeitstempeln enthält. Angenommen, wir möchten die Min-, Max- und Summenkosten für jede Frucht pro Tag berechnen, wobei wir ein Schiebefenster von 7 Tagen verwenden. Mit anderen Worten, jeder Datensatz im Resultset aggregiert die letzten 7 Tage, und das Ergebnis enthält einen Datensatz pro Tag in der Analyseperiode.  

Obsttisch: 

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

Schiebefensteraggregationsabfrage (Erläuterung enden unten zu den Abfrageergebnissen): 

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

Abfragedetails: 

Die Abfrage "dehnt" (dupliziert) jeden Datensatz in der Eingabetabelle während 7 Tagen nach seinem tatsächlichen Erscheinungsbild, so dass jeder Datensatz tatsächlich 7 Mal angezeigt wird. Daher umfasst die Aggregation beim Durchführen der Aggregation pro Tag alle Datensätze der vorherigen 7 Tage.

Schritt-für-Schritt-Erklärung (Zahlen beziehen sich auf die Zahlen in Abfrage-Inline-Kommentaren):
1. Bin jeder Datensatz auf 1d (relativ zu _start). 
2. Bestimmen Sie das Ende des Bereichs pro Datensatz - _bin + 7d, es sei denn, dieser liegt aneben _(Start, Ende),_ in diesem Fall wird er angepasst. 
3. Erstellen Sie für jeden Datensatz ein Array von 7 Tagen (Zeitstempel), beginnend mit dem aktuellen Datensatztag. 
4. mv-expand das Array, wodurch jeder Datensatz auf 7 Datensätze dupliziert wird, 1 Tag voneinander entfernt. 
5. Führen Sie die Aggregationsfunktion für jeden Tag aus. Aufgrund #4 fasst dies die _letzten_ 7 Tage zusammen. 
6. Da die Daten für die ersten 7d unvollständig sind (es gibt keine 7d-Nachwartezeit für die ersten 7 Tage), schließen wir die ersten 7 Tage vom Endergebnis aus (sie nehmen nur an der Aggregation für den 2018-10-01 teil). 

## <a name="find-preceding-event"></a>Vorherige s. ereignissuche
Im nächsten Beispiel wird veranschaulicht, wie ein vorhergehendes Ereignis zwischen zwei Datensätzen gefunden wird.  

*Zweck:* : Bei 2 Datensätzen, A und B, finden Sie bei jedem Datensatz in B das vorhergehende Ereignis in A (d. h. der arg_max Datensatz in A, der noch "älter" als B ist). Im Folgenden finden Sie z. B. die erwartete Ausgabe für die folgenden Beispieldatensätze: 

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

|Timestamp|Id|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|Timestamp|Id|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

Erwartete Ausgabe: 

|Id|Timestamp|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Es gibt 2 verschiedene Ansätze für dieses Problem vorgeschlagen. Sie sollten beide auf Ihrem spezifischen Datensatz testen, um den für Sie am besten geeigneten Datensatz zu finden (sie können in verschiedenen Datensätzen unterschiedlich funktionieren). 

### <a name="suggestion-1"></a>Vorschlag #1:
Dieser Vorschlag serialisiert beide Datensätze nach ID und Zeitstempel, gruppiert dann alle B-Ereignisse mit allen vorhergehenden A-Ereignissen und wählt die `arg_max` aus allen As in der Gruppe aus. 

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
Dieser Vorschlag erfordert die Definition einer max-lookback-Periode (wie viel "älter" erlauben wir, den Datensatz in A mit B zu vergleichen?) und schließt sich dann den beiden Datensätzen auf ID und dieser Lookback-Periode an. Die Verknüpfung erzeugt alle möglichen Kandidaten (alle A-Datensätze, die älter als B und innerhalb des Lookback-Zeitraums sind), und wir filtern dann den nächstgelegenen Kandidaten nach B nach arg_min (TimestampB – TimestampA). Je kleiner der Lookback-Zeitraum ist, desto besser wird erwartet, dass die Abfrage eine bessere Leistung erarbeitet. 

Im folgenden Beispiel wird die Lookback-Periode auf 1m festgelegt, und daher hat der Datensatz mit id 'z' kein entsprechendes 'A'-Ereignis (da es 'A' um 2m alt ist).

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

|Id|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||