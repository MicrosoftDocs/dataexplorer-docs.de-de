---
title: Verwenden der Datenbereinigung zum Löschen personenbezogener Daten von Geräten oder aus Diensten in Azure Data Explorer
description: Hier erfahren Sie, wie Sie mithilfe der Datenbereinigung Daten in Azure Data Explorer bereinigen (löschen).
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/12/2020
ms.openlocfilehash: 6bf447a845954bde58a0308a03bee45f98f16111
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873232"
---
# <a name="enable-data-purge-on-your-azure-data-explorer-cluster"></a>Aktivieren der Datenbereinigung in Ihrem Azure Data Explorer-Cluster

[!INCLUDE [gdpr-intro-sentence](includes/gdpr-intro-sentence.md)]

Azure Data Explorer unterstützt die Möglichkeit zum Löschen einzelner Datensätze. Beim Löschen von Daten mithilfe des Befehls `.purge` werden personenbezogene Daten geschützt. Dieses Verfahren sollte daher nicht in anderen Szenarien verwendet werden. Es ist nicht dafür konzipiert, häufige Löschanforderungen oder das Löschen großer Datenmengen zu unterstützen, und wirkt sich unter Umständen erheblich auf die Leistung des Diensts aus.

Durch das Ausführen eines Befehls vom Typ `.purge` wird ein Prozess ausgelöst, deren Ausführung einige Tage dauern kann. Besitzen die Datensätze, auf die `predicate` angewendet wird, eine hohe „Dichte“, werden bei diesem Prozess alle Daten in der Tabelle erneut erfasst. Dieser Prozess hat erhebliche Auswirkungen auf Leistung und Umsatzkosten. Weitere Informationen finden Sie unter [Datenbereinigung](kusto/concepts/data-purge.md).

## <a name="methods-of-invoking-purge-operations"></a>Methoden zum Aufrufen von Bereinigungsvorgängen 

Azure Data Explorer (Kusto) unterstützt sowohl die Löschung einzelner Datensätze als auch die Bereinigung ganzer Tabellen. Der Befehl `.purge` kann je nach Nutzungsszenario [auf zwei Arten aufgerufen werden](kusto/concepts/data-purge.md#purge-table-tablename-records-command):

* Programmgesteuerter Aufruf: Ein einzelner Schritt, der von Anwendungen aufgerufen werden soll. Durch den direkten Aufruf dieses Befehls wird die Bereinigungsausführungssequenz ausgelöst.

* Aufruf durch eine Person: Ein zweistufiger Prozess, für den als separater Schritt eine explizite Bestätigung erforderlich ist. Durch den Aufruf des Befehls wird ein Überprüfungstoken zurückgegeben, das zum Ausführen der eigentlichen Bereinigung bereitgestellt werden muss. Dies dient dazu, das versehentliche Löschen falscher Daten zu vermeiden Die Ausführung dieser Option kann für umfangreiche Tabellen mit einer großen Menge an Daten im „kalten“ Cache lange dauern. 

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Melden Sie sich bei der [Webbenutzeroberfläche](https://dataexplorer.azure.com/) an.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).

## <a name="enable-data-purge-on-your-cluster"></a>Aktivieren der Datenbereinigung für Ihren Cluster

> [!WARNING]
> * Für das Aktivieren der Datenbereinigung ist ein Dienstneustart erforderlich, der zum Verwerfen der Abfrage führen kann.
> * Überprüfen Sie vor dem Aktivieren der Datenbereinigung die [Einschränkungen](#limitations).

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster. 
1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus. 
1. Wählen Sie im Bereich **Konfigurationen** die Option **Ein** aus, um die Option **Löschen aktivieren** zu aktivieren.
1. Wählen Sie **Speichern** aus.
 
    ![Option „Löschen aktivieren“ aktiviert](media/data-purge-portal/enable-purge-on.png)

## <a name="disable-data-purge-on-your-cluster"></a>Deaktivieren der Datenbereinigung für Ihren Cluster

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster. 
1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus. 
1. Wählen Sie im Bereich **Konfigurationen** die Option **Aus** aus, um die Option **Löschen aktivieren** zu deaktivieren.
1. Wählen Sie **Speichern** aus.

    ![Option „Löschen aktivieren“ deaktiviert](media/data-purge-portal/enable-purge-off.png)

## <a name="limitations"></a>Einschränkungen

* Der Bereinigungsvorgang ist endgültig und kann nicht rückgängig gemacht werden. Es ist nicht möglich, diesen Prozess rückgängig zu machen oder bereinigte Daten wiederherzustellen. Daher können mit Befehlen wie [undo table drop](kusto/management/undo-drop-table-command.md) bereinigte Daten nicht wiederhergestellt werden. Zudem kann bei der Zurücksetzung von Daten auf eine vorherige Version kein Zeitpunkt gewählt werden, der vor der letzten Bereinigung liegt.
* Der Befehl `.purge` wird für den Datenverwaltungsendpunkt *https://ingest- [IhrClustername].[Region].kusto.windows.net* ausgeführt. Der Befehl erfordert Berechtigungen vom Typ [Datenbankadministrator](kusto/management/access-control/role-based-authorization.md) für die relevanten Datenbanken. 
* In Anbetracht der Leistungsbeeinträchtigung durch den Bereinigungsvorgang sollte der Aufrufer das Datenschema so ändern, dass möglichst wenige Tabellen relevante Daten enthalten. Außerdem sollten die Befehle pro Tabelle zu einem Batch zusammengefasst werden, um die erheblichen Auswirkungen des Bereinigungsvorgangs auf die Umsatzkosten zu reduzieren.
* Mit dem Parameter `predicate` des Bereinigungsbefehls wird angegeben, welche Datensätze gelöscht werden sollen. Die Größe von `Predicate` ist auf 63 KB beschränkt. 

## <a name="next-steps"></a>Nächste Schritte

* [Datenbereinigung in Azure Data Explorer](kusto/concepts/data-purge.md)
