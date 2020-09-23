---
title: Aktivieren von isoliertem Computing in Ihrem Azure Data Explorer-Cluster
description: In diesem Artikel erfahren Sie, wie Sie isoliertes Computing in Ihrem Azure Data Explorer-Cluster aktivieren, indem Sie die passende SKU auswählen.
author: orspod
ms.author: orspodek
ms.reviewer: dagrawal
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.custom: references_regions
ms.openlocfilehash: 8139701037a8d4d25490a355cc822051851c85cd
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682289"
---
# <a name="enable-isolated-compute-on-your-azure-data-explorer-cluster"></a>Aktivieren von isoliertem Computing in Ihrem Azure Data Explorer-Cluster

Mit virtuellen Computern (virtual machines, VMs) für isoliertes Computing können Kunden ihre Workload in einer hardwareisolierten, für einen einzelnen Kunden dedizierten Umgebung ausführen. Cluster, die mit virtuellen Computern für isoliertes Computing bereitgestellt werden, eignen sich am besten für Workloads, die ein hohes Maß an Isolation erfordern, um Complianceanforderungen und gesetzliche Anforderungen zu erfüllen. Die Compute-SKUs bieten Isolation zum Schutz von Daten, ohne auf die Flexibilität bei der Konfiguration verzichten zu müssen. Weitere Informationen finden Sie unter [Computeisolation](/azure/security/fundamentals/isolation-choices#compute-isolation) sowie im [Azure-Leitfaden für eine sichere Isolation](/azure/azure-government/azure-secure-isolation-guidance#compute-isolation). 

Azure Data Explorer bietet unterstützt isoliertes Computing mit der SKU **Standard_E64i_v3**. Diese SKU kann automatisch hoch- und herunterskaliert werden, um die Anforderungen Ihrer Anwendung oder Ihres Unternehmens zu erfüllen. Isoliertes Computing wird in folgenden Regionen unterstützt:
* USA, Westen 2
* East US 
* USA Süd Mitte

Virtuelle Computer für isoliertes Computing sind zwar teuer, aber die ideale SKU für die Ausführung von Workloads, die eine Isolation auf der Serverinstanzebene erfordern. Weitere Informationen zu unterstützten SKUs für Azure Data Explorer finden Sie unter [Auswählen der passenden VM-SKU für Ihren Azure Data Explorer-Cluster](manage-cluster-choose-sku.md).

> [!NOTE]
> [Azure Dedicated Host](https://azure.microsoft.com/services/virtual-machines/dedicated-host/) wird derzeit von Azure Data Explorer nicht unterstützt. 

## <a name="enable-isolated-compute-on-azure-data-explorer-cluster"></a>Aktivieren von isoliertem Computing im Azure Data Explorer-Cluster 

Verwenden Sie einen der folgenden Prozesse, wenn Sie isoliertes Computing in Azure Data Explorer aktivieren möchten:
* [Erstellen eines Clusters mit SKU für isoliertes Computing](#create-a-cluster-with-isolated-compute-sku)
* [Auswählen der SKU für isoliertes Computing für einen bereits vorhandenen Cluster](#select-the-isolated-compute-sku-on-an-existing-cluster)

## <a name="create-a-cluster-with-isolated-compute-sku"></a>Erstellen eines Clusters mit SKU für isoliertes Computing

1. Führen Sie die Schritte zum [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md) über das Azure-Portal aus.
1. Wählen Sie unter [Erstellen eines Clusters](create-cluster-database-portal.md#create-a-cluster) auf der Registerkarte **Basisinformationen** in der Dropdownliste **Computespezifikationen** die Option **Standard_E64i_v3** aus.

## <a name="select-the-isolated-compute-sku-on-an-existing-cluster"></a>Auswählen der SKU für isoliertes Computing für einen bereits vorhandenen Cluster

1. Wählen Sie auf dem Bildschirm **Übersicht** Ihres Azure Data Explorer-Clusters die Option **Hochskalieren** aus.
1. Suchen Sie über das Suchfeld nach *Standard_E64i_v3*, und klicken Sie auf den SKU-Namen, oder wählen Sie die SKU **Standard_E64i_v3** in der SKU-Liste aus.
1. Wählen Sie **Anwenden** aus, um Ihre SKU zu aktualisieren. 

:::image type="content" source="media/isolated-compute/select-isolated-compute-sku.png" alt-text="Auswählen der SKU für isoliertes Computing":::

> [!TIP]
> Das Hochskalieren kann einige Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten des vertikalen Hoch- oder Herunterskalierens eines Clusters in Azure Data Explorer, um ihn an den sich ändernden Bedarf anzupassen](manage-cluster-vertical-scaling.md)
* [Auswählen der passenden VM-SKU für Ihren Azure Data Explorer-Cluster](manage-cluster-choose-sku.md)
