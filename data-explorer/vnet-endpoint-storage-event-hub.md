---
title: Erstellen eines von Datenverbindungen genutzten privaten Endpunkts oder Dienstendpunkts für Ressourcen, etwa Event Hub und Azure Storage
description: Hier erfahren Sie, wie Sie einen von Datenverbindungen genutzten privaten Endpunkt oder Dienstendpunkt für Ressourcen aktivieren, etwa Event Hub und Azure Storage.
author: orspod
ms.author: orspodek
ms.reviewer: gunjand
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: a8e351dc04b77a41dd7ab793581a1f464f181f4e
ms.sourcegitcommit: 724d3a3c817867b17a5a5853b6433818cbc97cf7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97050517"
---
# <a name="create-a-private-or-service-endpoint-to-event-hub-and-azure-storage"></a>Erstellen eines privaten Endpunkts oder eines Dienstendpunkts für Event Hub und Azure Storage

[Azure Virtual Network (VNET)](/azure/virtual-network/virtual-networks-overview) ermöglicht zahlreichen Azure-Ressourcentypen die sichere Kommunikation untereinander. Mit [Azure Private Link](/azure/private-link/) können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-Dienste sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste zugreifen. Ein [privater Endpunkt](/azure/private-link/private-endpoint-overview) verwendet eine IP-Adresse aus dem Adressraum Ihres VNET für den Azure-Dienst, um eine sichere Verbindung zwischen Azure Data Explorer und Azure-Diensten wie Azure Storage und Event Hub zu ermöglichen. Azure Data Explorer greift über den Microsoft-Backbone auf den privaten Endpunkt der Speicherkonten oder von Event Hubs zu, und die gesamte Kommunikation (etwa Datenexport, externe Tabellen und Datenerfassung) erfolgt über die private IP-Adresse. 

Im Gegensatz zu einem privaten Endpunkt ist ein [Dienstendpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview) weiterhin eine IP-Adresse, die öffentlich geroutet werden kann. Ein Virtual Network-Dienstendpunkt (VNET) bietet eine sichere und direkte Verbindung mit Azure-Diensten über eine optimierte Route über das Azure-Backbonenetzwerk. 

In diesem Artikel erfahren Sie, wie Sie eine Verbindung zwischen Azure Data Explorer und [Event Hub](ingest-data-event-hub-overview.md) oder [Azure Storage](/azure/storage/) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

