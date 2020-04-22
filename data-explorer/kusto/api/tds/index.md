---
title: MS-TDS (T-SQL-Unterstützung) – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird MS-TDS (T-SQL-Unterstützung) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 8aaea26b6c4e8a7f76c4129faeb681791f25ae7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490547"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS (T-SQL-Unterstützung)

Kusto unterstützt einen Teil des Microsoft SQL Server-Kommunikationsprotokolls (MS-TDS) mit einem Teil der T-SQL-Abfragesprache, sodass bereits vorhandene Tools, die Abfragen für SQL Server durchführen können, mit Kusto verwendet werden können. Zu den zahlreichen unterstützten Clients zählen unter anderem Microsoft Excel und Microsoft Power BI.

Hinweis: Damit ein Clienttool eine Kusto-Abfrage über MS-TDS durchführen kann, muss der Client die integrierte Azure Active Directory-Authentifizierung verwenden.

Weitere Informationen zur Implementierung der T-SQL-Abfragesprache durch Kusto finden Sie unter [T-SQL](./t-sql.md). 

Unter [MS-TDS clients and Kusto](./clients.md) (MS-TDS-Clients und Kusto) finden Sie Kusto-Verwendungsbeispiele mit MS-TDS/T-SQL für einige bekannte Clients.

Unter [Kusto as linked server from SQL server](./linkedserver.md) (Kusto als mit SQL Server verknüpfter Server) erfahren Sie, wie Sie einen Kusto-Cluster als Server konfigurieren, der mit einer lokalen SQL Server-Instanz verknüpft ist.

Unter [MS-TDS with Azure Active Directory](./aad.md) (MS-TDS mit Azure Active Directory) erfahren Sie ausführlicher, wie Sie AAD über TDS verwenden, um eine Verbindung mit Kusto herzustellen.

Unter [KQL over TDS](./tdskql.md) (KQL über TDS) finden Sie Informationen zum Ausführen nativer KQL-Abfragen über den TDS-Endpunkt. 

[Hier](./sqlknownissues.md) finden Sie einige der Hauptunterschiede zwischen der T-SQL-Implementierung von SQL Server und Kusto.