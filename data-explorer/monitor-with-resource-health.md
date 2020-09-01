---
title: Überwachen von Azure Data Explorer mithilfe von Resource Health
description: Verwenden Sie Azure Resource Health zum Überwachen von Azure Data Explorer-Ressourcen.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/31/2020
ms.openlocfilehash: 5de30b4e6eb2ac9d01314482b693f6643075ca4a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872742"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Überwachen von Azure Data Explorer mithilfe von Resource Health (Vorschau)

[Resource Health](/azure/service-health/resource-health-overview) für Azure Data Explorer informiert Sie über die Integrität Ihrer Azure Data Explorer-Ressource und bietet Handlungsempfehlungen, um Probleme zu beheben. Resource Health wird alle 1 bis 2 Minuten aktualisiert und meldet die aktuelle und die frühere Integrität ihrer Ressourcen. 

Resource Health ermittelt die Integrität ihrer Azure Data Explorer-Ressource durch Untersuchen verschiedener Integritätsstatusprüfungen wie beispielsweise:
* Ressourcenverfügbarkeit
* Abfragefehler

## <a name="access-resource-health-reporting"></a>Zugriff auf Resource Health-Berichte

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **Überwachen** aus der Liste der Dienste aus.
1. Wählen Sie **Dienstintegrität** > **Ressourcenintegrität** aus.
1. Wählen Sie in der Dropdownliste **Abonnement** Ihr Abonnement aus. Wählen Sie in der Dropdownliste **Ressourcentyp** die Option **Azure Data Explorer** aus.
1. In der sich ergebenden Tabelle werden alle Ressourcen im ausgewählten Abonnement aufgelistet. Jede Ressource verfügt über ein Integritätszustandssymbol, das die Integrität der Ressourcen angibt.
1. Wählen Sie Ihre Ressource aus, um ihren [Ressourcenintegritätsstatus](#resource-health-status) und Empfehlungen anzuzeigen.

    ![Übersicht](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Ressourcenintegritätsstatus

Die Integrität einer Ressource wird mit einem der folgenden Status angezeigt: „Verfügbar“, „Nicht verfügbar“ und „Unbekannt“.

### <a name="available"></a>Verfügbar

Der Integritätsstatus **Verfügbar** gibt an, dass Ihre Azure Data Explorer-Ressource fehlerfrei ist und keine Probleme aufweist.

![Verfügbar](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Nicht verfügbar

Der Integritätsstatus **Nicht verfügbar** gibt an, dass ein Problem mit Ihrer Azure Data Explorer-Ressource vorliegt, das dazu geführt hat, dass sie für Abfragen und Erfassung nicht verfügbar ist. Beispielsweise kann es sein, dass Knoten in Ihrer Azure Data Explorer-Ressource unerwartet neu gestartet wurden. Falls sich Ihre Azure Data Explorer-Ressource länger in diesem Zustand befindet, wenden Sie sich an den [Support]().

![Nicht verfügbar](media/monitor-with-resource-health/unavailable.png)

> [!TIP]
> Die Problemursache kann mithilfe der [Systeminformationsbefehle](kusto/management/systeminfo.md) ermittelt werden.

### <a name="unknown"></a>Unknown

Der Integritätsstatus **Unbekannt** gibt an, dass **Resource Health** seit mehr als 10 Minuten keine Informationen mehr zu dieser Azure Data Explorer-Ressource empfangen hat. Dieser Status ist zwar keine definitive Angabe des Azure Data Explorer-Ressourcenzustands, aber ein wichtiger Datenpunkt im Problembehandlungsprozess. Wenn Ihr Azure Data Explorer-Cluster erwartungsgemäß funktioniert, ändert sich der Status innerhalb weniger Minuten in **Verfügbar**. Der Integritätsstatus **Unbekannt** kann darauf hindeuten, dass ein Ereignis auf der Plattform die Ressource beeinträchtigt. 

> [!TIP]
> Die Integrität der Azure Data Explorer-Clusterressource ist **Unbekannt**, wenn sie sich im Zustand „Beendet“ befindet.

![Unknown](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Verlaufsinformationen

Im Bereich **Resource Health** unter **Integritätsverlauf** können Sie auf Informationen zum Ressourcenintegritätsstatus aus bis zu vier Wochen zugreifen. Wählen Sie den Pfeil aus, um weitere Informationen zu den in diesem Bereich gemeldeten Integritätsereignisproblemen zu erhalten. 

![Verlauf](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von Resource Health-Warnungen](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](ingest-data-no-code.md)
* [Überwachen der Azure Data Explorer-Leistung, -Integrität und -Auslastung mit Metriken](using-metrics.md)
