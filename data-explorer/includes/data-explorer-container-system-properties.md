---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493406"
---
### <a name="event-system-properties-mapping"></a>Zuordnung von Ereignissystemeigenschaften

> [!Note]
> * Systemeigenschaften werden für Ereignisse mit einem Datensatz unterstützt.
> * Für die `csv`-Zuordnung werden am Anfang des Datensatzes Eigenschaften hinzugefügt. Bei einer `json`-Zuordnung werden Eigenschaften entsprechend dem in der Dropdownliste angezeigten Namen hinzugefügt.

Wenn Sie **Ereignissystemeigenschaften** im Abschnitt **Datenquelle** der Tabelle ausgewählt haben, müssen Sie die folgenden Eigenschaften in das Tabellenschema und die Zuordnung einfügen.

**Beispiel für ein Tabellenschema**

Wenn Ihre Daten drei Spalten (`Timespan`, `Metric` und `Value`) enthalten und die Eigenschaften, die Sie einschließen, `x-opt-enqueued-time` und `x-opt-offset` lauten, erstellen oder ändern Sie das Tabellenschema mit dem folgenden Befehl:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Beispiel einer CSV-Zuordnung**

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
 
**Beispiel einer JSON-Zuordnung**

Daten werden mithilfe der Namen der Systemeigenschaften hinzugefügt, mit denen sie auf dem Blatt **Datenverbindung** in der Liste **Ereignissystemeigenschaften** angezeigt werden. Führen Sie diese Befehle aus.

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
