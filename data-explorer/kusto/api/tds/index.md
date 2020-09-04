---
title: MS-TDS (T-SQL-Unterstützung) – Azure Data Explorer
description: In diesem Artikel wird MS-TDS (T-SQL-Unterstützung) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: cc10fcc725e038d6428d4b794a1f6d368a86a39e
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428412"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS (T-SQL-Unterstützung)

Azure Data Explorer unterstützt einen Teil des Microsoft SQL Server-Kommunikationsprotokolls (MS-TDS) mit einem Teil der T-SQL-Abfragesprache. Microsoft Excel und Microsoft Power BI sind nur einige der zahlreichen Tools, die mit Azure Data Explorer verwendet werden können. Mit diesen Microsoft-Anwendungen kann auch SQL Server abgefragt werden.

> [!NOTE]
> Verwenden Sie die integrierte Azure Active Directory-Authentifizierung (Azure AD) als Clienttool, damit eine Kusto-Abfrage über MS-TDS durchgeführt werden kann.

## <a name="next-steps"></a>Nächste Schritte

* Unter [T-SQL](./t-sql.md) finden Sie Informationen zur Implementierung der T-SQL-Abfragesprache durch Kusto. 

* Unter [KQL über TDS](./tdskql.md) finden Sie Informationen zum Ausführen nativer KQL-Abfragen über TDS-Endpunkte.

* Informationen zur Verwendung von Azure Data Explorer über bekannte Clients, die MS-TDS/T-SQL nutzen, finden Sie unter [MS-TDS-Clients und Kusto](./clients.md).

* Unter [Azure Data Explorer als mit SQL Server verknüpfter Server](./linkedserver.md) erfahren Sie, wie Sie den Cluster als Server konfigurieren, der mit der lokalen SQL Server-Instanz verknüpft ist. 

* Unter [MS-TDS mit Azure Active Directory](./aad.md) finden Sie Informationen zur Verwendung von Azure AD über TDS für das Herstellen einer Verbindung mit Azure Data Explorer.

* Unter [Bekannte Probleme mit SQL](./sqlknownissues.md) finden Sie Informationen zu den Hauptunterschieden zwischen der T-SQL- und Azure Data Explorer-Implementierung von SQL Server.
