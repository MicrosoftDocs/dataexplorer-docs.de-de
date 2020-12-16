---
title: 'Python-Plug-in: Azure Daten-Explorer'
description: Dieser Artikel beschreibt das python-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 918d0f2f7fa8667a4cf90b2813bb3b80dd49fa78
ms.sourcegitcommit: 335e05864e18616c10881db4ef232b9cda285d6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97596854"
---
# <a name="python-plugin"></a>Python-Plugin

::: zone pivot="azuredataexplorer"

Das python-Plug-in führt eine benutzerdefinierte Funktion (User-Defined Function, UDF) mit einem Python-Skript aus. Das Python-Skript ruft Tabellendaten als Eingabe ab, und es wird erwartet, dass eine tabellarische Ausgabe erzeugt wird.
Die Laufzeit des Plug-ins wird in [Sand Fächern](../concepts/sandboxes.md)gehostet, die auf den Knoten des Clusters ausgeführt werden.

## <a name="syntax"></a>Syntax

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `python(` *output_schema* `,` *Skript* [ `,` *script_parameters*] [ `,` *external_artifacts*]`)`

## <a name="arguments"></a>Argumente

* *output_schema*: ein `type` Literalwert, der das Ausgabe Schema der tabellarischen Daten definiert, die vom Python-Code zurückgegeben werden.
    * Das Format ist: `typeof(` *ColumnName* `:` *ColumnType*[,...] `)` . Beispiel: `typeof(col1:string, col2:long)` .
    * Verwenden Sie die folgende Syntax, um das Eingabe Schema zu erweitern: `typeof(*, col1:string, col2:long)`
