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
ms.openlocfilehash: a128db995c78c0583bc7c7712c06292a2f6598d1
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550536"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS (T-SQL-Unterstützung)

Azure Data Explorer (Kusto) unterstützt einen Teil des Microsoft SQL Server-Kommunikationsprotokolls (MS-TDS) mit einem Teil der T-SQL-Abfragesprache. Microsoft Excel und Microsoft Power BI sind nur einige der zahlreichen Tools, die mit Azure Data Explorer (Kusto) verwendet werden können. Mit diesen Microsoft-Anwendungen kann auch SQL Server abgefragt werden.

> [!NOTE]
> Verwenden Sie die integrierte Azure Active Directory-Authentifizierung (Azure AD) als Clienttool, damit eine Kusto-Abfrage über MS-TDS durchgeführt werden kann.

## <a name="next-steps"></a>Nächste Schritte

* Unter [T-SQL](./t-sql.md) finden Sie Informationen zur Implementierung der T-SQL-Abfragesprache durch Kusto. 

* Unter [KQL über TDS](./tdskql.md) finden Sie Informationen zum Ausführen nativer KQL-Abfragen über TDS-Endpunkte.

* Informationen zur Verwendung von Azure Data Explorer über bekannte Clients, die MS-TDS/T-SQL nutzen, finden Sie unter [MS-TDS-Clients und Kusto](./clients.md).

* Unter [Azure Data Explorer (Kusto) als mit SQL Server verknüpfter Server](./linkedserver.md) erfahren Sie, wie Sie den Cluster als Server konfigurieren, der mit der lokalen SQL Server-Instanz verknüpft ist. 

* Unter [MS-TDS mit Azure Active Directory](./aad.md) finden Sie Informationen zur Verwendung von Azure AD über TDS für das Herstellen einer Verbindung mit Azure Data Explorer.

* Unter [Bekannte Probleme mit SQL](./sqlknownissues.md) finden Sie Informationen zu den Hauptunterschieden zwischen der T-SQL- und Azure Data Explorer-Implementierung von SQL Server.