* [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
* [Azure Data Explorer-Subnetz](vnet-deployment.md)

## <a name="private-endpoint"></a>Privater Endpunkt

Ein [privater Endpunkt](/azure/private-link/private-endpoint-overview) in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure [Private Link](/azure/private-link/) betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse in Ihrem VNet und bindet den Dienst effektiv in Ihr VNet ein. 
 
# <a name="azure-storage---private-endpoint"></a>[Azure Storage: Privater Endpunkt](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>Zulassen des Zugriffs auf ein Azure Storage-Konto aus Azure Data Explorer-Subnetzen mithilfe eines privaten Endpunkts

Ein Tutorial zum Erstellen eines privaten Endpunkts in Ihrem Azure Storage-Konto finden Sie unter [Tutorial: Herstellen einer Verbindung mit einem Speicherkonto mithilfe eines privaten Endpunkts in Azure](/azure/private-link/tutorial-private-endpoint-storage-portal).

Wählen Sie in diesem Tutorial das VNET, in dem sich das Azure Data Explorer-Subnetz befindet, und das Azure Data Explorer-Subnetz aus.

# <a name="event-hub---private-endpoint"></a>[Event Hub: Privater Endpunkt](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>Zulassen des Zugriffs auf Azure Event Hub aus Azure Data Explorer-Subnetzen mithilfe eines privaten Endpunkts

Ein Tutorial zum Erstellen eines privaten Endpunkts in Ihrem Event Hub finden Sie unter [Hinzufügen eines privaten Endpunkts über das Azure-Portal](/azure/event-hubs/private-link-service#add-a-private-endpoint-using-azure-portal).

Wählen Sie in diesem Tutorial das VNET, in dem sich das Azure Data Explorer-Subnetz befindet, und das Azure Data Explorer-Subnetz aus.

---

## <a name="service-endpoint"></a>Dienstendpunkt

Der Virtual Network-[Dienstendpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview) (VNET) bietet eine sichere und direkte Verbindung mit Azure-Diensten über eine optimierte Route über das Azure-Backbonenetzwerk. Endpunkte ermöglichen es Ihnen, Ihre kritischen Ressourcen von Azure-Diensten auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. 

# <a name="azure-storage---service-endpoint"></a>[Azure Storage: Dienstendpunkt](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>Zulassen des Zugriffs auf ein Azure Storage-Konto aus Azure Data Explorer-Subnetzen mithilfe eines Dienstendpunkts

In diesem Abschnitt erfahren Sie, wie Sie mit dem Azure-Portal einen VNET-Dienstendpunkt hinzufügen. Integrieren Sie zum Beschränken des Zugriffs den VNET-Dienstendpunkt für dieses Azure Storage-Konto.

### <a name="add-a-virtual-network"></a>Hinzufügen eines virtuellen Netzwerks 

1. Navigieren Sie zu dem Speicherkonto, das Sie sichern möchten.
1. Wählen Sie im Menü auf der linken Seite **Firewalls und virtuelle Netzwerke** aus.
1. Wählen Sie unter „Zugriff erlauben von“ die Option **Ausgewählte Netzwerke** aus.
1. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Vorhandenes virtuelles Netzwerk hinzufügen** aus. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-existing-vnet.png" alt-text="Hinzufügen einer vorhandenen VNET-Verbindung in Azure Storage zu Azure Data Explorer":::

### <a name="add-networks-pane"></a>Bereich „Netzwerke hinzufügen“

:::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-networks.png" alt-text="Hinzufügen eines virtuellen Netzwerks zu einem Azure Storage-Konto, um eine Verbindung mit Azure Data Explorer herzustellen":::

1. Wählen Sie auf der rechten Seite im Bereich **Netzwerke hinzufügen** Ihr Azure-Abonnement aus.

1. Wählen Sie in der Liste mit den virtuellen Netzwerken das virtuelle Netzwerk und anschließend das Subnetz aus. 

    > [!NOTE]
    > Aktivieren Sie den Dienstendpunkt, bevor Sie das virtuelle Netzwerk der Liste hinzufügen. Wenn der Dienstendpunkt nicht aktiviert ist, erhalten Sie im Portal eine entsprechende Aufforderung.
    
1. Wählen Sie **Hinzufügen**.

### <a name="save-and-verify-virtual-network-settings"></a>Speichern und Überprüfen der Einstellungen des virtuellen Netzwerks

1. Wählen Sie auf der Symbolleiste **Speichern** aus, um die Einstellungen zu speichern. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-virtual-network.png" alt-text="VNET zum Verbinden eines Speicherkontos mit Azure Data Explorer":::

    Warten Sie einige Minuten, bis die Bestätigung in den Portalbenachrichtigungen angezeigt wird.

# <a name="event-hub---service-endpoint"></a>[Event Hub:Dienstendpunkt](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>Zulassen des Zugriffs auf Azure Event Hub aus Azure Data Explorer-Subnetzen mithilfe eines Dienstendpunkts

> [!IMPORTANT]
> Virtuelle Netzwerke werden in den Tarifen **Standard** und **Dedicated** von Event Hubs, aber nicht im Tarif „Basic“ unterstützt. 

### <a name="add-a-virtual-network"></a>Hinzufügen eines virtuellen Netzwerks

1. Navigieren Sie im Azure-Portal zu dem **Event Hubs-Namespace**, den Sie schützen möchten.
1. Wählen Sie im Menü auf der linken Seite die Option **Netzwerk** aus. Diese Registerkarte wird nur in Namespaces vom Typ **Standard** oder **Dedicated** angezeigt.
1. Schließen Sie die Registerkarte **Firewalls und virtuelle Netzwerke**. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/networking.png" alt-text="„Netzwerk“ in Event Hub":::

1. Wählen Sie unter „Zugriff erlauben von“ die Option **Ausgewählte Netzwerke** aus.
1. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Vorhandenes virtuelles Netzwerk hinzufügen** aus. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-add-existing-vnet.png" alt-text="Hinzufügen eines vorhandenen virtuellen Netzwerks in Azure Data Explorer":::

### <a name="add-networks-pane"></a>Bereich „Netzwerke hinzufügen“

:::image type="content" source="media/vnet-private-link-storage-event-hub/add-networks.png" alt-text="Hinzufügen von Netzwerkfeldern zum Verbinden eines VNET mit Azure Data Explorer":::  

1. Wählen Sie auf der rechten Seite im Bereich **Netzwerke hinzufügen** Ihr Azure-Abonnement aus.

1. Wählen Sie in der Liste mit den virtuellen Netzwerken das virtuelle Netzwerk und anschließend das Subnetz aus. Sie müssen den Dienstendpunkt aktivieren, bevor Sie das virtuelle Netzwerk der Liste hinzufügen. 
    > [!NOTE]
    > Aktivieren Sie den Dienstendpunkt, bevor Sie das virtuelle Netzwerk der Liste hinzufügen. Wenn der Dienstendpunkt nicht aktiviert ist, erhalten Sie im Portal eine entsprechende Aufforderung.
1. Wählen Sie **Hinzufügen**.

### <a name="save-and-verify-virtual-network-settings"></a>Speichern und Überprüfen der Einstellungen des virtuellen Netzwerks

1. Wählen Sie auf der Symbolleiste **Speichern** aus, um die Einstellungen zu speichern. Warten Sie einige Minuten, bis die Bestätigung in den Portalbenachrichtigungen angezeigt wird.
    
    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-firewalls-and-vnet.png" alt-text="Hinzufügen eines virtuellen Netzwerks und Subnetzes in Event Hub, um eine Verbindung mit Azure Data Explorer herzustellen"::: 

---

## <a name="next-steps"></a>Nächste Schritte

* [Exportieren von Daten in den Speicher](kusto/management/data-export/export-data-to-storage.md)
* [Erfassen von Daten aus Event Hub in Azure Data Explorer](ingest-data-event-hub.md)
