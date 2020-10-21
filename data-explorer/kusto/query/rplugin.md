---
title: 'R-Plug-in (Vorschau): Azure Daten-Explorer'
description: In diesem Artikel wird das R-Plug-in (Vorschau) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ae8a82253dfd17b7d3667cd4c72648df0c42658a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242779"
---
# <a name="r-plugin-preview"></a>R-Plug-in (Vorschau)

::: zone pivot="azuredataexplorer"

Das r-Plug-in führt eine benutzerdefinierte Funktion (UDF) mithilfe eines r-Skripts aus. Das Skript ruft die Tabellendaten als Eingabe ab und erzeugt eine tabellarische Ausgabe.
Die Laufzeit des Plug-ins wird in einem [Sandkasten](../concepts/sandboxes.md) auf den Knoten des Clusters gehostet. Der Sandkasten bietet eine isolierte und sichere Umgebung.

## <a name="syntax"></a>Syntax

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `r(` *output_schema* `,` *Skript* [ `,` *script_parameters*]`)`

## <a name="arguments"></a>Argumente

* *output_schema*: ein `type` Literalwert, der das Ausgabe Schema der tabellarischen Daten definiert, die vom R-Code zurückgegeben werden.
    * Das Format ist: `typeof(` *ColumnName* `:` *ColumnType*[,...] `)` , z. b `typeof(col1:string, col2:long)` .:.
    * Um das Eingabe Schema zu erweitern, verwenden Sie die folgende Syntax: `typeof(*, col1:string, col2:long)` .
* *Skript*: ein `string` Literalwert, der das gültige R-Skript ist, das ausgeführt werden soll.
* *script_parameters*: ein optionales `dynamic` literalobjekt, bei dem es sich um einen Eigenschaften Behälter mit Name-Wert-Paaren handelt, die an das R-Skript als reserviertes `kargs` Wörterbuch Weitere Informationen finden Sie unter [reservierte R-Variablen](#reserved-r-variables).
* *Hint. Distribution*: ein optionaler Hinweis für die Ausführung des Plug-ins, das auf mehrere Cluster Knoten verteilt wird.
   Standardwert: `single`.
    * `single`: Eine einzelne Instanz des Skripts wird über die gesamten Abfrage Daten ausgeführt.
    * `per_node`: Wenn die Abfrage vor der Verteilung des R-Blocks verteilt wird, wird eine Instanz des Skripts auf jedem Knoten über die darin enthaltenen Daten ausgeführt.

## <a name="reserved-r-variables"></a>Reservierte R-Variablen

Die folgenden Variablen sind für die Interaktion zwischen der Kusto-Abfragesprache und dem R-Code reserviert:

* `df`: Die Eingabe Tabellendaten (die Werte von `T` oben) als R-dataframe.
* `kargs`: Der Wert des *script_parameters* Arguments als R-Wörterbuch.
* `result`: Ein r-dataframe, der vom r-Skript erstellt wurde. Der Wert wird zu den Tabellendaten, die an einen beliebigen Kusto-Abfrage Operator gesendet werden, der auf das Plug-in folgt.

## <a name="enable-the-plugin"></a>Aktivieren des Plug-Ins

* Das Plug-in ist standardmäßig deaktiviert.
* Aktivieren oder deaktivieren Sie das Plug-in auf der Azure-Portal auf der Registerkarte **Konfiguration** Ihres Clusters. Weitere Informationen finden [Sie unter Verwalten von Spracherweiterungen in Ihrem Azure Daten-Explorer-Cluster (Vorschau)](../../language-extensions.md) .

## <a name="notes-and-limitations"></a>Hinweise und Einschränkungen

* Das r Sandbox-Image basiert auf *r 3.4.4 für Windows*und enthält Pakete aus [dem r Essentials-Bundle von Anaconda](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* Die R-Sandbox schränkt den Zugriff auf das Netzwerk ein. Mit dem R-Code können keine weiteren Pakete dynamisch installiert werden, die nicht im Abbild enthalten sind. Wenn Sie bestimmte Pakete benötigen, öffnen Sie eine **neue Supportanfrage** im Azure-Portal.

## <a name="examples"></a>Beispiele

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

## <a name="performance-tips"></a>Leistungstipps

* Reduzieren Sie die Eingabedaten des Plug-Ins auf den minimal erforderlichen Wert (Spalten/Zeilen).
    * Verwenden Sie nach Möglichkeit Filter für das Quell DataSet mithilfe der Kusto-Abfragesprache.
    * Wenn Sie eine Berechnung für eine Teilmenge der Quell Spalten durchführen möchten, projizieren Sie nur die Spalten, bevor Sie das Plug-in aufrufen.
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

## <a name="usage-tips"></a>Verwendungstipps

* So vermeiden Sie Konflikte zwischen Kusto-Zeichen folgen Trennzeichen und R-Zeichen folgen Trennzeichen:  
    * Verwenden Sie einfache Anführungszeichen ( `'` ) für Kusto-Zeichen folgen Literale in Kusto-Abfragen.
    * Verwenden Sie doppelte Anführungszeichen ( `"` ) für r-Zeichen folgen Literale in r-Skripts.
* Verwenden Sie den [externen Daten Operator](externaldata-operator.md) zum Abrufen des Inhalts eines Skripts, das Sie an einem externen Speicherort gespeichert haben, z. b. in Azure BLOB Storage oder in einem öffentlichen GitHub-Repository.
  
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

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end

