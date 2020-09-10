---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: 40334f81e39317839c05ce09a2e4923be4e0747c
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502658"
---
### <a name="schema-mapping-examples"></a>Beispiele für die Schemazuordnung

**Beispiel für die Tabellenschemazuordnung**

Wenn Ihre Daten drei Spalten (`Timespan`, `Metric` und `Value`) enthalten und die Eigenschaften, die Sie einschließen, `x-opt-enqueued-time` und `x-opt-offset` lauten, erstellen oder ändern Sie das Tabellenschema mit dem folgenden Befehl:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Beispiel für eine CSV-Zuordnung**

Führen Sie die folgenden Befehle aus, um am Anfang des Datensatzes Daten hinzuzufügen. Beachten Sie die Ordinalwerte.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Beispiel für eine JSON-Zuordnung**

Daten werden mithilfe der Systemeigenschaftenzuordnung hinzugefügt. Führen Sie diese Befehle aus:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
