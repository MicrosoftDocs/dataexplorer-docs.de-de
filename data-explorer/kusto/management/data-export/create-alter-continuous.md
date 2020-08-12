---
title: 'Erstellen oder Ändern von kontinuierlichen Daten Exporten: Azure-Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie den fortlaufenden Datenexport in Azure Daten-Explorer erstellen oder ändern.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b75ac4fe4b03c85eec74ccc69c5c03ff59a4bcfe
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149515"
---
# <a name="create-or-alter-continuous-export"></a>Erstellen oder Ändern von fortlaufendem Export

Erstellt oder ändert einen fortlaufenden Exportauftrag.

## <a name="syntax"></a>Syntax

`.create-or-alter` `continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*, *T2* `)` ] <br>
`to``table` *Externaltablename* <br> [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]<br>
\<| *Such*

## <a name="properties"></a>Eigenschaften

| Eigenschaft             | type     | BESCHREIBUNG   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | Name des fortlaufenden Exports. Der Name muss innerhalb der Datenbank eindeutig sein und wird verwendet, um den fortlaufenden Export regelmäßig auszuführen.      |
| Externaltablename    | String   | Name der [externen Tabelle](../externaltables.md) , in die exportiert werden soll.  |
| Abfrage                | String   | Die zu exportier Abfrage.  |
| Over (T1, T2)        | String   | Eine optionale durch Trennzeichen getrennte Liste von Fakten Tabellen in der Abfrage. Wenn nicht angegeben, wird davon ausgegangen, dass alle Tabellen, auf die in der Abfrage verwiesen wird, Fakten Tabellen sind. Wenn angegeben, werden Tabellen, die *nicht* in dieser Liste enthalten sind, als Dimensions Tabellen behandelt und werden nicht als Bereichs bezogen betrachtet (alle Datensätze werden an allen Exporten beteiligt). Ausführliche Informationen finden Sie unter [Übersicht über fortlaufenden Datenexport](continuous-data-export.md) . |
| intervalbetweaufausführungen  | Timespan | Die Zeitspanne zwischen fortlaufenden Export Ausführungen. Muss größer als 1 Minute sein.   |
| forcedlatency        | Timespan | Ein optionaler Zeitraum, um die Abfrage auf Datensätze zu beschränken, die nur vor diesem Zeitraum (relativ zur aktuellen Zeit) erfasst wurden. Diese Eigenschaft ist nützlich, wenn die Abfrage z. b. einige Aggregationen/Joins ausführt und Sie sicherstellen möchten, dass alle relevanten Datensätze bereits vor dem Ausführen des Exports erfasst wurden.

Zusätzlich zu den oben genannten werden alle Eigenschaften unterstützt, die im Befehl "in [externe Tabelle exportieren](export-data-to-an-external-table.md) " unterstützt werden. 

## <a name="example"></a>Beispiel

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| Name     | Externaltablename | Abfrage | Forcedlatency | Intervalbetweaufausführungen | Cursor scopedtables         | Export Properties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| Myexport | Externalblob      | E     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']. ['] '<br>] | {<br>  "SizeLimit": 104857600<br>} |
