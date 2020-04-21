---
title: Kusto als verknüpfter Server vom SQL-Server - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto als verknüpfter Server vom SQL-Server in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 77db975f2bd7c5c1d747902248070664cd020e39
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523459"
---
# <a name="kusto-as-linked-server-from-sql-server"></a>Kusto als verknüpfter Server vom SQL-Server

Der lokale SQL-Server ermöglicht das Anfügen eines verknüpften Servers. Mit dieser Funktion können Abfragen erstellt werden, die Daten von SQL-Servern und verknüpften Servern verknüpfen.

Es ist möglich, Kusto als verknüpften Server über ODBC-Konnektivität zu verwenden.

1. Der SQL Server-Dienst (lokal) muss ein Active Directory-Konto (nicht das Standarddienstkonto) verwenden, das die Verbindung mit Kusto über AAD ermöglicht.
2. Installieren Sie den neuesten ODBC-Treiber für SQL Server 2017 (er kommt auch mit SSMS 18):https://www.microsoft.com/download/details.aspx?id=56567
3. Bereiten Sie die DSN-Verbindungszeichenfolge für den ODBC-Treiber `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`für einen bestimmten Kusto-Cluster und eine bestimmte Datenbank vor: . Die Sprachoption wird hinzugefügt, um Kusto so zu stimmen, dass Zeichenfolgen als NVARCHAR(4000) codiert werden. Weitere Informationen zu dieser Problemumgehung finden Sie unter [ODBC](./clients.md#odbc).
4. Erstellen Sie den linked Server mit den folgenden 3 definierten Einstellungen: ![Alt-Text](../images/linkedserverconnection.png "Verknüpfte Serververbindung")
5. Die Registerkarte Sicherheit muss mit dieser Einstellung definiert werden: ![alt text](../images/linkedserverlogin.png "Verknüpfte Serveranmeldung")

Jetzt ist es möglich, Daten aus Kusto wie folgt abzufragen:
```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

Notizen:
1. Es wird empfohlen, Kusto [gespeicherte Funktionen](../../query/schema-entities/stored-functions.md) zum Extrahieren von Daten aus Kusto zu verwenden. Die gespeicherte Funktion kann die gesamte Logik enthalten, die für eine effiziente Abfrage von Kusto erforderlich ist, die in der muttersprachlichen [KQL-Sprache](../../query/index.md) erstellt und durch bestimmte Parameterwerte gesteuert wird. Die T-SQL-Syntax zum Aufrufen der gespeicherten Kusto-Funktion ist identisch mit dem Aufrufen der SQL-Tabellarischen Funktion.
2. SQL Server unterstützt nicht das Aufrufen von Remote-Tabellarischen Funktionen von verknüpften Servern innerhalb seiner eigenen Abfragen. Die Problemumgehung für diese `OpenQuery` Einschränkung besteht darin, zum Ausführen von Remoteabfragen auf dem verknüpften Server zu verwenden. Auf diese Weise wird die Tabellarische Funktion nicht auf SQL-Server direkt aufgerufen, sondern in Abfragen, die auf dem verknüpften Server ausgeführt werden. Die äußere T-SQL-Abfrage kann verwendet werden, um zwischen Daten auf dem `OpenQuery`SQL-Server und Daten, die von der gespeicherten Kusto-Funktion über zurückgegeben werden, zu verbinden.