---
title: 'Problembehandlung: Fehler beim Erstellen eines Azure Data Explorer-Clusters'
description: Dieser Artikel beschreibt die Schritte zur Problembehandlung beim Erstellen eines Clusters im Azure-Daten-Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494102"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Problembehandlung: Fehlerhafte Clustererstellung von Azure Data Explorer

Im unwahrscheinlichen Fall, dass bei der Clustererstellung im Azure-Daten-Explorer ein Fehler auftritt, können Sie die hier angegebenen Schritte ausführen.

1. Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Um einen Cluster erstellen zu können, müssen Sie Mitglied der Rolle *Mitwirkender* oder *Besitzer* für das Azure-Abonnement sein. Arbeiten Sie ggf. mit Ihrem Abonnementadministrator zusammen, damit dieser Sie der entsprechenden Rolle hinzufügen kann.

1. Stellen Sie sicher, dass keine Überprüfungsfehler für den Clusternamen vorliegen, den Sie im Azure-Portal unter **Cluster erstellen** eingegeben haben.

1. Sehen Sie sich das [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) an. Suchen Sie nach dem Status von Azure-Daten-Explorer in der Region, in der Sie versuchen, den Cluster zu erstellen.

    Wenn der Status nicht **Gut** lautet (grünes Häkchen), können Sie versuchen, den Cluster zu erstellen, wenn sich der Status verbessert hat.

1. Falls Sie weitere Unterstützung bei der Lösung Ihres Problems benötigen, können Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eine Supportanfrage erstellen.
