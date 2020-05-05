---
title: Erfassen von Daten in Azure Data Explorer mithilfe der 1-Klick-Erfassung
description: Übersicht über das problemlose Erfassen (Laden) von Daten in Azure Data Explorer mit der 1-Klick-Erfassung.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5f424716a9227f22b4ec5b148d29798b0a55fa27
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618376"
---
# <a name="what-is-one-click-ingestion"></a>Was ist die 1-Klick-Erfassung? 

Mit der 1-Klick-Erfassung können Sie Daten schnell erfassen und basierend auf einer Datenquelle in Azure Data Explorer automatisch Tabellen und Zuordnungsstrukturen vorschlagen. 

Über die Azure Data Explorer-Webbenutzeroberfläche können Sie Daten aus Speicher (Blobdatei), aus einer lokalen Datei oder aus einem Container (bis zu 10.000 Blobs) erfassen. Für eine fortlaufende Erfassung können Sie darüber hinaus ein Ereignisraster für einen Container definieren. Die Daten können in einer vorhandenen oder neuen Tabelle im JSON-, CSV- oder einem [anderen Format](#file-formats) erfasst werden. Die 1-Klick-Erfassung kann basierend auf der Datenquelle eine Struktur für eine neue Tabelle und Tabellenzuordnung vorschlagen und eine intuitive Plattform bieten, um die Tabellenzuordnung und die Tabellenstruktur einer vorhandenen oder neuen Tabelle anzupassen. Bei der 1-Klick-Erfassung werden die Daten innerhalb weniger Minuten in die Tabelle übernommen.

Die 1-Klick-Erfassung ist besonders nützlich, wenn Daten erstmalig erfasst werden oder Sie nicht mit dem Schema Ihrer Daten vertraut sind.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).
* Melden Sie sich bei der [Azure Data Explorer-Webbenutzeroberfläche](https://dataexplorer.azure.com/) an, und [fügen Sie eine Verbindung mit Ihrem Cluster hinzu](/azure/data-explorer/web-query-data#add-clusters).

## <a name="file-formats"></a>Dateiformate

Die 1-Klick-Erfassung unterstützt das Erfassen einer neuen Tabelle aus Quelldaten in einem der folgenden Formate:
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>Assistent für die 1-Klick-Erfassung

Der Assistent für die 1-Klick-Erfassung leitet Sie durch die 1-Klick-Erfassung. 

> [!Note]
> In diesem Abschnitt werden die allgemeinen Optionen des Assistenten beschrieben. Welche Optionen Sie auswählen, hängt davon ab, ob Sie Daten in einer neuen oder in einer vorhandenen Tabelle erfassen. Weitere Informationen finden Sie unter
    > * Erfassung in einer [neuen Tabelle](one-click-ingestion-new-table.md)
    > * Erfassung in einer [vorhandenen Tabelle](one-click-ingestion-existing-table.md) 
    
1. Um den Assistenten zu starten, klicken Sie im linken Menü der Azure Data Explorer-Webbenutzeroberfläche mit der rechten Maustaste auf die Zeile *Datenbank* oder *Tabelle*, und wählen Sie **Neue Daten erfassen (Vorschau)** aus.

    ![Wählen Sie auf der Webbenutzeroberfläche die 1-Klick Erfassung aus.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. Der Assistent führt Sie durch die folgenden Optionen:
    * Erfassung in einer [vorhandenen Tabelle](one-click-ingestion-existing-table.md)
    * Erfassung in einer [neuen Tabelle](one-click-ingestion-new-table.md)
    * Erfassung von Daten aus:
      * Blob Storage
      * einer lokalen Datei
      * einem Container
    * Geben Sie die Stichprobengröße ein (1 bis 10.000 Zeilen, nur bei Containern).
       
1. Nach erfolgreicher Auswahl der Datenquelle wird eine Vorschau der Daten angezeigt. 
    Wenn Sie Daten aus einem Container erfassen, können Sie die Daten so filtern, dass nur Dateien mit bestimmten Präfixen oder Dateierweiterungen erfasst werden. Sie können z. B. nur Dateien erfassen, deren Dateinamen mit dem Begriff *Europa* beginnen, oder lediglich Dateien mit der Erweiterung *.json*. 

1. Klicken Sie auf **Schema bearbeiten**. Überprüfen Sie die automatisch generierten Schema- und Erfassungseigenschaften (Datenformat, Zuordnungsname usw.), und passen Sie sie an.

1. Starten Sie den Vorgang zur Datenerfassung.

> [!Note]
> Wenn Sie einen Container als Datenquelle verwenden, sollten Sie Folgendes beachten: Azure Data Explorer verfügt über eine Aggregationsrichtlinie (Batching) für die Datenerfassung, die für die Optimierung des Erfassungsprozesses konzipiert ist. Da die Richtlinie standardmäßig mit fünf Minuten oder einem Datenvolumen von 500 MB konfiguriert ist, kann es zu Wartezeiten kommen. Aggregationsoptionen finden Sie unter [IngestionBatching policy](kusto/management/batchingpolicy.md) (IngestionBatching-Richtlinie). Beim Erfassen von Daten aus anderen Quellen wird die Erfassung sofort ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

* Legen Sie fest, ob Sie die 1-Klick-Erfassung zum Erfassen von Daten in einer [vorhandenen Tabelle](one-click-ingestion-existing-table.md) oder [neuen Tabelle](one-click-ingestion-new-table.md) verwenden
* [Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](/azure/data-explorer/web-query-data)
* [Schreiben von Abfragen für Azure Data Explorer mithilfe der Kusto-Abfragesprache](/azure/data-explorer/write-queries)
