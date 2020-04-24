---
title: Auswählen der passenden VM-SKU für Ihren Azure Data Explorer-Cluster
description: In diesem Artikel erfahren Sie, wie Sie die optimale SKU-Größe für den Azure Data Explorer-Cluster auswählen.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494486"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Auswählen der passenden VM-SKU für Ihren Azure Data Explorer-Cluster 

Wenn Sie einen neuen Cluster erstellen oder einen Cluster für eine veränderliche Workload optimieren, bietet Azure Data Explorer mehrere VM-SKUs zur Auswahl an. Die VMs wurden sorgfältig ausgewählt, um die optimalsten Kosten für jede Workload zu erzielen. 

Größe und VM-SKU des Datenverwaltungsclusters werden vollständig vom Azure Data Explorer-Dienst verwaltet. Sie werden durch Faktoren wie die VM-Größe der Engine und die Erfassungsworkload bestimmt. 

Sie können die VM-SKU für den Engine-Cluster durch [Skalierung des Clusters](manage-cluster-vertical-scaling.md) jederzeit ändern. Es ist am besten, mit der kleinsten SKU-Größe zu beginnen, die zum Ausgangsszenario passt. Beachten Sie, dass die Skalierung des Clusters zu einer Ausfallzeit von bis zu 30 Minuten führt, während der Cluster mit der neuen VM-SKU neu erstellt wird.

> [!TIP]
> Die Berechnung von [reservierten Instanzen (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) ist auf den Azure Data Explorer-Cluster anwendbar.  

Dieser Artikel beschreibt verschiedene VM-SKU-Optionen und liefert die technischen Details, die Ihnen helfen, die beste Wahl zu treffen.

## <a name="select-a-cluster-type"></a>Auswählen des Clustertyps

Azure Data Explorer bietet zwei Arten von Clustern:

* **Produktion:** Produktionscluster enthalten zwei Knoten für Engine und Datenverwaltungscluster und werden unter dem Azure Data Explorer-[SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) betrieben.

* **Dev/Test (kein SLA):** Dev/Test-Cluster haben einen einzigen D11 v2-Knoten für den Engine-Cluster und einen einzigen D1-Knoten für den Datenverwaltungscluster. Dieser Clustertyp ist die kostengünstigste Konfiguration aufgrund seiner geringen Anzahl von Instanzen und der fehlenden Aufschlaggebühren für die Engine. Für diese Clusterkonfiguration gibt es kein SLA, da ihr die Redundanz fehlt.

## <a name="sku-types"></a>SKU-Typen

Wählen Sie beim Erstellen eines Azure Data Explorer-Clusters die *optimale* VM-SKU für die geplante Workload. Sie können aus den beiden folgenden Azure Data Explorer-SKU-Familien wählen:

* **D v2**: Die D-SKU ist für Compute optimiert und in zwei Varianten erhältlich:
    * Die VM selbst
    * Die VM mit Premium-Speicher für Datenträger im Paket

* **LS:** Die L-SKU ist datenspeicheroptimiert. Sie hat eine viel größere SSD-Größe als die preislich vergleichbare D-SKU.

Die wesentlichen Unterschiede zwischen den verfügbaren SKU-Typen werden in der folgenden Tabelle beschrieben:
 
| attribute | D-SKU | L-SKU |
|---|---|---
|**Kleine SKUs**|Mindestgröße ist D11 mit zwei Kernen|Mindestgröße ist L4 mit vier Kernen |
|**Verfügbarkeit**|In allen Regionen verfügbar. (Die DS+PS-Version ist nur eingeschränkt verfügbar.)|In einigen Region verfügbar |
|**Kosten pro&nbsp;GB-Cache pro Kern**|Hoch mit der D-SKU, gering mit der DS+PS-Version|Am niedrigsten mit der Option „Nutzungsbasierte Zahlung“ |
|**Preise für reservierte Instanzen (RI)**|Hoher Rabatt (über 55&nbsp;% für eine dreijährige Verpflichtung)|Niedrigerer Rabatt (20&nbsp;% für eine dreijährige Verpflichtung) |  

## <a name="select-your-cluster-vm"></a>Auswählen Ihres virtuellen Clustercomputers 

[Konfigurieren Sie die vertikale Skalierung](manage-cluster-vertical-scaling.md#configure-vertical-scaling), um Ihren virtuellen Clustercomputer auszuwählen. 

Mit den verschiedenen verfügbaren VM-SKU-Optionen können Sie die Kosten für die Leistung und die Hot-Cache-Anforderungen Ihres Szenarios optimieren. 
* Wenn Sie die optimale Leistung bei hohem Abfragevolumen benötigen, sollte die ideale SKU für Compute optimiert werden. 
* Wenn Sie große Datenmengen mit relativ geringerer Abfragelast benötigen, kann die speicheroptimierte SKU helfen, die Kosten zu senken, und bietet dennoch eine ausgezeichnete Leistung.

Da die Anzahl der Instanzen pro Cluster für die kleinen SKUs begrenzt ist, ist es besser, größere VMs mit mehr RAM zu verwenden. Mehr RAM wird für einige Abfragetypen benötigt, die die RAM-Ressource stärker beanspruchen, z. B. für Abfragen, die `joins` verwenden. Daher wird empfohlen, bei der Betrachtung von Skalierungsoptionen auf eine höhere SKU hochzuskalieren, anstatt durch Hinzufügen weiterer Instanzen aufzuskalieren.

## <a name="vm-options"></a>VM-Optionen

Die technischen Spezifikationen für die Cluster-VMs von Azure Data Explorer werden in der folgenden Tabelle beschrieben:

|**Name**| **Kategorie** | **SSD-Größe** | **Kerne** | **RAM** | **Storage Premium-Datenträger (1&nbsp;TB)**| **Minimale Anzahl von Instanzen pro Cluster** | **Maximale Anzahl von Instanzen pro Cluster**
|---|---|---|---|---|---|---|---
|D11 v2| Compute-optimiert | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (außer für Dev/Test-SKU, dort liegt sie bei 1)
|D12 v2| Compute-optimiert | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| Compute-optimiert | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1\.000
|D14 v2| Compute-optimiert | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1\.000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| Speicheroptimiert | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1\.000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| Speicheroptimiert | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1\.000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| Speicheroptimiert | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1\.000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| Speicheroptimiert | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1\.000
|L4s v1| Speicheroptimiert | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| Speicheroptimiert | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1\.000
|L16s_1| Speicheroptimiert | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1\.000

* Sie können die aktualisierte VM-SKU-Liste pro Region mithilfe der Azure Data Explorer [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) anzeigen. 
* Weitere Informationen zu den verschiedenen SKUs finden Sie [hier](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Nächste Schritte

* Sie können den Engine-Cluster jederzeit abhängig von unterschiedlichen Anforderungen [hoch- oder herunterskalieren](manage-cluster-vertical-scaling.md), indem Sie die VM-SKU ändern. 

* Sie können die Größe des Engine-Clusters abhängig von unterschiedlichen Anforderungen [ab- oder aufskalieren](manage-cluster-horizontal-scaling.md), um die Kapazität zu ändern.

