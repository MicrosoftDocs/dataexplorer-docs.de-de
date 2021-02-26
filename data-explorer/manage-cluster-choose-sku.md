---
title: Auswählen der passenden Compute-SKU für Ihren Azure Data Explorer-Cluster
description: In diesem Artikel wird beschrieben, wie Sie die optimale Größe der Compute-SKU für den Azure Data Explorer-Cluster auswählen.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 73ba99c71139a38b32b2242d33e9901f3f79df82
ms.sourcegitcommit: ee49cd8186d4aecd5de1ed6d24db6c7b7a079ac4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548951"
---
# <a name="select-the-correct-compute-sku-for-your-azure-data-explorer-cluster"></a>Auswählen der passenden Compute-SKU für Ihren Azure Data Explorer-Cluster 

Wenn Sie einen neuen Cluster erstellen oder einen Cluster für eine veränderliche Workload optimieren, bietet Azure Data Explorer mehrere VM-SKUs zur Auswahl an. Diese Compute-SKUs wurden sorgfältig ausgewählt, damit Sie für jede Workload die bestmögliche Kostenoption wählen können. 

Größe und VM-SKU des Datenverwaltungsclusters werden vollständig vom Azure Data Explorer-Dienst verwaltet. Sie werden durch Faktoren wie die VM-Größe der Engine und die Erfassungsworkload bestimmt. 

Sie können die Compute-SKU für den Engine-Cluster jederzeit ändern, indem Sie den [Cluster hochskalieren](manage-cluster-vertical-scaling.md). Es ist am besten, mit der kleinsten SKU-Größe zu beginnen, die zum Ausgangsszenario passt. Beachten Sie, dass die Skalierung des Clusters zu einer Ausfallzeit von bis zu 30 Minuten führt, während der Cluster mit der neuen SKU neu erstellt wird. Sie können auch die [Azure Advisor-Empfehlungen](azure-advisor.md) verwenden, um Ihre Compute-SKU zu optimieren.

> [!TIP]
> [Reservierte Instanzen (RI) für Compute](/azure/virtual-machines/windows/prepay-reserved-vm-instances) können auch für den Azure Data Explorer-Cluster genutzt werden.  

In diesem Artikel werden verschiedene Compute-SKU-Optionen beschrieben, und er enthält die technischen Details, die Ihnen das Treffen der besten Wahl ermöglichen.

## <a name="select-a-cluster-type"></a>Auswählen des Clustertyps

Azure Data Explorer bietet zwei Arten von Clustern:

* **Produktion:** Produktionscluster enthalten zwei Knoten für Engine und Datenverwaltungscluster und werden unter dem Azure Data Explorer-[SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) betrieben.

* **Dev/Test (kein SLA):** Dev/Test-Cluster verfügen über einen gemeinsamen Knoten für die Engine und den Datenverwaltungscluster. Dieser Clustertyp ist die kostengünstigste Konfiguration aufgrund seiner geringen Anzahl von Instanzen und der fehlenden Aufschlaggebühren für die Engine. Für diese Clusterkonfiguration gibt es kein SLA, da ihr die Redundanz fehlt.

## <a name="compute-sku-types"></a>Compute-SKU-Typen

Der Azure Data Explorer-Cluster unterstützt viele verschiedene SKUs für unterschiedliche Arten von Workloads. Jede SKU verfügt über ein spezielles SSD/CPU-Verhältnis, damit Kunden ihre Bereitstellung richtig dimensionieren und die kostengünstigsten Lösungen für die Analyseaufgaben ihres Unternehmens entwickeln können.

### <a name="compute-optimized"></a>Computeoptimiert

* Verfügt über ein hohes Kern-zu-Cache-Verhältnis.
* Für eine hohe Anzahl von Abfragen bei kleinem oder mittlerem Datenumfang geeignet.
* Lokale SSD zur Erzielung eines niedrigen E/A-Latenzwerts.

### <a name="heavy-compute"></a>Hoher Computeaufwand

