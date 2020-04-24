---
title: 'Python-Plug-in: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt das python-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5ceafde1361c87d368237d0f8c71ad8d0708aec1
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108506"
---
# <a name="python-plugin"></a>Python-Plug-In

::: zone pivot="azuredataexplorer"

Das python-Plug-in führt eine benutzerdefinierte Funktion (User-Defined Function, UDF) mit einem Python-Skript aus. Das Python-Skript ruft Tabellendaten als Eingabe ab, und es wird erwartet, dass eine tabellarische Ausgabe erzeugt wird.
Die Laufzeit des Plug-ins wird in [Sand Fächern](../concepts/sandboxes.md)gehostet, die auf den Knoten des Clusters ausgeführt werden.

## <a name="syntax"></a>Syntax

*T* `|` `single``,` *script* *output_schema* `,` *external_artifacts*[`hint.distribution` (`per_node`)] `python(`output_schema Skript [`,` *script_parameters*] [external_artifacts] |  `evaluate` `=``)`

## <a name="arguments"></a>Argumente

* *output_schema*: ein `type` Literalwert, der das Ausgabe Schema der tabellarischen Daten definiert, die vom Python-Code zurückgegeben werden.
    * Das Format ist: `typeof(` *ColumnName* `:` *ColumnType* [,...] `)`Beispiel: `typeof(col1:string, col2:long)`.
    * Verwenden Sie zum Erweitern des Eingabe Schemas die folgende Syntax:`typeof(*, col1:string, col2:long)`
