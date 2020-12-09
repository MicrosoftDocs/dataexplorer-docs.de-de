---
title: Fehler beim Erstellen oder Löschen einer Datenbank oder Tabelle in Azure Data Explorer
description: Dieser Artikel enthält Schritte zum Beheben von Problemen, die beim Erstellen und Löschen von Datenbanken und Tabellen im Azure-Daten-Explorer auftreten können.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/01/2020
ms.openlocfilehash: 8b94a36bc2e5190770db424126a3461330c4245d
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444181"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Problembehandlung: Fehler beim Erstellen oder Löschen einer Datenbank oder Tabelle im Azure-Daten-Explorer

Im Azure-Daten-Explorer arbeiten Sie regelmäßig mit Datenbanken und Tabellen. Dieser Artikel enthält Schritte zur Behandlung von Problemen, die möglicherweise auftreten. 

## <a name="creating-a-database"></a>Erstellen einer Datenbank

1. Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Um eine Datenbank erstellen zu können, müssen Sie Mitglied der Rolle *Mitwirkender* oder *Besitzer* für das Azure-Abonnement sein. Arbeiten Sie ggf. mit Ihrem Abonnementadministrator zusammen, damit dieser Sie der entsprechenden Rolle hinzufügen kann.

1. Stellen Sie sicher, dass für den Datenbanknamen keine Namensüberprüfungsfehler vorliegen. Der Name muss aus maximal 260 alphanumerischen Zeichen bestehen.

1. Vergewissern Sie sich, dass die Werte für Datenbankaufbewahrung und -caching im zulässigen Bereich liegen. Die Aufbewahrungsdauer muss zwischen 1 und 36.500 Tagen (100 Jahre) betragen. Für das Caching muss ein Wert zwischen 1 und dem maximalen Wert für die Aufbewahrungsdauer festgelegt werden.

## <a name="deleting-or-renaming-a-database"></a>Löschen oder Umbenennen einer Datenbank

Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Um eine Datenbank löschen oder umbenennen zu können, müssen Sie Mitglied der Rolle *Mitwirkender* oder *Besitzer* für das Azure-Abonnement sein. Arbeiten Sie ggf. mit Ihrem Abonnementadministrator zusammen, damit dieser Sie der entsprechenden Rolle hinzufügen kann. Sie können eine Datenbank auch löschen oder umbenennen, wenn Sie über die Rolle *Datenbankadministrator* verfügen.

## <a name="creating-a-table"></a>Erstellen einer Tabelle

1. Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Zum Erstellen einer Tabelle müssen Sie Mitglied der Rolle *database admin* (Datenbankadministrator) oder *Datenbank-Benutzer* in der Datenbank bzw. Mitglied der Rolle *Mitwirkender* oder *Besitzer* für das Azure-Abonnement sein. Arbeiten Sie ggf. mit Ihrem Abonnement- oder Clusteradministrator zusammen, damit dieser Sie zur entsprechenden Rolle hinzufügen kann.

    Informationen zu Berechtigungen finden Sie unter [Verwalten von Datenbankberechtigungen](manage-database-permissions.md).

1. Stellen Sie sicher, dass keine Tabelle mit demselben Namen vorhanden ist. Ist bereits eine vorhanden, haben Sie folgende Möglichkeiten: Erstellen Sie eine Tabelle mit einem anderen Namen, benennen Sie die vorhandene Tabelle um (erfordert die Rolle *table admin* (Tabellenadministrator)), oder löschen Sie die vorhandene Tabelle (erfordert die Rolle *database admin* (Datenbankadministrator)). Verwenden Sie die folgenden Befehle:

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Löschen oder Umbenennen einer Tabelle

Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Zum Löschen oder Umbenennen einer Tabelle müssen Sie Mitglied der Rolle *database admin* (Datenbankadministrator) oder *table admin* (Tabellenadministrator) in der Datenbank sein. Arbeiten Sie ggf. mit Ihrem Abonnement- oder Clusteradministrator zusammen, damit dieser Sie zur entsprechenden Rolle hinzufügen kann.

Informationen zu Berechtigungen finden Sie unter [Verwalten von Datenbankberechtigungen](manage-database-permissions.md).

## <a name="general-guidance"></a>Allgemeine Hinweise

1. Sehen Sie sich das [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) an. Suchen Sie nach dem Status des Azure-Daten-Explorers in der Region, in der Sie eine Datenbank oder Tabelle verwenden möchten.

    Wenn der Status nicht **Gut** lautet (grünes Häkchen), wiederholen Sie den Vorgang, wenn sich der Status verbessert hat.

1. Falls Sie weitere Unterstützung bei der Lösung Ihres Problems benötigen, können Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eine Supportanfrage erstellen.

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen der Clusterintegrität](check-cluster-health.md)
* [Verwalten von Datenbankberechtigungen](manage-database-permissions.md)
