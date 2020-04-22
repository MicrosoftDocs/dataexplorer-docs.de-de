---
title: Python-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Python-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5192474779fb712595ff1c25785892bb543fda84
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765713"
---
# <a name="python-plugin"></a>Python-Plug-In

::: zone pivot="azuredataexplorer"

Das Python-Plugin führt eine benutzerdefinierte Funktion (UDF) mit einem Python-Skript aus. Das Python-Skript ruft Tabellarische Daten als Eingabe ab und wird voraussichtlich eine tabellarische Ausgabe erzeugen.
Die Laufzeit des Plugins wird in [Sandboxes](../concepts/sandboxes.md)gehostet, die auf den Knoten des Clusters ausgeführt werden.

## <a name="syntax"></a>Syntax

*T* `|` `per_node` `python(` *output_schema* `,` *script* `,` *script_parameters*`,` *external_artifacts*[ `=` `single`( | )] output_schema Skript [ script_parameters ][ external_artifacts ] `evaluate` `hint.distribution``)`

## <a name="arguments"></a>Argumente

* *output_schema*: `type` Ein Literal, das das Ausgabeschema der Tabellendaten definiert, das vom Python-Code zurückgegeben wird.
    * Das Format `typeof(`lautet: *ColumnName* `:` *ColumnType* [, ...] `)`, z. `typeof(col1:string, col2:long)`B.: .
    * Verwenden Sie zum Erweitern des Eingabeschemas die folgende Syntax:`typeof(*, col1:string, col2:long)`