* *Skript*: ein `string` Literalzeichen, das das gültige auszuführende Python-Skript ist.
* *script_parameters*: ein optionales `dynamic` literalobjekt, bei dem es sich um einen Eigenschaften Behälter mit Name-Wert-Paaren handelt, die `kargs` als reserviertes Wörterbuch an das Python-Skript übermittelt werden sollen (siehe [reservierte python](#reserved-python-variables)
* *Hint. Distribution*: ein optionaler Hinweis für die Ausführung des Plug-ins, das auf mehrere Cluster Knoten verteilt wird.
  * Standardwert: `single`.
  * `single`: Eine einzelne Instanz des Skripts wird über die gesamten Abfrage Daten ausgeführt.
  * `per_node`: Wenn die Abfrage vor der Verteilung des python-Blocks verteilt wird, wird eine Instanz des Skripts auf jedem Knoten über die darin enthaltenen Daten ausgeführt.
* *external_artifacts*: ein optionales `dynamic` Literalformat, bei dem es sich um einen Eigenschaften Behälter mit Name & URL-Paaren für Artefakte handelt, auf die über den cloudspeicher zugegriffen werden kann und die zur Laufzeit für das Skript verfügbar gemacht werden können
  * URLs, auf die in diesem Eigenschaften Behälter verwiesen wird, sind erforderlich für:
  * Sie sind in der Legenden [Richtlinie](../management/calloutpolicy.md)des Clusters enthalten.
    2. Wenn Sie sich an einem öffentlich verfügbaren Speicherort befinden oder die erforderlichen Anmelde Informationen bereitstellen, wie unter [Speicher Verbindungs](../api/connection-strings/storage.md)Zeichenfolgen erläutert.
  * Die Artefakte werden bereitgestellt, damit das Skript aus einem lokalen temporären Verzeichnis,, `.\Temp`verwendet werden kann. die in der Eigenschaften Sammlung bereitgestellten Namen werden als lokale Dateinamen verwendet (siehe [Beispiel](#examples) unten).
  * Weitere Informationen finden Sie im [Anhang](#appendix-installing-packages-for-the-python-plugin) unten.

## <a name="reserved-python-variables"></a>Reservierte python-Variablen

Die folgenden Variablen sind für die Interaktion zwischen der Kusto-Abfragesprache und dem Python-Code reserviert:

* `df`: Die Eingabe Tabellendaten (die Werte von `T` oben) als `pandas` dataframe.
* `kargs`: Der Wert des *script_parameters* Arguments als python-Wörterbuch.
* `result`: Ein `pandas` dataframe, der vom Python-Skript erstellt wurde, dessen Wert zu den Tabellendaten wird, die an den Kusto-Abfrage Operator gesendet werden, der auf das Plug-in folgt

## <a name="onboarding"></a>Onboarding

* Das Plug-in ist standardmäßig deaktiviert.
* Die Voraussetzungen für die Aktivierung des Plug-ins sind [hier](../concepts/sandboxes.md#prerequisites)aufgeführt.
* Aktivieren oder deaktivieren Sie das Plug-in auf der [Azure-Portal auf der Registerkarte **Konfiguration** Ihres Clusters](https://docs.microsoft.com/azure/data-explorer/language-extensions).

## <a name="notes-and-limitations"></a>Hinweise und Einschränkungen

* Das python Sandbox-Image basiert auf der *Anaconda 5.2.0* -Verteilung mit dem *python 3,6* -Modul.
  Die Liste der Pakete finden Sie [hier](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/) (ein kleiner Prozentsatz der Pakete ist möglicherweise nicht mit den von der Sandbox, in der das Plug-in ausgeführt wird, erzwungenen Beschränkungen kompatibel).
* Das python-Image enthält auch gängige `tensorflow`ml `keras` `torch` `hdbscan` `xgboost` -Pakete:,,, und andere nützliche Pakete.
* Das Plug-in importiert *numpy* (as `np`) & *Pandas* (as `pd`) standardmäßig.  Sie können andere Module nach Bedarf importieren.
* **Erfassung [von Abfrage](../management/data-ingestion/ingest-from-query.md) -und [Aktualisierungs Richtlinien](../management/updatepolicy.md)**
  * Es ist möglich, das Plug-in in Abfragen zu verwenden, die folgende Aktionen ausführen:
      1. Definiert als Teil einer Update Richtlinie, deren Quell Tabelle mit der *nicht-streamingerfassung* erfasst wird.
      2. Führen Sie als Teil eines Befehls aus, der von einer Abfrage erfasst wird (z `.set-or-append`. b.).
  * In beiden oben genannten Fällen wird empfohlen, zu überprüfen, ob das Volume und die Häufigkeit der Erfassung sowie die Komplexität und Ressourcennutzung der python-Logik an den [Sandkasten Einschränkungen](../concepts/sandboxes.md#limitations)und den verfügbaren Ressourcen des Clusters angepasst sind.
    Wenn dies nicht der Fall ist, kann dies zu [Drosselungs Fehlern](../concepts/sandboxes.md#errors)führen.
  * Es ist *nicht* möglich, das Plug-in in einer Abfrage zu verwenden, die als Teil einer Update Richtlinie definiert ist, deren Quell Tabelle mithilfe der [streamingansung](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)erfasst wird.

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

* Reduzieren Sie die Eingabedaten des Plug-Ins auf den minimal erforderlichen Wert (Spalten/Zeilen).
    * Verwenden Sie nach Möglichkeit Filter für das Quell DataSet mit der Abfragesprache von Kusto.
    * Um eine Berechnung für eine Teilmenge der Quell Spalten auszuführen, projizieren Sie nur diese Spalte, bevor Sie das Plug-in aufrufen.
* Verwenden `hint.distribution = per_node` Sie immer dann, wenn die Logik in Ihrem Skript Verteil Bar ist.
    * Sie können auch den [Partitions Operator](partitionoperator.md) für die Partitionierung des Eingabe Datasets verwenden.
* Verwenden Sie die Abfragesprache von Kusto, wann immer dies möglich ist, um die Logik Ihres python-Skripts zu implementieren.

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

* Um mehrzeilige Zeichen folgen mit dem Python-Skript `Kusto.Explorer`in zu generieren, kopieren Sie Ihr Python-Skript aus Ihrem bevorzugten python-Editor (*jupyter*, *Visual Studio Code*, *pycharm*usw.), und führen Sie dann eine der folgenden Aktionen aus:
    * Drücken Sie *F2* , um das Fenster **in python bearbeiten** zu öffnen. Fügen Sie das Skript in dieses Fenster ein. Wählen Sie **OK** aus. Das Skript wird mit Anführungszeichen und neuen Zeilen versehen (ist in Kusto gültig) und wird automatisch in die Registerkarte Abfrage eingefügt.
    * Fügen Sie den Python-Code direkt in die Registerkarte Abfrage ein, wählen Sie die Zeilen aus, und drücken Sie *STRG + k*, *STRG + S* -Taste, um Sie wie oben zu ergänzen (um Sie umzukehren, drücken Sie *STRG + k*, *STRG + M* heiße Taste). [Hier](../tools/kusto-explorer-shortcuts.md#query-editor) ist die vollständige Liste der Verknüpfungen für den Abfrage-Editor.
* Zum Vermeiden von Konflikten zwischen Kusto-Zeichen folgen Trennzeichen und python-Zeichenfolgenliteralen empfiehlt es`'`sich, einfache Anführungszeichen () für Kusto-Zeichen folgen Literale in Kusto-Abfragen und doppelte Anführungszeichen (`"`) für python-Zeichen folgen Literale in python-Skripts zu verwenden.
* Verwenden Sie den [externaldata-Operator](externaldata-operator.md) zum Abrufen des Inhalts eines Skripts, das Sie an einem externen Speicherort gespeichert haben, z. b. Azure BLOB Storage.
  
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

## <a name="appendix-installing-packages-for-the-python-plugin"></a>Anhang: Installieren von Paketen für das python-Plug-in

Aus den folgenden Gründen müssen Sie möglicherweise Pakete selbst installieren:

* Das Paket ist privat und selbst.
* Das Paket ist öffentlich, aber nicht im Basis Image des Plug-ins enthalten.

Sie können Pakete installieren, indem Sie die folgenden Schritte ausführen:

1. Einmalige Voraussetzung:
  
  ein. Erstellen Sie einen BLOB-Container, um die Pakete zu hosten, vorzugsweise in derselben Region wie Ihr Cluster.
    * Beispiel: `https://artifcatswestus.blob.core.windows.net/python` (angenommen, Ihr Cluster befindet sich in der Region "USA, Westen")
  
  b. Ändern Sie die Legenden [Richtlinien](../management/calloutpolicy.md) des Clusters, um den Zugriff auf diesen Speicherort zuzulassen.
    * Hierfür sind [alldatabasesadmin](../management/access-control/role-based-authorization.md) -Berechtigungen erforderlich.
    * Um z. b. den Zugriff auf ein BLOB in `https://artifcatswestus.blob.core.windows.net/python`zu aktivieren, lautet der Befehl, der ausgeführt werden soll, wie folgt:

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. Für öffentliche Pakete (in [pypi](https://pypi.org/) oder anderen Kanälen) a. Laden Sie das Paket und seine Abhängigkeiten herunter.
  b. Kompilieren Sie ggf. in Wheel`*.whl`()-Dateien:
    * Führen Sie in einem cmd-Fenster (in Ihrer lokalen python-Umgebung) Folgendes aus:
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. Erstellen Sie eine ZIP-Datei, die das erforderliche Paket und seine Abhängigkeiten enthält:

    * Bei öffentlichen Paketen: zippen Sie die Dateien, die Sie im vorherigen Schritt heruntergeladen haben.
    * Notizen:
        * Stellen Sie sicher, dass `.whl` Sie die Dateien selbst und *nicht* ihren übergeordneten Ordner komprimieren.
        * Sie können Dateien `.whl` für Pakete überspringen, die bereits mit derselben Version im Basis Sandbox Image vorhanden sind.
    * Bei privaten Paketen: ZIP-Ordner des Pakets und der zugehörigen Abhängigkeiten

4. Laden Sie die gezippte Datei in ein BLOB im artefaktspeicherort hoch (aus Schritt 1.).

5. Aufrufen des `python` Plug-ins:
    * Geben Sie `external_artifacts` den Parameter mit dem Eigenschaften Behälter Name und Verweis auf die ZIP-Datei an (die URL des BLOBs).
    * In Ihrem Inline-Python-Code `Zipackage` : `sandbox_utils` importieren Sie aus `install()` , und wenden Sie die-Methode mit dem Namen der ZIP-Datei an.

### <a name="example"></a>Beispiel

Installieren des [Faker](https://pypi.org/project/Faker/) -Pakets, das gefälschte Daten generiert:

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
