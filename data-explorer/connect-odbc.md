---
title: Herstellen einer Verbindung mit Azure Data Explorer mithilfe der ODBC
description: In diesem Artikel erfahren Sie, wie Sie eine ODBC-Verbindung (Open Database Connectivity) mit Azure Data Explorer einrichten.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 156257613195cb53730273ac3a654908feac9601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492726"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Herstellen einer Verbindung mit Azure Data Explorer mithilfe der ODBC

Die Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) ist eine weit verbreitete Anwendungsprogrammierschnittstelle (API) für Datenbankzugriff. Verwenden Sie die ODBC zum Herstellen einer Verbindung mit Azure Data Explorer aus Anwendungen ohne dedizierten Connector.

Im Hintergrund rufen Anwendungen Funktionen in der ODBC-Schnittstelle ab, die in datenbankspezifischen Modulen (*Treibern*) implementiert sind. Azure Data Explorer unterstützt eine Teilmenge des SQL Server-Kommunikationsprotokolls ([MS-TDS](kusto/api/tds/index.md)) und kann daher den ODBC-Treiber für SQL Server verwenden.

Anhand des folgenden Videos lernen Sie, wie Sie eine ODBC-Verbindung erstellen können. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Alternativ können Sie auch [die ODBC-Datenquelle](#configure-the-odbc-data-source) wie unten beschrieben konfigurieren. 

In diesem Artikel erfahren Sie, wie Sie den SQL Server-ODBC-Treiber verwenden, damit Sie aus jeder Anwendung, die die ODBC unterstützt, eine Verbindung mit Azure Data Explorer herstellen können. 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes:

* [Microsoft ODBC Driver for SQL Server, Version 17.2.0.1 oder höher](/sql/connect/odbc/download-odbc-driver-for-sql-server) für Ihr Betriebssystem.

## <a name="configure-the-odbc-data-source"></a>Konfigurieren der ODBC-Datenquelle

Gehen Sie wie folgt vor, um eine ODBC-Datenquelle mithilfe des ODBC-Treibers für SQL Server zu konfigurieren.

1. Suchen Sie in Windows nach *ODBC Data Sources*, und öffnen Sie die ODBC Data Sources-Desktop-App.

1. Wählen Sie **Hinzufügen**.

    ![Hinzufügen einer Datenquelle](media/connect-odbc/add-data-source.png)

1. Wählen Sie **ODBC Driver 17 for SQL Server** und dann **Fertig stellen** aus.

    ![Treiber auswählen](media/connect-odbc/select-driver.png)

1. Geben Sie einen Namen ein sowie eine Beschreibung für die Verbindung und den Cluster, mit dem Sie eine Verbindung herstellen möchten, und klicken Sie dann auf **Weiter**. Die Form der Cluster-URL sollte wie folgt aussehen: *\<ClusterName\>.\<Region\>.kusto.windows.net*.

    ![Server auswählen](media/connect-odbc/select-server.png)

1. Wählen Sie **Active Directory Integrated** aus, und klicken Sie dann auf **Weiter**.

    ![Active Directory Integrated](media/connect-odbc/active-directory-integrated.png)

1. Wählen Sie die Datenbank mit den Beispieldaten aus, und klicken Sie auf **Weiter**.

    ![Standarddatenbank ändern](media/connect-odbc/change-default-database.png)

1. Lassen Sie auf dem nächsten Bildschirm alle Optionen als Standardeinstellungen, und wählen Sie dann **Fertig stellen** aus.

1. Wählen Sie **Datenquelle testen** aus.

    ![Datenquelle testen](media/connect-odbc/test-data-source.png)

1. Stellen Sie sicher, dass der Test erfolgreich war, und klicken Sie dann auf **OK**. Wenn der Test nicht erfolgreich war, überprüfen Sie die Werte, die Sie in vorherigen Schritten angegeben haben, und stellen Sie sicher, dass Sie über ausreichende Berechtigungen für das Herstellen einer Verbindung mit dem Cluster verfügen.

    ![Test erfolgreich](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mit Azure Data Explorer aus Tableau](tableau.md)