---
title: Kusto. Webexplorer-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Kusto. Webexplorer in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: d53f12c4a0c4dd2bce669dbe004b8f325db27af5
ms.sourcegitcommit: 4986354cc1ba25c584e4f3c7eac7b5ff499f0cf1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84856386"
---
# <a name="kustowebexplorer"></a>Kusto. Webexplorer

Kusto. Webexplorer ist eine Webanwendung, die zum Senden von Abfragen und Steuern von Befehlen an einen Kusto-Dienst verwendet werden kann. Die Anwendung wird unter https://dataexplorer.azure.com/ und kurz verknüpft von gehostet https://aka.ms/kwe .



"Kusto. Webexplorer" kann auch von anderen Webportalen in einem HTML-IFrame gehostet werden.
(Dies wird z. b. durch den [Azure-Portal](https://portal.azure.com)erreicht.) In der [Monaco-IDE](../api/monaco/monaco-kusto.md) finden Sie ausführliche Informationen zum Hosten und zum Hosten von Monaco-Editor.

## <a name="connect-to-multiple-clusters"></a>Herstellen einer Verbindung mit mehreren Clustern

Sie können jetzt mehrere Cluster verbinden und zwischen Datenbanken und Clustern wechseln.
Das Tool ist so konzipiert, dass der Cluster und die Datenbank, mit der Sie verbunden sind, problemlos identifiziert werden können.

![alt text](./Images/KustoTools-WebExplorer/AddingCluster.gif "Addingcluster")

## <a name="recall-results"></a>Rückruf Ergebnisse

Häufig werden bei der Analyse mehrere Abfragen ausgeführt, und die Ergebnisse der vorherigen Abfragen müssen möglicherweise erneut überprüft werden. Sie können diese Funktion verwenden, um die Ergebnisse abzurufen, ohne die Abfrage erneut ausführen zu müssen. Die Daten werden aus dem lokalen Client seitigen Cache bereitgestellt.

![alt text](./Images/KustoTools-WebExplorer/RecallResults.gif "Recallresults")

## <a name="enhanced-results-grid-control"></a>Raster Steuerelement für erweiterte Ergebnisse

Mit dem Tabellen Raster können Sie mehrere Zeilen, Spalten und Zellen auswählen. Berechnen Sie Aggregate durch Auswahl mehrerer Zellen (z. b. Excel), und pivotieren Sie die Daten.

![alt text](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "Enhancedgrid")

## <a name="intellisense--formatting"></a>IntelliSense-& Formatierung

Sie können das Recht Abdruck Format verwenden, indem Sie die Tastenkombination UMSCHALT + ALT + F, Code Falten (Gliederung) und IntelliSense verwenden.

![alt text](./Images/KustoTools-WebExplorer/Formating.gif "Wird formatieren")

## <a name="deep-linking"></a>Deep Linking

Sie können nur den Deep-Link oder Deep-Link und die Abfrage kopieren. Mithilfe der folgenden Vorlage können Sie auch die URL so formatieren, dass Sie den Cluster, die Datenbank und die Abfrage einschließt:

`https://dataexplorer.azure.com/`[ `clusters/` *Cluster* [ `/databases/` *Database* [ `?` *Optionen*]]]

Die folgenden Optionen können angegeben werden:

* `workspace=empty`: Gibt an, dass ein neuer leerer Arbeitsbereich erstellt werden soll (kein Rückruf vorheriger Cluster, Registerkarten und Abfragen).



![alt text](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>Feedback

Sie können Ihr Feedback über das Tool einreichen.
![alt text](./Images/KustoTools-WebExplorer/Feedback.gif "Feedback")
