---
title: Verwenden von Azure Notebooks zum Analysieren von Daten in Azure Data Explorer
description: In diesem Thema erfahren Sie, wie Sie eine Abfrage mit einem Azure Notebook erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: d9564eec41fd78b3506994da2917f1d8765ee266
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373991"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Verwenden von Azure Notebooks zum Analysieren von Daten in Azure Data Explorer

[Azure Notebooks](https://notebooks.azure.com/) ist ein Azure-Clouddienst, der das Erstellen und Freigeben von [Jupyter Notebooks](https://jupyter.org/) vereinfacht. Mit Azure Notebooks ist das Kombinieren von Dokumentation, Code und den Ergebnissen der Codeausführung ganz einfach.

> [!Note]
> * Im folgenden Verfahren wird der Python-Client in der Azure Notebooks-Umgebung zum Abfragen von Azure Data Explorer verwendet. Sie können Azure Data Explorer jedoch auch mit [KQLmagic](kqlmagic.md) abfragen.
> * Einige Benutzer haben Probleme beim Authentifizieren mithilfe von Edge gemeldet. Bis diese Probleme behoben wurden, verwenden Sie einen anderen Browser.

## <a name="create-a-project"></a>Erstellen eines Projekts

1. Öffnen Sie [Azure Notebooks](https://notebooks.azure.com/) in Ihrem Browser, und melden Sie sich an.

1. Wählen Sie in der Kopfzeile die Registerkarte **Meine Projekte** aus. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Wählen Sie **+ Neue Projekte** aus.
    
1. Gehen Sie im Dialogfeld **Neues Projekt erstellen** folgendermaßen vor:
    1. Geben Sie den **Projektnamen** ein.
    1. Deaktivieren Sie das Kontrollkästchen **Öffentlich**.
        >[!Important]
        > Wenn Sie das Kontrollkästchen „Öffentlich“ nicht deaktivieren, wird das Projekt offen im Internet verfügbar gemacht.
    1. Klicken Sie auf **Erstellen**.
    
    ![Erstellen eines neuen Projekts](media/azurenotebooks/an-create-new-project-blank.png)

    Die Projekt-ID wird automatisch erstellt.

## <a name="create-a-notebook"></a>Erstellen eines Notebooks

1. Erstellen Sie in Ihrem neuen Projekt ein Notebook. Für das Notebook sollte eine [unterstützte Sprache](https://github.com/Azure/azure-kusto-python#minimum-requirements) verwendet werden.
Wählen Sie **+ Neu** und dann **Notebook** aus, um ein Notebook zu erstellen.

    ![Erstellen eines neuen Notebooks](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Geben Sie im Dialogfeld **Neues Notizbuch erstellen** einen Namen für das Notebook ein.

1. Wählen Sie **Python 3.6** und dann **Neu** aus.
    
    ![Erstellen eines neuen Notebooks](media/azurenotebooks/an-create-new-notebook.png) 
    
1. Wählen Sie in Ihrem Projekt Ihr neues Notebook aus.

    ![Auswählen des neuen Notebooks](media/azurenotebooks/an-select-notebook.png)

    Warten Sie, bis der Python-Kernel initialisiert wurde. Wenn sich das Symbol des gefüllten Kreises in einen leeren Kreis ändert, können Sie den Vorgang fortsetzen.

    ![Initialisierung des Kernels](media/azurenotebooks/an-python-init-icon.png)

1. Geben Sie die folgenden Befehle ein, und wählen Sie **Ausführen** aus, um das Systemmodul zu importieren:
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Zum Ausführen einer Zelle können Sie auch UMSCHALT+EINGABETASTE drücken.

1.  Geben Sie die folgenden Befehle ein, und wählen Sie **Ausführen** aus, um SDK-Klassen zu importieren:
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Um die Verbindungszeichenfolge zu erstellen und den Kusto-Client zu starten, geben Sie die folgenden Befehle ein, und wählen Sie **Ausführen** aus:  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. Öffnen Sie an der Eingabeaufforderung eine neue Browserregisterkarte zu [https://aka.ms/devicelogin](https://aka.ms/devicelogin). 
   
1. Geben Sie den Autorisierungscode ein, und melden Sie sich bei Ihrem AAD-Konto (@microsoft.com) an. Der Kusto-Client `kc` kann sich jetzt mit Ihrer Identität bei Azure Data Explorer authentifizieren.

1. Wechseln Sie zu Ihrem Notebook zurück, um das Ergebnis der Authentifizierung anzuzeigen. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Ausführen einer Kusto-Abfrage

1. Geben Sie Ihre Kusto-Abfrage ein, und wählen Sie **Ausführen** aus. Beispiel:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* [GitHub-Repository für Kusto-python](https://github.com/Azure/azure-kusto-python)
