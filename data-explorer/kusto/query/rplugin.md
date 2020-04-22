---
title: R-Plugin (Vorschau) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt das R-Plugin (Vorschau) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d815a75b241f7779a5f4ee9cae626c38ed54f9f4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765999"
---
# <a name="r-plugin-preview"></a>R-Plugin (Vorschau)

::: zone pivot="azuredataexplorer"

Das R-Plugin führt eine benutzerdefinierte Funktion (UDF) mit einem R-Skript aus. Das R-Skript ruft Tabellarische Daten als Eingabe ab und wird voraussichtlich eine tabellarische Ausgabe erzeugen.
Die Laufzeit des Plugins wird in einer [Sandbox](../concepts/sandboxes.md)gehostet , einer isolierten und sicheren Umgebung, die auf den Knoten des Clusters ausgeführt wird.

### <a name="syntax"></a>Syntax

*T* `|` `=` `single` | `per_node` `r(` *output_schema* `,` *script* `,` [ ( )] output_schema Skript [ script_parameters ] *script_parameters* `evaluate` `hint.distribution``)`


### <a name="arguments"></a>Argumente

* *output_schema*: `type` Ein Literal, das das Ausgabeschema der Tabellendaten definiert, das vom R-Code zurückgegeben wird.
    * Das Format `typeof(`lautet: *ColumnName* `:` *ColumnType* [, ...] `)`, z. `typeof(col1:string, col2:long)`B.: .
    * Um das Eingabeschema zu erweitern, `typeof(*, col1:string, col2:long)`verwenden Sie die folgende Syntax: .
* *script*: `string` Ein Literal, das das gültige R-Skript ist, das ausgeführt werden soll.
* *script_parameters*: `dynamic` Ein optionales Literal, das ein Eigenschaftenbeutel mit Namen/Wert-Paaren ist, der als reserviertes `kargs` Wörterbuch an das R-Skript übergeben werden soll (siehe [Reservierte R-Variablen](#reserved-r-variables)).
* *hint.distribution*: Ein optionaler Hinweis für die Ausführung des Plugins, der auf mehrere Clusterknoten verteilt werden soll.
   Standard: `single`
    * `single`: Eine einzelne Instanz des Skripts wird über die gesamten Abfragedaten ausgeführt.
    * `per_node`: Wenn die Abfrage vor dem R-Block verteilt wird, wird auf jedem Knoten eine Instanz des Skripts über die darin enthaltenen Daten ausgeführt.


### <a name="reserved-r-variables"></a>Reservierte R-Variablen

Die folgenden Variablen sind für die Interaktion zwischen Kusto Query Language und dem R-Code reserviert:

* `df`: Die Eingabe-Tabellarischen `T` Daten (die oben genannten Werte) als R DataFrame.
* `kargs`: Der Wert des *script_parameters-Arguments* als R-Wörterbuch.
* `result`: Ein R DataFrame, der vom R-Skript erstellt wurde, dessen Wert zu den Tabellendaten wird, die an jeden Kusto-Abfrageoperator gesendet werden, der dem Plugin folgt.

### <a name="onboarding"></a>Onboarding


* Das Plugin ist standardmäßig deaktiviert.
    * *Möchten Sie das Plugin auf Ihrem Cluster aktivieren?*
        
        * Wählen Sie im Azure-Portal in Ihrem Azure Data Explorer-Cluster im linken Menü **neue Supportanfrage** aus.
        * Das Deaktivieren des Plugins erfordert auch das Öffnen eines Support-Tickets.

### <a name="notes-and-limitations"></a>Hinweise und Einschränkungen

* Das R-Sandbox-Image basiert auf *R 3.4.4 für Windows*und enthält Pakete aus [Anacondas R Essentials-Bundle](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* Die R-Sandbox beschränkt den Zugriff auf das Netzwerk, daher kann der R-Code keine zusätzlichen Pakete dynamisch installieren, die nicht im Abbild enthalten sind. Öffnen Sie eine **neue Supportanfrage** im Azure-Portal, wenn Sie bestimmte Pakete benötigen.


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

:::image type="content" source="images/samples/sine-demo.png" alt-text="Sinus-Demo":::

### <a name="performance-tips"></a>Leistungstipps

* Reduzieren Sie den Eingabedatensatz des Plugins auf den erforderlichen Mindestbetrag (Spalten/Zeilen).
    * Verwenden Sie filter für den Quelldatensatz, wenn möglich, mithilfe der Kusto-Abfragesprache.
    * Um eine Berechnung für eine Teilmenge der Quellspalten durchzuführen, projizieren Sie nur die Spalten, bevor Sie das Plugin aufrufen.
* Verwenden `hint.distribution = per_node` Sie immer dann, wenn die Logik in Ihrem Skript verteilbar ist.
    * Sie können den [Partitionsoperator](partitionoperator.md) auch zum Partitionieren des Eingabedatensatzes verwenden.
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

* Um Konflikte zwischen Kusto-Zeichenfolgentrennzeichen und R-Zeichenfolgen zu vermeiden,`'`empfehlen wir die Verwendung einzelner Anführungszeichen (`"`) für Kusto-Zeichenfolgenliterale in Kusto-Abfragen und doppelte Anführungszeichen ( ) für R-Zeichenfolgenliterale in R-Skripten.
* Verwenden Sie den [externen Datenoperator,](externaldata-operator.md) um den Inhalt eines Skripts abzurufen, das Sie an einem externen Speicherort gespeichert haben, z. B. Azure-Blobspeicher, ein öffentliches GitHub-Repository usw.
  
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

