---
title: Erfassen von Daten aus Event Hub in Azure Data Explorer mithilfe der 1-Klick-Erfassung
description: In diesem Artikel erfahren Sie, wie Sie mit nur einem Klick Daten aus Event Hub in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 983b9c59e6130c58d04e1d4f1a7042a3aa73bba7
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554935"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-data-explorer"></a>Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mithilfe der 1-Klick-Erfassung

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [1-Klick](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md)

Azure-Daten-Explorer ermöglicht die Datenerfassung (das Laden von Daten) aus Event Hubs. Dabei handelt es sich um eine Big Data-Streamingplattform und einen Ereigniserfassungsdienst. [Event Hubs](/azure/event-hubs/event-hubs-about) kann Millionen von Ereignissen pro Sekunde nahezu in Echtzeit verarbeiten. In diesem Artikel verbinden Sie mithilfe der [1-Klick-Erfassung](ingest-data-one-click.md) einen Event Hub mit einer Tabelle in Azure Data Explorer.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Ein Cluster und eine Datenbank](create-cluster-database-portal.md).
* [Event Hub mit Daten für die Erfassung](ingest-data-event-hub.md#create-an-event-hub)

## <a name="ingest-new-data"></a>Erfassen neuer Daten

1. Klicken Sie im linken Menü der [Webbenutzeroberfläche](https://dataexplorer.azure.com/) mit der rechten Maustaste auf eine *Datenbank* oder *Tabelle*, und wählen Sie **Neue Daten erfassen** aus. 

:::image type="content" source="media/one-click-event-hub/one-click-ingestion-in-web-ui.png" alt-text="Auswählen der 1-Klick Erfassung auf der Webbenutzeroberfläche":::

Das Fenster **Neue Daten erfassen** wird mit der Registerkarte **Quelle** geöffnet.

:::image type="content" source="media/one-click-event-hub/reference-to-event-hub.png" alt-text="Screenshot: Registerkarte „Quelle“ im Fenster „Neue Daten erfassen“ in Azure Data Explorer. Für Quelle ist „Verweis auf Event Hub“ ausgewählt.":::

1. Das Feld **Datenbank** wird automatisch mit Ihrer Datenbank aufgefüllt. Über das Dropdownmenü kann eine andere Datenbank ausgewählt werden.

1. Wählen Sie unter **Tabelle** die Option **Neu erstellen** aus, und geben Sie einen Namen für die neue Tabelle ein, oder verwenden Sie eine vorhandene Tabelle. 

    > [!NOTE]
    > Tabellennamen müssen zwischen einem und 1024 Zeichen lang sein. Sie können alphanumerische Zeichen, Bindestriche und Unterstriche verwenden. Sonderzeichen werden nicht unterstützt.

1. Wählen Sie unter **Quelltyp** die Option **Verweis auf Event Hub** aus. Die Auswahl für die Datenverbindung wird angezeigt.

## <a name="data-connection"></a>Datenverbindung

1. Füllen Sie unter **Datenverbindung** die folgenden Felder aus:

    :::image type="content" source="media/one-click-event-hub/project-details.png" alt-text="Screenshot: Registerkarte „Quelle“ mit auszufüllenden Feldern für Projektdetails. Erfassen neuer Daten in Azure Data Explorer mit Event Hub mit nur einem Klick":::

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Name der Datenverbindung | *TestDataConnection*  | Der Name, der Ihre Datenverbindung bezeichnet
    | Subscription |      | Die Abonnement-ID, unter der sich die Event Hub-Ressource befindet  |
    | Event Hub-Namespace |  | Der Name, der Ihren Namespace bezeichnet |
    | Event Hub |  | Der zu verwendende Event Hub |
    | Consumergruppe |  | Die in Ihrem Event Hub definierte Consumergruppe |
    | Datenformat | | Daten werden in Form von [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata)-Objekten aus dem Event Hub gelesen. Die folgenden Formate werden unterstützt: CSV, JSON, PSV, SCsv, SOHsv TSV und TSVE. |
    | Ereignissystemeigenschaften | Auswählen relevanter Eigenschaften | Die [Event Hub-Systemeigenschaften](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Wenn pro Ereignisnachricht mehrere Datensätze vorhanden sind, werden die Systemeigenschaften dem ersten Datensatz hinzugefügt. Beim Hinzufügen von Systemeigenschaften [erstellen](kusto/management/create-table-command.md) oder [aktualisieren](kusto/management/alter-table-command.md) Sie das Tabellenschema und die [Zuordnung](kusto/management/mappings.md), um die ausgewählten Eigenschaften einzubeziehen. |

1. Wählen Sie **Schema bearbeiten** aus.

## <a name="schema-tab"></a>Registerkarte „Schema“

Informationen zur Schemazuordnung mit JSON-formatierten Daten finden Sie unter [Bearbeiten des Schemas](one-click-ingestion-existing-table.md#edit-the-schema).
Informationen zur Schemazuordnung mit CSV-formatierten Daten finden Sie unter [Bearbeiten des Schemas](one-click-ingestion-new-table.md#edit-the-schema).

:::image type="content" source="media/one-click-event-hub/schema-tab.png" alt-text="Screenshot: Registerkarte „Schema“ beim Erfassen neuer Daten in Azure Data Explorer mit Event Hub mit nur einem Klick":::

1. Sind die im Vorschaufenster angezeigten Daten nicht vollständig, benötigen Sie möglicherweise mehr Daten, um eine Tabelle mit allen erforderlichen Datenfeldern zu erstellen. Verwenden Sie die folgenden Befehle, um neue Daten aus Ihrem Event Hub abzurufen:
    * **Verwerfen und neue Daten abrufen**: Die dargestellten Daten werden verworfen, und es wird nach neuen Ereignissen gesucht.
    * **Fetch more data** (Weitere Daten abrufen): Zusätzlich zu den bereits gefundenen Ereignissen wird nach weiteren Ereignissen gesucht. 
    
    > [!NOTE]
    > Damit Sie eine Vorschau Ihrer Daten anzeigen können, muss Ihr Event Hub Ereignisse senden.
        
1. Wählen Sie **Erfassung starten** aus.

## <a name="complete-data-ingestion"></a>Abschließen der Datenerfassung

Im Fenster **Datenerfassung abgeschlossen** werden alle Schritte mit grünen Häkchen markiert, wenn die Datenerfassung erfolgreich abgeschlossen wurde. Die Karten unter diesen Schritten enthalten Optionen zum Untersuchen Ihrer Daten mit **Schnellabfragen**, zum Rückgängigmachen von Änderungen, die mithilfe von **Tools** vorgenommen wurden, oder zum **Überwachen** der Event Hub-Verbindungen und -Daten.

:::image type="content" source="media/one-click-event-hub/data-ingestion-completed.png" alt-text="Screenshot: Letzter Bildschirm bei der 1-Klick-Erfassung in Azure Data Explorer aus Event Hub":::

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md)
* [Schreiben von Abfragen für Azure Data Explorer mithilfe der Kusto-Abfragesprache](write-queries.md)
