---
title: Kusto als Verbindungs Server von SQL Server-Azure Daten-Explorer
description: In diesem Artikel wird Kusto als Verbindungs Server von SQL Server in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 103d39f7fdd10f0375e4a530d51cd17f7cdcec51
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799593"
---
# <a name="kusto-as-a-linked-server-from-the-sql-server"></a>Kusto als Verbindungs Server von SQL Server

Lokale SQL Server-Instanz ermöglicht das Anfügen eines Verbindungs Servers und ermöglicht das Erstellen von Abfragen, die Daten aus dem SQL-Server und aus Verbindungs Servern verknüpfen.

Sie können Kusto als Verbindungs Server über ODBC-Konnektivität verwenden.
Der SQL Server lokale Dienst muss ein Active Directory-Konto (nicht das Standard Dienst Konto) verwenden, mit dem die Verbindung mit Azure Daten-Explorer mithilfe von Azure Active Directory (Azure AD) hergestellt werden kann.

1. Installieren Sie den neuesten ODBC-Treiber für SQL Server 2017 (er ist auch in SSMS 18 verfügbar):https://www.microsoft.com/download/details.aspx?id=56567
1. Bereiten Sie die DSN-Less-Verbindungs Zeichenfolge für den ODBC-Treiber für einen bestimmten Azure Daten-Explorer `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`-Cluster und eine bestimmte Datenbank vor:. Die Sprachoption wird zum Optimieren von Azure Daten-Explorer hinzugefügt, um Zeichen folgen als nvarchar (4000) zu codieren. Weitere Informationen zu dieser Problem Umgehung finden Sie unter [ODBC](./clients.md#odbc).
1. Erstellen Sie den Verbindungs Server mit den Einstellungen, auf die mit den roten Pfeilen verwiesen wird.

:::image type="content" source="../images/linkedserverconnection.png" alt-text="Verbindung mit Verbindungs Server":::

1. Definieren Sie die Sicherheit mit der Einstellung, auf die durch den roten Pfeil gezeigt wird. 

:::image type="content" source="../images/linkedserverlogin.png" alt-text="Anmelde Name des Verbindungs Servers":::

So Fragen Sie Daten aus Kusto ab:

```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

> [!NOTE]
> 1. Verwenden Sie gespeicherte Kusto- [Funktionen](../../query/schema-entities/stored-functions.md) zum Extrahieren von Daten aus Azure Daten-Explorer. Die gespeicherte Funktion kann die gesamte erforderliche Logik für effiziente Abfragen von Kusto enthalten, die in nativer [kql](../../query/index.md) -Sprache erstellt und durch angegebene Parameterwerte gesteuert werden. Die T-SQL-Syntax zum Aufrufen der gespeicherten Kusto-Funktion ist mit dem Aufruf der tabellarischen SQL-Funktion identisch.
> 1. Der SQL Server unterstützt nicht das Aufrufen von Remote Tabellen Funktionen von verknüpften Servern innerhalb der eigenen Abfragen. Die Problem Umgehung für diese Einschränkung ist die `OpenQuery` Verwendung von zum Ausführen von Remote Abfragen auf dem Verbindungs Server. Auf diese Weise wird die tabellarische Funktion nicht im SQL Server-Verzeichnis, sondern in einer Abfrage aufgerufen, die auf dem Verbindungs Server ausgeführt wird. Die äußere T-SQL-Abfrage kann verwendet werden, um zwischen den Daten auf dem SQL-Server und aus den Daten, die von `OpenQuery`der gespeicherten Kusto-Funktion über zurückgegeben werden,
