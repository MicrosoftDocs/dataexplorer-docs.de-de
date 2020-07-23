---
title: Konfigurieren der Streamingerfassung in Ihrem Azure Data Explorer-Cluster mithilfe des Azure-Portals
description: Hier erfahren Sie, wie Sie Ihren Azure Data Explorer-Cluster konfigurieren und mit dem Laden von Daten mit der Streamingerfassung mithilfe des Azure-Portals beginnen.
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7e5d836e25916a039a4df8e451d16e4f8a41cf18
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423217"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-the-azure-portal"></a>Konfigurieren der Streamingerfassung in Ihrem Azure Data Explorer-Cluster mithilfe des Azure-Portals

> [!div class="op_single_selector"]
> * [Portal](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Aktivieren der Streamingerfassung in Ihrem Cluster

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster-in-the-azure-portal"></a>Aktivieren der Streamingerfassung beim Erstellen eines neuen Clusters im Azure-Portal

Sie können die Streamingerfassung aktivieren, während Sie einen neuen Azure Data Explorer-Cluster erstellen. 

Wählen Sie auf der Registerkarte **Konfigurationen** die Optionen **Streamingerfassung** > **Ein** aus.

:::image type="content" source="media/ingest-data-streaming/cluster-creation-enable-streaming.png" alt-text="Aktivieren der Streamingerfassung beim Erstellen eines Clusters in Azure Data Explorer":::

### <a name="enable-streaming-ingestion-on-an-existing-cluster-in-the-azure-portal"></a>Aktivieren der Streamingerfassung für einen vorhandenen Cluster im Azure-Portal

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster. 
1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus. 
1. Wählen Sie im Bereich **Konfigurationen** die Option **Ein** aus, um die **Streamingerfassung** zu aktivieren.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-on.png" alt-text="Aktivieren der Streamingerfassung in Azure Data Explorer":::

> [!WARNING]
> Überprüfen Sie vor dem Aktivieren der Streamingerfassung die [Einschränkungen](#limitations).

## <a name="create-a-target-table-and-define-the-policy-in-the-azure-portal"></a>Erstellen einer Zieltabelle und Definieren der Richtlinie im Azure-Portal

1. Navigieren Sie im Azure-Portal zu Ihrem Cluster.
1. Wählen Sie **Abfrage**.

    :::image type="content" source="media/ingest-data-streaming/cluster-select-query-tab.png" alt-text="Auswählen von „Abfrage“ im Azure Data Explorer-Portal zum Aktivieren der Streamingerfassung":::

1. Kopieren Sie den folgenden Befehl in den **Abfragebereich**, und wählen Sie **Ausführen** aus, um die Tabelle zu erstellen, die die Daten über Streamingerfassung erhalten soll:

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    :::image type="content" source="media/ingest-data-streaming/create-table.png" alt-text="Erstellen einer Tabelle für die Streamingerfassung in Azure Data Explorer":::

1. Definieren Sie die [Streamingerfassungsrichtlinie](kusto/management/streamingingestionpolicy.md) für die von Ihnen erstellte Tabelle oder für die Datenbank, die diese Tabelle enthält. 
 
    > [!TIP]
    > Eine Richtlinie, die auf Datenbankebene definiert ist, gilt für alle vorhandenen und zukünftigen Tabellen in der Datenbank. 
    
1. Kopieren Sie einen der folgenden Befehle in den **Abfragebereich**, und wählen Sie **Ausführen** aus:

    ```kusto
    .alter table TestTable policy streamingingestion enable
    ```

    oder

    ```kusto
    .alter database StreamingTestDb policy streamingingestion enable
    ```

    :::image type="content" source="media/ingest-data-streaming/define-streaming-ingestion-policy.png" alt-text="Definieren der Streamingerfassungsrichtlinie in Azure Data Explorer":::

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

## <a name="drop-the-streaming-ingestion-policy-in-the-azure-portal"></a>Löschen der Streamingerfassungsrichtlinie im Azure-Portal

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster, und wählen Sie **Abfrage** aus. 
1. Kopieren Sie zum Löschen der Streamingerfassungsrichtlinie aus der Tabelle den folgenden Befehl in den **Abfragebereich**, und wählen Sie **Ausführen** aus:

    ```Kusto
    .delete table TestTable policy streamingingestion 
    ```

    :::image type="content" source="media/ingest-data-streaming/delete-streaming-ingestion-policy.png" alt-text="Löschen der Streamingerfassungsrichtlinie in Azure Data Explorer":::

1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus.
1. Wählen Sie im Bereich **Konfigurationen** die Option **Off** aus, um die **Streamingerfassung** zu aktivieren.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-off.png" alt-text="Deaktivieren der Streamingerfassung in Azure Data Explorer":::

[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