* AMD-SKUs mit einem deutlich höheren Kern-zu-Cache-Verhältnis.
* Lokale SSD zur Erzielung eines niedrigen E/A-Latenzwerts.

### <a name="storage-optimized"></a>Speicheroptimiert

* Option für größeren Speicherplatz im Bereich von 1 TB bis 4 TB pro Engine-Knoten.
* Für Workloads geeignet, bei denen große Datenmengen gespeichert werden müssen und die Computeanforderungen für Abfragen weniger hoch sind.
* Für bestimmte SKUs wird anstelle von lokalen SSDs für die Speicherung von heißen Daten Premiumspeicher (verwalteter Datenträger) verwendet, der an den Engine-Knoten angefügt ist.

### <a name="isolated-compute"></a>Isolierte Computeressource

Ideale SKU zum Ausführen von Workloads, für die die Isolation auf Serverinstanzebene erforderlich ist.

## <a name="select-and-optimize-your-compute-sku"></a>Auswählen und Optimieren Ihrer Compute-SKU 

### <a name="select-your-compute-sku-during-cluster-creation"></a>Auswählen Ihrer Compute-SKU während der Clustererstellung

Wählen Sie beim Erstellen eines Azure Data Explorer-Clusters die *optimale* VM-SKU für die geplante Workload.

Die folgenden Attribute können bei der Wahl der SKU ebenfalls hilfreich sein:
 
| attribute | Details |
|---|---
|**Verfügbarkeit**| Nicht alle SKUs sind in allen Regionen verfügbar. |
|**Kosten pro GB-Cache pro Kern**| Hohe Kosten bei Optimierung für normalen und hohen Computeaufwand. Niedrige Kosten bei SKUs mit Speicheroptimierung. |
|**Preise für reservierte Instanzen (RI)**| RI-Rabatt variiert nach Region und SKU |  

> [!NOTE]
> Für den Azure Data Explorer-Cluster sind die Computekosten im Vergleich mit den Kosten für Speicher und Netzwerk der signifikanteste Teil der Gesamtkosten des Clusters.

### <a name="optimize-your-cluster-compute-sku"></a>Optimieren Ihrer Compute-SKU des Clusters

