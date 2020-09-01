---
title: 'Azure Data Explorer: Debuggen der Kusto-Abfragesprache in Python-Inlinecode mithilfe von Visual Studio Code'
description: Erfahren Sie, wie Sie die Kusto-Abfragesprache (KQL) in Python-Inlinecode mithilfe von Visual Studio Code debuggen.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: 21e3da862931219a338d2364117b8990280118d9
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874289"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debuggen der Kusto-Abfragesprache in Python-Inlinecode mithilfe von Visual Studio Code

Azure Data Explorer unterstützt das Ausführen von in die Kusto-Abfragesprache eingebettetem Python-Code mithilfe des [python()-Plugins](kusto/query/pythonplugin.md). Die Runtime des Plugins wird in einer Sandbox gehostet. Dabei handelt es sich um eine isolierte und sichere Python-Umgebung. Das python()-Plugin erweitert die nativen Funktionalitäten der Kusto-Abfragesprache um das riesige Archiv von OSS-Python-Paketen. Mithilfe dieser Erweiterung können Sie erweiterte Algorithmen im Rahmen der Abfrage ausführen, z. B. Maschinelles Lernen, Künstliche Intelligenz, Statistiken und Zeitreihen.

Die Tools der Kusto-Abfragesprache eignen sich nicht sonderlich gut für die Entwicklung und das Debuggen von Python-Algorithmen. Entwickeln Sie den Algorithmus daher für Ihre bevorzugte in Python integrierte Entwicklungsumgebung, z. B. Jupyter, PyCharm, Visual Studio oder Visual Studio Code. Sobald der Algorithmus fertig gestellt ist, kopieren Sie Ihn in KQL. Zur Verbesserung und Optimierung dieses Workflows unterstützt Azure Data Explorer die Integration zwischen dem Kusto-Explorer und Webbenutzeroberflächen-Clients und Visual Studio Code zum Schreiben und Debuggen von KQL mit Python-Inlinecode. 

> [!NOTE]
> Dieser Workflow kann nur zum Debuggen relativ kleiner Eingabetabellen (nur wenige MB) verwendet werden. Daher müssen Sie die Eingabe für das Debuggen möglicherweise einschränken.  Wenn Sie einer große Tabelle verarbeiten müssen, beschränken Sie sie mit `| take`, `| sample` oder `where rand() < 0.x` auf das Debuggen.

## <a name="prerequisites"></a>Voraussetzungen

1. Installieren Sie die [Anaconda-Distribution](https://www.anaconda.com/distribution/#download-section) von Python. Klicken Sie in **Erweiterte Optionen** auf **Add Anaconda to my PATH environment variable** (Anaconda zu meiner PATH-Umgebungsvariable hinzufügen).
2. [Visual Studio Code](https://code.visualstudio.com/Download) installiert
3. Installieren Sie die [Python-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Ausführen Ihrer Abfrage in der Clientanwendung

1. Fügen Sie in Ihrer Clientanwendung mit `set query_python_debug;` eine Abfrage mit Python-Inlinecode als Präfix hinzu.
1. Führen Sie die Abfrage aus.
    * Kusto-Explorer: VS Code wird beim Start des Skripts *debug_pythin.py* automatisch gestartet.
    * Kusto-Webbenutzeroberfläche: 
        1. Laden Sie die Dateien *debug_python.py*, *df.txt* und *kargs.txt* herunter, und speichern Sie sie. Klicken Sie im angezeigten Fenster auf **Zulassen**. **Speichern** Sie die Dateien im ausgewählten Verzeichnis. 

            ![Herunterladen der Python-Inlinecodedateien über die Webbenutzeroberfläche](media/debug-inline-python/webui-inline-python.png)

        1. Klicken Sie mit der rechten Maustaste auf das Skript *debug_python.py*, und öffnen Sie es mit VS Code. 
        Das Skript *debug_python.py* enthält Python-Inlinecode aus der KQL-Abfrage mit dem Vorlagencode als Präfix, um den Eingabedatenrahmen aus *df.txt* und das Parameterverzeichnis aus *kargs.txt* zu initialisieren.    
            
1. Starten Sie den VS Code-Debugger in Visual Studio Code: Wählen Sie unter **Debuggen** > **Debuggen starten (F5)** die **Python**-Konfiguration aus. Der Debugger wird automatisch gestartet und legt automatisch einen Breakpoint zum Debuggen des Inlinecodes fest.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Wie funktioniert das Debuggen von Python-Inlinecode in VS Code?

1. Die Abfrage wird analysiert und auf dem Server ausgeführt, bis die erforderliche `| evaluate python()`-Klausel erreicht wird.
1. Die Python-Sandbox wird aufgerufen, anstatt den Code auszuführen, werden jedoch die Eingabetabelle, das Parameterverzeichnis und der Code serialisiert und an den Client zurückgesendet.
1. Diese drei Objekte werden in den drei Dateien *df.txt*, *kargs.txt* und *debug_python.py* im ausgewählten Verzeichnis (Webbenutzeroberfläche) oder im Verzeichnis „%TEMP%“ des Clients (Kusto-Explorer) gespeichert.
1. VS Code wird mit der Datei *debug_python.py* vorab geladen gestartet, die einen Präfixcode zum Initialisieren von „df“ und „kargs“ über ihre jeweiligen Dateien enthält. Anschließend wird das in der KQL-Abfrage eingebettete Python-Skript gestartet.

## <a name="query-example"></a>Abfragebeispiel

1. Führen Sie die folgende KQL-Abfrage in Ihrer Clientanwendung aus:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Daraus resultiert die folgende Tabelle:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Führen Sie dieselbe KQL-Abfrage mit `set query_python_debug;` in Ihrer Clientanwendung aus:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. Daraufhin wird Visual Studio Code gestartet:

    ![VS Code starten](media/debug-inline-python/launch-vs-code.png)

1. Visual Studio Code führt das Debugging durch und gibt den resultierenden Datenrahmen in der Debugging-Konsole aus:

    ![Debuggen in VS Code](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Zwischen dem Python-Sandboximage und Ihrer lokalen Installation können Unterschiede bestehen. [Überprüfen Sie das Sandboximage auf bestimmte Pakete, indem Sie das Plugin abfragen](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
