---
title: 'R-Plug-in (Vorschau): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird das R-Plug-in (Vorschau) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 514c67133980c9ab1c38b65cc51e4592dcb15eda
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618960"
---
# <a name="r-plugin-preview"></a>R-Plug-in (Vorschau)

::: zone pivot="azuredataexplorer"

Das r-Plug-in führt eine benutzerdefinierte Funktion (UDF) mithilfe eines r-Skripts aus. Das R-Skript ruft Tabellendaten als Eingabe ab, und es wird erwartet, dass eine tabellarische Ausgabe erzeugt wird.
Die Laufzeit des Plug-ins wird in einem [Sandkasten](../concepts/sandboxes.md)gehostet, eine isolierte und sichere Umgebung, die auf den Knoten des Clusters ausgeführt wird.

### <a name="syntax"></a>Syntax

*T* `|` `per_node``,` *script_parameters* *output_schema* *script* [`hint.distribution` (`single`)] `r(`output_schema`,` Skript [script_parameters] `evaluate` `=`  | `)`


### <a name="arguments"></a>Argumente

* *output_schema*: ein `type` Literalwert, der das Ausgabe Schema der tabellarischen Daten definiert, die vom R-Code zurückgegeben werden.
    * Das Format ist: `typeof(` *ColumnName* `:` *ColumnType* [,...] `)`Beispiel: `typeof(col1:string, col2:long)`.
    * Um das Eingabe Schema zu erweitern, verwenden Sie die folgende `typeof(*, col1:string, col2:long)`Syntax:.
* *Skript*: ein `string` Literalwert, der das gültige R-Skript ist, das ausgeführt werden soll.
* *script_parameters*: ein optionales `dynamic` literalobjekt, bei dem es sich um einen Eigenschaften Behälter mit Name-Wert-Paaren handelt, `kargs` die an das R-Skript als reserviertes Wörterbuch (siehe [reservierte R-Variablen](#reserved-r-variables))
* *Hint. Distribution*: ein optionaler Hinweis für die Ausführung des Plug-ins, das auf mehrere Cluster Knoten verteilt wird.
   Standardwert: `single`.
    * `single`: Eine einzelne Instanz des Skripts wird über die gesamten Abfrage Daten ausgeführt.
    * `per_node`: Wenn die Abfrage vor der Verteilung des R-Blocks verteilt wird, wird eine Instanz des Skripts auf jedem Knoten über die darin enthaltenen Daten ausgeführt.


### <a name="reserved-r-variables"></a>Reservierte R-Variablen

Die folgenden Variablen sind für die Interaktion zwischen der Kusto-Abfragesprache und dem R-Code reserviert:

* `df`: Die Eingabe Tabellendaten (die Werte von `T` oben) als R-dataframe.
* `kargs`: Der Wert des *script_parameters* Arguments als R-Wörterbuch.
* `result`: Ein r-Datenrahmen, der vom r-Skript erstellt wurde, dessen Wert zu den Tabellendaten wird, die an einen beliebigen Kusto-Abfrage Operator gesendet werden, der auf das Plug-in folgt.

### <a name="onboarding"></a>Onboarding


* Das Plug-in ist standardmäßig deaktiviert.
    * *Möchten Sie das Plug-in in Ihrem Cluster aktivieren?*
        
        * Wählen Sie im Azure-Portal im Azure Daten-Explorer-Cluster im Menü auf der linken Seite die Option **neue Supportanfrage** aus.
        * Das Deaktivieren des Plug-ins erfordert auch das Öffnen eines Support Tickets.

### <a name="notes-and-limitations"></a>Hinweise und Einschränkungen

* Das r Sandbox-Image basiert auf *r 3.4.4 für Windows*und enthält Pakete aus [dem r Essentials-Bundle von Anaconda](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* Die r-Sandbox schränkt den Zugriff auf das Netzwerk ein, daher kann der r-Code keine weiteren Pakete installieren, die nicht im Abbild enthalten sind. Öffnen Sie eine **neue Supportanfrage** in der Azure-Portal, wenn Sie bestimmte Pakete benötigen.


### <a name="examples"></a>Beispiele

```kusto
range x from 1 to 360 step 1
| evaluate r(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result <- df\n'                    //  The R decorated script
'n <- nrow(df)\n'
'g <- kargs$gain\n'
'f <- kargs$cycles\n'
'result$fx <- g * sin(df$x / n * 2 * pi * f)'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="Sinus-Demo" border="false":::

### <a name="performance-tips"></a>Leistungstipps

* Reduzieren Sie die Eingabedaten des Plug-Ins auf den minimal erforderlichen Wert (Spalten/Zeilen).
    * Verwenden Sie nach Möglichkeit Filter für das Quell DataSet mithilfe der Kusto-Abfragesprache.
    * Um eine Berechnung für eine Teilmenge der Quell Spalten auszuführen, projizieren Sie nur diese Spalte, bevor Sie das Plug-in aufrufen.
* Verwenden `hint.distribution = per_node` Sie immer dann, wenn die Logik in Ihrem Skript Verteil Bar ist.
    * Sie können auch den [Partitions Operator](partitionoperator.md) für die Partitionierung des Eingabe Datasets verwenden.
* Verwenden Sie nach Möglichkeit die Kusto-Abfragesprache, um die Logik Ihres R-Skripts zu implementieren.

    Beispiel:

    ```kusto    
    .show operations
    | where StartedOn > ago(1d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node r( // Using per_node distribution, as the script's logic allows it
        typeof(*, d2:double),
        'result <- df\n'
        'result$d2 <- df$d_seconds\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(d2)
    ```

### <a name="usage-tips"></a>Verwendungstipps

* Um Konflikte zwischen Kusto-Zeichen folgen Trennzeichen und r-Zeichen zu vermeiden, empfiehlt es sich, einfache`'`Anführungszeichen () für Kusto-Zeichen folgen Literale in Kusto-Abfragen`"`und doppelte Anführungszeichen () für r-Zeichen folgen Literale in r-Skripts zu verwenden.
* Verwenden Sie den [externaldata-Operator](externaldata-operator.md) zum Abrufen des Inhalts eines Skripts, das Sie an einem externen Speicherort gespeichert haben, z. b. Azure BLOB Storage, ein öffentliches GitHub-Repository usw.
  
  Beispiel:

    ```kusto    
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate r(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

---

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end