* *skript*: `string` Ein Literal, das das gültige Python-Skript ist, das ausgeführt werden soll.
* *script_parameters*: `dynamic` Ein optionales Literal, das eine Eigenschaftstasche mit Namen/Wert-Paaren ist, die als reserviertes `kargs` Wörterbuch an das Python-Skript übergeben werden soll (siehe [Reservierte Python-Variablen](#reserved-python-variables)).
* *hint.distribution*: Ein optionaler Hinweis für die Ausführung des Plugins, der auf mehrere Clusterknoten verteilt werden soll.
  * Der Standardwert ist `single`.
  * `single`: Eine einzelne Instanz des Skripts wird über die gesamten Abfragedaten ausgeführt.
  * `per_node`: Wenn die Abfrage vor dem Python-Block verteilt wird, wird auf jedem Knoten eine Instanz des Skripts über die darin enthaltenen Daten ausgeführt.
* *external_artifacts*: `dynamic` Ein optionales Literal, das eine Eigenschaftstasche mit Namen & URL-Paare für Artefakte ist, auf die aus dem Cloudspeicher zugegriffen werden kann und die für das Skript zur Laufzeit verfügbar gemacht werden können.
  * URLs, auf die in diesem Eigenschaftenbeutel verwiesen wird, sind erforderlich, um:
  * Werden Sie in die [Legendenrichtlinie](../management/calloutpolicy.md)des Clusters einbezogen.
    2. An einem öffentlich verfügbaren Speicherort sein oder die erforderlichen Anmeldeinformationen bereitstellen, wie unter [Speicherverbindungszeichenfolgen](../api/connection-strings/storage.md)erläutert.
  * Die Artefakte werden für das Skript zur Verfügung gestellt, um aus einem lokalen temporären Verzeichnis zu verwenden, `.\Temp`und die im Eigenschaftenbeutel angegebenen Namen werden als lokale Dateinamen verwendet (siehe [Beispiel](#examples) unten).
  * Weitere Informationen finden Sie im [Anhang](#appendix-installing-packages-for-the-python-plugin) unten.

## <a name="reserved-python-variables"></a>Reservierte Python-Variablen

Die folgenden Variablen sind für die Interaktion zwischen der Kusto-Abfragesprache und dem Python-Code reserviert:

* `df`: Die Eingabe-Tabellarischen `T` Daten (die `pandas` oben genannten Werte) als DataFrame.
* `kargs`: Der Wert des *script_parameters-Arguments* als Python-Wörterbuch.
* `result`: `pandas` Ein DataFrame, der vom Python-Skript erstellt wurde, dessen Wert zu den Tabellendaten wird, die an den Kusto-Abfrageoperator gesendet werden, der dem Plugin folgt.

## <a name="onboarding"></a>Onboarding

* Das Plugin ist standardmäßig deaktiviert.
* Voraussetzungen für die Aktivierung des Plugins sind [hier](../concepts/sandboxes.md#prerequisites)aufgeführt.
* Aktivieren oder deaktivieren Sie das Plugin im [Azure-Portal auf der Registerkarte **Konfiguration** Ihres Clusters](https://docs.microsoft.com/azure/data-explorer/language-extensions).

## <a name="notes-and-limitations"></a>Hinweise und Einschränkungen

* Das Python-Sandbox-Image basiert auf der *Anaconda 5.2.0-Distribution* mit *Python 3.6-Engine.*
  Die Liste der Pakete finden Sie [hier](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/) (ein kleiner Prozentsatz der Pakete kann mit den Einschränkungen, die durch die Sandbox, in der das Plugin ausgeführt wird, erzwungen werden, nicht kompatibel sein).
* Das Python-Image enthält auch `tensorflow` `keras`allgemeine `torch` `hdbscan`ML-Pakete: , , , und `xgboost` andere nützliche Pakete.
* Das Plugin importiert standardmäßig `np` *numpy* (as `pd`) & *Pandas* (as ) .  Sie können andere Module nach Bedarf importieren.
* **[Erfassung aus Abfrage-](../management/data-ingestion/ingest-from-query.md) und [Aktualisierungsrichtlinien](../management/updatepolicy.md)**
  * Es ist möglich, das Plugin in Abfragen zu verwenden, die:
      1. Definiert als Teil einer Aktualisierungsrichtlinie, deren Quelltabelle für die Verwendung *nicht-streaming-Erfassung* aufgenommen wird.
      2. Wird als Teil eines Befehls ausgeführt, der von `.set-or-append`einer Abfrage erfasst wird (z. B. ).
  * In beiden oben genannten Fällen wird empfohlen, zu überprüfen, ob das Volumen und die Häufigkeit der Aufnahme sowie die Komplexität und Ressourcenauslastung der Python-Logik an den [Sandbox-Einschränkungen](../concepts/sandboxes.md#limitations)und den verfügbaren Ressourcen des Clusters ausgerichtet sind.
    Andernfalls kann es zu [Drosselungsfehlern kommen.](../concepts/sandboxes.md#errors)
  * Es ist *nicht* möglich, das Plugin in einer Abfrage zu verwenden, die als Teil einer Aktualisierungsrichtlinie definiert ist, deren Quelltabelle mithilfe der [Streaming-Aufnahme](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)aufgenommen wird.

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
:::image type="content" source="images/samples/sine-demo.png" alt-text="Sinus-Demo":::

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

* Reduzieren Sie den Eingabedatensatz des Plugins auf den erforderlichen Mindestbetrag (Spalten/Zeilen).
    * Verwenden Sie filtert nach Möglichkeit mit der Abfragesprache von Kusto.
    * Um eine Berechnung für eine Teilmenge der Quellspalten durchzuführen, projizieren Sie nur die Spalten, bevor Sie das Plugin aufrufen.
* Verwenden `hint.distribution = per_node` Sie immer dann, wenn die Logik in Ihrem Skript verteilbar ist.
    * Sie können den [Partitionsoperator](partitionoperator.md) auch zum Partitionieren des Eingabedatensatzes verwenden.
* Verwenden Sie nach Möglichkeit die Abfragesprache von Kusto, um die Logik Ihres Python-Skripts zu implementieren.

    Beispiel:

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

* Um mehrzeilige Zeichenfolgen zu `Kusto.Explorer`generieren, die das Python-Skript in enthalten, kopieren Sie Ihr Python-Skript aus Ihrem bevorzugten Python-Editor (*Jupyter*, *Visual Studio Code*, *PyCharm*usw.), dann entweder:
    * Drücken Sie *F2,* um das Fenster **Bearbeiten in Python** zu öffnen. Fügen Sie das Skript in dieses Fenster ein. Klicken Sie auf **OK**. Das Skript wird mit Anführungszeichen und neuen Zeilen (so dass es in Kusto gültig ist) dekoriert und automatisch in die Abfrageregisterkarte eingefügt.
    * Fügen Sie den Python-Code direkt in die Abfrage-Registerkarte ein, wählen Sie diese Zeilen aus und drücken Sie *Strg+K*, *Strg+S-Hotkey,* um sie wie oben zu dekorieren (um sie umzukehren, drücken Sie *Strg+K*, *Strg+M-Hotkey).* [Hier](../tools/kusto-explorer-shortcuts.md#query-editor) ist die vollständige Liste der Verknüpfungen mit dem Abfrage-Editor.
* Um Konflikte zwischen Kusto-Zeichenfolgentrennzeichen und Python-Zeichenfolgenliteralen zu`'`vermeiden, empfehlen wir die Verwendung einzelner Anführungszeichen`"`( ) für Kusto-Zeichenfolgenliterale in Kusto-Abfragen und doppelte Anführungszeichen ( ) für Python-Zeichenfolgenliterale in Python-Skripts.
* Verwenden Sie den [externen Datenoperator,](externaldata-operator.md) um den Inhalt eines Skripts abzudateien, das Sie an einem externen Speicherort, z. B. Azure Blob Storage, gespeichert haben.
  
    **Beispiel**

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

## <a name="appendix-installing-packages-for-the-python-plugin"></a>Anhang: Installieren von Paketen für das Python-Plugin

Möglicherweise müssen Sie Paket(e) aus einem der folgenden Gründe selbst installieren:

* Das Paket ist privat und ist Ihr eigenes.
* Das Paket ist öffentlich, aber nicht im Basisbild des Plugins enthalten.

Sie können Pakete installieren, indem Sie die folgenden Schritte ausführen:

1. Einmalige Voraussetzung:
  
  a. Erstellen Sie einen Blobcontainer, um das Paket zu hosten, vorzugsweise in derselben Region wie Ihr Cluster.
    * Beispiel: https://artifcatswestus.blob.core.windows.net/python (vorausgesetzt, Ihr Cluster befindet sich in West US)
  
  b. Ändern Sie die [Callout-Richtlinie](../management/calloutpolicy.md) des Clusters, um den Zugriff auf diesen Speicherort zu ermöglichen.
    * Dies erfordert [AllDatabasesAdmin-Berechtigungen.](../management/access-control/role-based-authorization.md)
    * Um z. B. den Zugriff https://artifcatswestus.blob.core.windows.net/pythonauf ein Blob in zu aktivieren, lautet der auszuführende Befehl:

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. Für öffentliche Pakete (in [PyPi](https://pypi.org/) oder anderen Kanälen) a. Laden Sie das Paket und seine Abhängigkeiten herunter.
  b. Kompilieren Sie bei`*.whl`Bedarf in Wheel- ( )-Dateien:
    * Führen Sie in einem cmd-Fenster (in Ihrer lokalen Python-Umgebung) folgende Ausführungszeit aus:
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. Erstellen Sie eine ZIP-Datei, die das erforderliche Paket und seine Abhängigkeiten enthält:

    * Für öffentliche Pakete: Zip die Dateien, die im vorherigen Schritt heruntergeladen wurden.
    * Notizen:
        * Achten Sie darauf, die `.whl` Dateien selbst und *nicht* ihren übergeordneten Ordner zu verpacken.
        * Sie können `.whl` Dateien für Pakete überspringen, die bereits mit derselben Version im Basissandkastenabbild vorhanden sind.
    * Für private Pakete: Zip den Ordner des Pakets und die seiner Abhängigkeiten

4. Laden Sie die gezippte Datei in ein Blob am Speicherort der Artefakte hoch (ab Schritt 1).

5. Aufrufen `python` des Plugins:
    * Geben `external_artifacts` Sie den Parameter mit einem Eigenschaftenbeutel mit Namen und Verweis auf die ZIP-Datei (url des Blobs) an.
    * In Ihrem Inline-Python-Code: Importieren `Zipackage` Sie aus `sandbox_utils` und rufen Sie seine `install()` Methode mit dem Namen der ZIP-Datei auf.

### <a name="example"></a>Beispiel

Installieren des [Faker-Pakets,](https://pypi.org/project/Faker/) das gefälschte Daten generiert:

```kusto
range Id from 1 to 3 step 1 
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

| Id | Name         |
|----|--------------|
|   1| Gary Tapia   |
|   2| Emma Evans   |
|   3| Ashley Bowen |

---

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
