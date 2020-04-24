---
title: Erfassen von Daten in Azure Data Explorer mithilfe der Streamingerfassung
description: Hier erfahren Sie, wie Sie Daten mithilfe der Streamingerfassung in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 219a9014b120e0df74f8d9d286253fa933c8f05a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493650"
---
# <a name="streaming-ingestion-preview"></a>Streamingerfassung (Vorschauversion)

Verwenden Sie die Streamingerfassung, wenn Sie eine geringe Wartezeit mit einer Erfassungsdauer von weniger als zehn Sekunden für Daten mit unterschiedlichem Volumen benötigen. Sie dient zur Optimierung der operativen Verarbeitung vieler Tabellen in einer oder mehreren Datenbanken, bei denen der Datenstrom für die einzelnen Tabellen jeweils relativ klein (wenige Datensätze pro Sekunde), das Gesamtvolumen der Datenerfassung aber hoch ist (mehrere tausend Datensätze pro Sekunde). 

Sollte die Datenmenge mehr als 1 MB pro Sekunde und Tabelle betragen, verwenden Sie anstelle der Streamingerfassung die Massenerfassung. Weitere Informationen zu den verschiedenen Erfassungsmethoden finden Sie in der [Übersicht über die Datenerfassung](/azure/data-explorer/ingest-data-overview).

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Melden Sie sich bei der [Webbenutzeroberfläche](https://dataexplorer.azure.com/) an.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Aktivieren der Streamingerfassung in Ihrem Cluster

> [!WARNING]
> Überprüfen Sie vor dem Aktivieren der Streamingerfassung die [Einschränkungen](#limitations).

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus. 
1. Wählen Sie im Bereich **Konfigurationen** die Option **Ein** aus, um die **Streamingerfassung** zu aktivieren.
1. Wählen Sie **Speichern** aus.
 
    ![Streamingerfassung aktiviert](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Definieren Sie auf der [Webbenutzeroberfläche](https://dataexplorer.azure.com/) die [Streamingerfassungsrichtlinie](kusto/management/streamingingestionpolicy.md) für Tabellen oder Datenbanken, die Streamingdaten empfangen. 

    > [!NOTE]
    > * Wird die Richtlinie auf der Datenbankebene definiert, kann die Streamingerfassung für alle Tabellen in der Datenbank verwendet werden.
    > * Die angewendete Richtlinie kann nur auf neu erfasste Daten und nicht auf andere Tabellen in der Datenbank verweisen.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Erfassen von Daten in Ihrem Cluster mithilfe der Streamingerfassung

Es werden zwei Streamingerfassungstypen unterstützt:


* [**Event Hub**](/azure/data-explorer/ingest-data-event-hub) (wird als Datenquelle verwendet)
* Für die **benutzerdefinierte Erfassung** muss eine Anwendung geschrieben werden, die eine der Azure Data Explorer-Clientbibliotheken verwendet. Eine Beispielanwendung finden Sie [hier](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample).

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Auswählen des geeigneten Streamingerfassungstyps

|   |Event Hub  |Benutzerdefinierte Erfassung  |
|---------|---------|---------|
|Datenverzögerung zwischen der Initiierung der Erfassung und der Verfügbarkeit der Daten für Abfragen   |    Längere Verzögerung     |   Kürzere Verzögerung      |
|Zusätzlicher Entwicklungsaufwand    |   Schnelle und einfache Einrichtung, kein zusätzlicher Entwicklungsaufwand    |   Hoher zusätzlicher Entwicklungsaufwand für die Anwendung, um Fehler zu behandeln und die Datenkonsistenz sicherzustellen     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Deaktivieren der Streamingerfassung in Ihrem Cluster

> [!WARNING]
> Die Deaktivierung der Streamingerfassung kann mehrere Stunden dauern.

1. Entfernen Sie die [Streamingerfassungsrichtlinie](kusto/management/streamingingestionpolicy.md) aus allen relevanten Tabellen und Datenbanken. Wenn Sie die Streamingerfassungsrichtlinie entfernen, werden die Streamingerfassungsdaten aus dem anfänglichen Speicher in den permanenten Speicher im Spaltenspeicher (Erweiterungen oder Shards) verschoben. Die Datenverschiebung kann zwischen einigen Sekunden und mehreren Stunden dauern. Dies ist abhängig von der Datenmenge im anfänglichen Speicher sowie davon, wie die CPU und der Arbeitsspeicher vom Cluster genutzt werden.
1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus. 
1. Wählen Sie im Bereich **Konfigurationen** die Option **Off** aus, um die **Streamingerfassung** zu aktivieren.
1. Wählen Sie **Speichern** aus.

    ![Streamingerfassung deaktiviert](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Einschränkungen

* [Datenbankcursor](kusto/management/databasecursor.md) und [Datenzuordnung](kusto/management/mappings.md) werden von der Streamingerfassung nicht unterstützt. Es wird nur die [vorab erstellte](kusto/management/create-ingestion-mapping-command.md) Datenzuordnung unterstützt. 
* Leistung und Kapazität der Streamingerfassung werden für größere virtuelle Computer und Cluster skaliert. Gleichzeitige Erfassungen sind auf sechs Erfassungen pro Kern beschränkt. Beispielsweise besteht die maximale unterstützte Last bei 16-Kern-SKUs (z.B. D14 und L16) aus 96 gleichzeitigen Erfassungen. Bei 2-Kern-SKUs (z. B. D11) besteht die maximale unterstützte Last aus 12 gleichzeitigen Erfassungen.
* Die Datengröße pro Erfassungsanforderung ist auf 4 MB beschränkt.
* Schemaaktualisierungen wie etwa die Erstellung und Änderung von Tabellen und Erfassungszuordnungen können für den Streamingerfassungsdienst bis zu fünf Minuten dauern.
* Wenn die Streamingerfassung in einem Cluster aktiviert wird, wird ein Teil des lokalen SSD-Datenträgers der Clustercomputer für Streamingerfassungsdaten genutzt, wodurch sich der verfügbare Speicherplatz für den aktiven Cache verringert. Dies gilt auch, wenn gar keine Daten per Streaming erfasst werden.
* [Erweiterungstags](kusto/management/extents-overview.md#extent-tagging) können für die Streamingerfassungsdaten nicht festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
