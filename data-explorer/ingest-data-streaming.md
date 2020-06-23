---
title: Erfassen von Daten in Azure Data Explorer mithilfe der Streamingerfassung
description: Hier erfahren Sie, wie Sie Daten mithilfe der Streamingerfassung in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 66118ad7b91d1bcfdd52679d4bfa3dc5ebc4c281
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780234"
---
# <a name="streaming-ingestion-preview"></a>Streamingerfassung (Vorschauversion)

Verwenden Sie die Streamingerfassung, wenn Sie eine geringe Wartezeit mit einer Erfassungsdauer von weniger als zehn Sekunden für Daten mit unterschiedlichem Volumen benötigen. Sie dient zur Optimierung der operativen Verarbeitung vieler Tabellen in einer oder mehreren Datenbanken, bei denen der Datenstrom für die einzelnen Tabellen jeweils relativ klein (wenige Datensätze pro Sekunde), das Gesamtvolumen der Datenerfassung aber hoch ist (mehrere tausend Datensätze pro Sekunde). 

Sollte die Datenmenge mehr als 4 GB pro Stunde und Tabelle betragen, verwenden Sie anstelle der Streamingerfassung die Sammelerfassung. Weitere Informationen zu den verschiedenen Erfassungsmethoden finden Sie in der [Übersicht über die Datenerfassung](ingest-data-overview.md).

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

* [**Event Hub**](ingest-data-event-hub.md) (wird als Datenquelle verwendet).
* Für die **benutzerdefinierte Erfassung** muss eine Anwendung geschrieben werden, die eine der [Clientbibliotheken](kusto/api/client-libraries.md) von Azure Data Explorer verwendet. Eine Beispielanwendung finden Sie [hier](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample).

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

* [Datenbankcursor](kusto/management/databasecursor.md) werden für die Datenbank nicht unterstützt, wenn für diese selbst oder für eine Ihrer Tabellen eine [Richtlinie für die Streamingerfassung](kusto/management/streamingingestionpolicy.md) definiert und aktiviert wurde.
* Die [Datenzuordnung](kusto/management/mappings.md) muss [vorab erstellt](kusto/management/create-ingestion-mapping-command.md) werden, damit sie bei der Streamingerfassung verwendet werden kann. Individuelle Anforderungen zur Streamingerfassung bieten keine Inlinedatenzuordnungen.
* Leistung und Kapazität der Streamingerfassung werden für größere virtuelle Computer und Cluster skaliert. Die Anzahl der gleichzeitigen Erfassungsanforderungen ist auf sechs pro Kern beschränkt. Beispielsweise besteht die maximale unterstützte Last bei SKUs mit 16 Kernen (z. B. D14 und L16) aus 96 gleichzeitigen Erfassungsanforderungen. Bei SKUs mit 2 Kernen (z. B. D11) werden als maximale Last 12 gleichzeitige Erfassungsanfragen unterstützt.
* Die Datengröße für Anforderungen zur Streamingerfassung ist auf 4 MB beschränkt.
* Schemaaktualisierungen wie etwa die Erstellung und Änderung von Tabellen und Erfassungszuordnungen können für den Streamingerfassungsdienst bis zu fünf Minuten dauern. Weitere Informationen finden Sie unter [Streamingerfassung und Schemaänderungen](kusto/management/data-ingestion/streaming-ingestion-schema-changes.md).
* Wenn die Streamingerfassung in einem Cluster aktiviert wird, wird ein Teil des lokalen SSD-Datenträgers der Clustercomputer für Streamingerfassungsdaten genutzt, wodurch sich der verfügbare Speicherplatz für den aktiven Cache verringert. Dies gilt auch, wenn gar keine Daten per Streaming erfasst werden.
* [Erweiterungstags](kusto/management/extents-overview.md#extent-tagging) können für die Streamingerfassungsdaten nicht festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