[Konfigurieren Sie die vertikale Skalierung](manage-cluster-vertical-scaling.md#configure-vertical-scaling), und sehen Sie sich die [Azure Advisor-Empfehlungen](azure-advisor.md) an, um Ihre Compute-SKU für den Cluster zu optimieren. 

Mit den verschiedenen verfügbaren Compute-SKU-Optionen können Sie die Kosten für die Leistung und die Hot-Cache-Anforderungen Ihres Szenarios optimieren. 
* Wenn Sie die optimale Leistung bei hohem Abfragevolumen benötigen, sollte die ideale SKU für Compute optimiert werden. 
* Wenn Sie große Datenmengen mit relativ geringerer Abfragelast benötigen, kann die speicheroptimierte SKU helfen, die Kosten zu senken, und bietet dennoch eine ausgezeichnete Leistung.

Da die Anzahl der Instanzen pro Cluster für die kleinen SKUs begrenzt ist, ist es besser, größere VMs mit mehr RAM zu verwenden. Mehr RAM wird für einige Abfragetypen benötigt, die die RAM-Ressource stärker beanspruchen, z. B. für Abfragen, die `joins` verwenden. Daher empfehlen wir Ihnen, bei der Auswahl von Skalierungsoptionen auf eine höhere SKU hochzuskalieren, anstatt durch Hinzufügen weiterer Instanzen aufzuskalieren.

## <a name="compute-sku-options"></a>Compute-SKU-Optionen

Die technischen Spezifikationen für die Cluster-VMs von Azure Data Explorer werden in der folgenden Tabelle beschrieben:

|**Name**| **Kategorie** | **SSD-Größe** | **Kerne** | **RAM** | **Storage Premium-Datenträger (1&nbsp;TB)**| **Minimale Anzahl von Instanzen pro Cluster** | **Maximale Anzahl von Instanzen pro Cluster**
|---|---|---|---|---|---|---|---
|Dev (keine SLA) Standard_D11_v2| Compute-optimiert | 80&nbsp;GB    | 1 | 14&nbsp;GB | 0 | 1 | 1
|Dev (keine SLA) Standard_E2a_v4| Compute-optimiert | 24&nbsp;GB    | 1 | 16&nbsp;GB | 0 | 1 | 1
|Standard_D11_v2| Compute-optimiert | 80&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 2 | 8 
|Standard_D12_v2| Compute-optimiert | 160&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|Standard_D13_v2| Compute-optimiert | 317&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1\.000
|Standard_D14_v2| Compute-optimiert | 628&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1\.000
|Standard_E2a_v4| Hoher Computeaufwand | 24&nbsp;GB    | 2 | 16&nbsp;GB | 0 | 2 | 8 
|Standard_E4a_v4| Hoher Computeaufwand | 60&nbsp;GB   | 4 | 32&nbsp;GB | 0 | 2 | 16
|Standard_E8a_v4| Hoher Computeaufwand | 137&nbsp;GB   | 8 | 64&nbsp;GB | 0 | 2 | 1\.000
|Standard_E16a_v4| Hoher Computeaufwand | 273&nbsp;GB   | 16| 128&nbsp;GB | 0 | 2 | 1\.000
|Standard_DS13_v2 + 1&nbsp;TB&nbsp;PS| Speicheroptimiert | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1\.000
|Standard_DS13_v2 + 2&nbsp;TB&nbsp;PS| Speicheroptimiert | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1\.000
|Standard_DS14_v2 + 3&nbsp;TB&nbsp;PS| Speicheroptimiert | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1\.000
|Standard_DS14_v2 + 4&nbsp;TB&nbsp;PS| Speicheroptimiert | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1\.000
|Standard_E8as_v4 + 1&nbsp;TB&nbsp;PS| Speicheroptimiert | 1&nbsp;TB | 8 | 64&nbsp;GB | 1 | 2 | 1\.000
|Standard_E8as_v4 + 2&nbsp;TB&nbsp;PS| Speicheroptimiert | 2&nbsp;TB | 8 | 64&nbsp;GB | 2 | 2 | 1\.000
|Standard_E16as_v4 + 3&nbsp;TB&nbsp;PS| Speicheroptimiert | 3&nbsp;TB | 16 | 128&nbsp;GB | 3 | 2 | 1\.000
|Standard_E16as_v4 + 4&nbsp;TB&nbsp;PS| Speicheroptimiert | 4&nbsp;TB | 16 | 128&nbsp;GB | 4 | 2 | 1\.000
|Standard_L4s| Speicheroptimiert | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|Standard_L8s| Speicheroptimiert | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1\.000
|Standard_L16s| Speicheroptimiert | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1\.000
|Standard_L8s_v2| Speicheroptimiert | 1,7&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1\.000
|Standard_L16s_v2| Speicheroptimiert | 3,5&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1\.000
|Standard_E64i_v3| Isolierte Computeressource | 1,1&nbsp;TB | 64 | 432&nbsp;GB | 0 | 2 | 1\.000
|Standard_E80ids_v4| Isolierte Computeressource | 1,8&nbsp;TB | 80 | 504&nbsp;GB | 0 | 2 | 1\.000

* Sie können die aktualisierte Liste mit den Compute-SKUs pro Region mit der [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus) von Azure Data Explorer anzeigen. 
* Weitere Informationen zu den verschiedenen SKUs finden Sie [hier](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Nächste Schritte

* Sie können den Engine-Cluster jederzeit abhängig von unterschiedlichen Anforderungen [hoch- oder herunterskalieren](manage-cluster-vertical-scaling.md), indem Sie die VM-SKU ändern. 

* Sie können die Größe des Engine-Clusters abhängig von unterschiedlichen Anforderungen [ab- oder aufskalieren](manage-cluster-horizontal-scaling.md), um die Kapazität zu ändern.

* Verwenden Sie die [Azure Advisor-Empfehlungen](azure-advisor.md), um Ihre Compute-SKU zu optimieren.
