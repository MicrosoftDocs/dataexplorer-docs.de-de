---
title: Kusto.WebExplorer - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto.WebExplorer in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1f6926df09a207cfea2b9201ef57f36932a63f74
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523867"
---
# <a name="kustowebexplorer"></a>Kusto.WebExplorer

Kusto.WebExplorer ist eine Webanwendung, die verwendet werden kann, um Abfragen und Steuerbefehle an einen Kusto-Dienst zu senden. Die Anwendung wirdhttps://dataexplorer.azure.com/unter [ ] gehostethttps://aka.ms/kweund kurz verknüpft von [ ].



Kusto.WebExplorer kann auch von anderen Webportalen in einem HTML-IFRAME gehostet werden.
(Dies geschieht z. B. über das [Azure-Portal.)](https://portal.azure.com) Siehe [Monaco IDE](../api/monaco/monaco-kusto.md) für Details, wie es zu hosten und der Monaco-Editor verwendet.

## <a name="connect-to-multiple-clusters"></a>Herstellen einer Verbindung mit mehreren Clustern

Sie können jetzt mehrere Cluster verbinden und zwischen Datenbanken und Clustern wechseln.
Das Tool wurde entwickelt, um den Cluster und die Datenbank, mit der Sie verbunden sind, leicht zu identifizieren.

![alt text](./Images/KustoTools-WebExplorer/AddingCluster.gif "AddingCluster")

## <a name="recall-results"></a>Rückrufergebnisse

Häufig führen wir während der Analyse mehrere Abfragen aus und müssen möglicherweise die Ergebnisse der vorherigen Abfragen erneut überprüfen. Sie können diese Funktion verwenden, um Ihre Ergebnisse abzurufen, ohne die Abfrage erneut ausführen zu müssen. Die Daten werden aus dem lokalen clientseitigen Cache bereitgestellt.

![alt text](./Images/KustoTools-WebExplorer/RecallResults.gif "RecallErgebnisse")

## <a name="enhanced-results-grid-control"></a>Verbesserte Ergebnis-Rastersteuerung

Im Tabellenraster können Sie mehrere Zeilen, Spalten und Zellen auswählen. Berechnen Sie Aggregate, indem Sie mehrere Zellen (z. B. Excel) auswählen und die Daten schwenken.

![alt text](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Intellisense & Formatierung

Sie können das Format "Hübsch drucken" verwenden, indem Sie die Tastenkombination "Umschalt + Alt + F", das Falten von Codes (Gliederung) und IntelliSense verwenden.

![alt text](./Images/KustoTools-WebExplorer/Formating.gif "Formatierung")

## <a name="deep-linking"></a>Tiefe Verknüpfung

Sie können nur den Deep Link oder Deep Link und die Abfrage kopieren. Sie können die URL auch so formatieren, dass sie den Cluster, die Datenbank und die Abfrage einschließt, indem Sie die folgende Vorlage verwenden:

`https://dataexplorer.azure.com/`[`clusters/` *Cluster* Cluster`/databases/` [`?` *Datenbank* [ *Optionen*]]]

Es können die folgenden Optionen angegeben werden:

* `workspace=empty`: Gibt an, einen neuen leeren Arbeitsbereich zu erstellen (es werden keine Rückrufaktionen früherer Cluster, Registerkarten und Abfragen durchgeführt).



![alt text](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>Feedback

Sie können Ihr Feedback über das Tool einreichen.
![alt text](./Images/KustoTools-WebExplorer/Feedback.gif "Feedback")