* *Skript*: ein `string` Literalzeichen, das das gültige auszuführende Python-Skript ist.
* *script_parameters*: ein optionales `dynamic` Literalzeichen. Dabei handelt es sich um einen Eigenschaften Behälter mit Name-Wert-Paaren, die als reserviertes Wörterbuch an das Python-Skript übermittelt werden `kargs` . Weitere Informationen finden Sie unter [reservierte python-Variablen](#reserved-python-variables).
* *Hint. Distribution*: ein optionaler Hinweis für die Ausführung des Plug-ins, das auf mehrere Cluster Knoten verteilt wird.
  * Standardwert: `single`.
  * `single`: Eine einzelne Instanz des Skripts wird über die gesamten Abfrage Daten ausgeführt.
  * `per_node`: Wenn die Abfrage vor der Verteilung des python-Blocks verteilt wird, wird eine Instanz des Skripts auf jedem Knoten auf den darin enthaltenen Daten ausgeführt.
* *external_artifacts*: ein optionales `dynamic` literalobjekt, bei dem es sich um einen Eigenschaften Behälter mit Name-und URL-Paaren handelt Sie können zur Laufzeit für das Skript zur Verfügung gestellt werden.
  * URLs, auf die in diesem Eigenschaften Behälter verwiesen wird, müssen Folgendes sein:
    * In der Legenden [Richtlinie](../management/calloutpolicy.md)des Clusters enthalten.
    * An einem öffentlich verfügbaren Speicherort oder die erforderlichen Anmelde Informationen bereitstellen, wie unter [Speicher Verbindungs](../api/connection-strings/storage.md)Zeichenfolgen erläutert.
  * Die Artefakte werden bereitgestellt, damit das Skript aus einem lokalen temporären Verzeichnis,, verwendet werden kann `.\Temp` . Die in der Eigenschaften Sammlung bereitgestellten Namen werden als lokale Dateinamen verwendet. Siehe [Beispiele](#examples).
  * Weitere Informationen finden Sie unter [Installieren von Paketen für das python-Plug](#install-packages-for-the-python-plugin)-in. 

## <a name="reserved-python-variables"></a>Reservierte python-Variablen

Die folgenden Variablen sind für die Interaktion zwischen der Kusto-Abfragesprache und dem Python-Code reserviert.

* `df`: Die Eingabe Tabellendaten (die Werte von `T` oben) als `pandas` dataframe.
* `kargs`: Der Wert des *script_parameters* Arguments als python-Wörterbuch.
* `result`: Ein `pandas` dataframe, der durch das Python-Skript erstellt wurde, dessen Wert zu den Tabellendaten wird, die an den Kusto-Abfrage Operator gesendet werden, der auf das Plug-in folgt.

## <a name="enable-the-plugin"></a>Aktivieren des Plug-Ins

* Das Plug-in ist standardmäßig deaktiviert.
* Informationen zum Aktivieren des Plug-Ins finden Sie in der Liste der [Voraussetzungen](../concepts/sandboxes.md#prerequisites).
* Aktivieren oder deaktivieren Sie das Plug-in in der Azure-Portal auf der [Registerkarte Konfiguration](../../language-extensions.md)Ihres Clusters.

## <a name="python-sandbox-image"></a>Python Sandbox-Image

* Das python Sandbox-Image basiert auf der *Anaconda 5.2.0* -Verteilung mit der *python 3,6* -Engine.
  Sehen Sie sich die Liste der [Anaconda-Pakete](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)an.
  
  > [!NOTE]
  > Ein kleiner Prozentsatz von Paketen ist möglicherweise nicht mit den von der Sandbox, in der das Plug-in ausgeführt wird, erzwungenen Beschränkungen kompatibel.
  
* Das python-Image enthält auch gängige ml-Pakete: `tensorflow` , `keras` , `torch` , `hdbscan` , `xgboost` und andere nützliche Pakete.
* Das Plug-in importiert *numpy* (as `np` ) & *Pandas* (as `pd` ) standardmäßig.  Sie können andere Module nach Bedarf importieren.

## <a name="use-ingestion-from-query-and-update-policy"></a>Verwenden der Erfassung von Abfrage-und Aktualisierungs Richtlinien

* Verwenden Sie das Plug-in in Abfragen mit folgenden Aktionen:
  * Definiert als Teil einer [Update Richtlinie](../management/updatepolicy.md), deren Quell Tabelle mit der *nicht-streamingerfassung* erfasst wird.
  * Führen Sie als Teil eines Befehls aus, der [von einer Abfrage](../management/data-ingestion/ingest-from-query.md)erfasst wird, z `.set-or-append` . b..
    Vergewissern Sie sich in beiden Fällen, dass das Volume und die Häufigkeit der Erfassung sowie die Komplexität und Ressourcen, die von der python-Logik verwendet werden, den [Sandbox-Einschränkungen](../concepts/sandboxes.md#limitations) und den verfügbaren Ressourcen des Clusters entsprechen. Wenn dies nicht der Fall ist, kann dies zu [Drosselungs Fehlern](../concepts/sandboxes.md#errors)führen.
* Sie können das Plug-in nicht in einer Abfrage verwenden, die als Teil einer Update Richtlinie definiert ist, deren Quell Tabelle mithilfe der [streamingansung](../../ingest-data-streaming.md)erfasst wird.

## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 360 step 1
| evaluate python(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result = df\n'                     //  The Python decorated script
'n = df.shape[0]\n'
'g = kargs["gain"]\n'
'f = kargs["cycles"]\n'
'result["fx"] = g * np.sin(df["x"]/n*2*np.pi*f)\n'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="Sinus-Demo" border="false":::

```kusto
print "This is an example for using 'external_artifacts'"
| evaluate python(
    typeof(File:string, Size:string),
    "import os\n"
    "result = pd.DataFrame(columns=['File','Size'])\n"
    "sizes = []\n"
    "path = '.\\\\Temp'\n"
    "files = os.listdir(path)\n"
    "result['File']=files\n"
    "for file in files:\n"
    "    sizes.append(os.path.getsize(path + '\\\\' + file))\n"
    "result['Size'] = sizes\n"
    "\n",
    external_artifacts = 
        dynamic({"this_is_my_first_file":"https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r",
                 "this_is_a_script":"https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py"})
)
```

| Datei                  | Size |
|-----------------------|------|
| this_is_a_script      | 120  |
| this_is_my_first_file | 105  |

## <a name="performance-tips"></a>Leistungstipps

* Reduzieren Sie die Eingabedaten des Plug-Ins auf den minimal erforderlichen Wert (Spalten/Zeilen).
    * Verwenden Sie nach Möglichkeit Filter für das Quell DataSet mit der Abfragesprache von Kusto.
    * Wenn Sie eine Berechnung für eine Teilmenge der Quell Spalten durchführen möchten, projizieren Sie nur die Spalten, bevor Sie das Plug-in aufrufen.
* Verwenden `hint.distribution = per_node` Sie immer dann, wenn die Logik in Ihrem Skript Verteil Bar ist.
    * Sie können auch den [Partitions Operator](partitionoperator.md) für die Partitionierung des Eingabe Datasets verwenden.
* Verwenden Sie die Abfragesprache von Kusto, wenn dies möglich ist, um die Logik Ihres python-Skripts zu implementieren.

    ### <a name="example"></a>Beispiel

    ```kusto    
    .show operations
    | where StartedOn > ago(7d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node python( // Using per_node distribution, as the script's logic allows it
        typeof(*, _2d:double),
        'result = df\n'
        'result["_2d"] = 2 * df["d_seconds"]\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(_2d)
    ```

## <a name="usage-tips"></a>Verwendungstipps

* Um mehrzeilige Zeichen folgen zu generieren, die das Python-Skript in enthalten `Kusto.Explorer` , kopieren Sie Ihr Python-Skript aus Ihrem bevorzugten python-Editor (*jupyter*, *Visual Studio Code*, *pycharm* usw.). 
  Führen Sie einen der folgenden Schritte aus:
    * Drücken Sie **F2** , um das Fenster *in python bearbeiten* zu öffnen. Fügen Sie das Skript in dieses Fenster ein. Klicken Sie auf **OK**. Das Skript wird mit Anführungszeichen und neuen Zeilen versehen und ist daher in Kusto gültig und wird automatisch in die Registerkarte Abfrage eingefügt.
    * Fügen Sie den Python-Code direkt in die Registerkarte Abfrage ein. Wählen Sie diese Zeilen aus, und drücken Sie **STRG + K**, **STRG + S** , um Sie wie oben zu ergänzen. Um umzukehren, drücken Sie **STRG + K**, **STRG + M** -Taste. Sehen Sie sich die vollständige Liste der [Abfrage-Editor](../tools/kusto-explorer-shortcuts.md#query-editor)-Verknüpfungen
* Verwenden Sie zum Vermeiden von Konflikten zwischen Kusto-Zeichen folgen Trennzeichen und python-Zeichenfolgenliteralen Folgendes:
     * Einfache Anführungszeichen ( `'` ) für Kusto-Zeichen folgen Literale in Kusto-Abfragen
     * Doppelte Anführungszeichen ( `"` ) für python-Zeichen folgen Literale in python-Skripts
* Verwenden Sie den- [ `externaldata` Operator](externaldata-operator.md) zum Abrufen des Inhalts eines Skripts, das Sie an einem externen Speicherort gespeichert haben, z. b. Azure BLOB Storage.
  
    ### <a name="example"></a>Beispiel

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate python(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

## <a name="install-packages-for-the-python-plugin"></a>Installieren von Paketen für das python-Plug-in

Aus den folgenden Gründen müssen Sie möglicherweise selbst Pakete installieren:

* Das Paket ist privat und selbst.
* Das Paket ist öffentlich, aber nicht im Basis Image des Plug-ins enthalten.

Installieren Sie die Pakete wie folgt:

### <a name="prerequisites"></a>Voraussetzungen

  1. Erstellen Sie einen BLOB-Container, um die Pakete zu hosten, vorzugsweise am gleichen Ort wie Ihr Cluster. `https://artifcatswestus.blob.core.windows.net/python`Angenommen, Ihr Cluster befindet sich in der Region "USA, Westen".
  1. Ändern Sie die Legenden [Richtlinien](../management/calloutpolicy.md) des Clusters, um den Zugriff auf diesen Speicherort zuzulassen.
        * Diese Änderung erfordert [alldatabasesadmin](../management/access-control/role-based-authorization.md) -Berechtigungen.

        * Um z. b. den Zugriff auf ein BLOB in zu aktivieren `https://artifcatswestus.blob.core.windows.net/python` , führen Sie den folgenden Befehl aus:

        ```kusto
        .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
        ```

### <a name="install-packages"></a>Installieren von Paketen

1. Laden Sie bei öffentlichen Paketen in [pypi](https://pypi.org/) oder anderen Kanälen das Paket und seine Abhängigkeiten herunter.

   * Kompilieren Sie die Wheel- `*.whl` Dateien (), falls erforderlich.
   * Führen Sie in einem cmd-Fenster in Ihrer lokalen python-Umgebung Folgendes aus:
    
    ```python
    pip wheel [-w download-dir] package-name.
    ```

1. Erstellen Sie eine ZIP-Datei, die das erforderliche Paket und seine Abhängigkeiten enthält.

    * Bei privaten Paketen: zippen Sie den Ordner des Pakets und die Ordner seiner Abhängigkeiten.
    * Bei öffentlichen Paketen ZIP-Dateien, die im vorherigen Schritt heruntergeladen wurden.
    
    > [!NOTE]
    > * Stellen Sie sicher, dass Sie die `.whl` Dateien selbst und nicht ihren übergeordneten Ordner komprimieren.
    > * Sie können `.whl` Dateien für Pakete überspringen, die bereits mit derselben Version im Basis Sandbox Image vorhanden sind.

1. Laden Sie die gezippte Datei in ein BLOB im artefaktspeicherort hoch (aus Schritt 1).

1. Ruft das Plug-in auf `python` .
    * Geben `external_artifacts` Sie den Parameter mit dem Eigenschaften Behälter Name und Verweis auf die ZIP-Datei an (die URL des BLOBs).
    * Importieren Sie in Ihrem python-Inline Code `Zipackage` aus, `sandbox_utils` und nennen Sie seine- `install()` Methode mit dem Namen der ZIP-Datei.

### <a name="example"></a>Beispiel

Installieren Sie das [Faker](https://pypi.org/project/Faker/) -Paket, mit dem gefälschte Daten generiert werden.

```kusto
range ID from 1 to 3 step 1 
| extend Name=''
| evaluate python(typeof(*),
    'from sandbox_utils import Zipackage\n'
    'Zipackage.install("Faker.zip")\n'
    'from faker import Faker\n'
    'fake = Faker()\n'
    'result = df\n'
    'for i in range(df.shape[0]):\n'
    '    result.loc[i, "Name"] = fake.name()\n',
    external_artifacts=pack('faker.zip', 'https://artifacts.blob.core.windows.net/kusto/Faker.zip?...'))
```

| id | Name         |
|----|--------------|
|   1| Gary Tapia   |
|   2| Emma Evans   |
|   3| Ashley Bowen |

---

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
