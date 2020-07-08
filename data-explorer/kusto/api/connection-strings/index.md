---
title: Verbindungszeichenfolgen – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden Verbindungszeichenfolgen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 9cea718db64c3da998df8b832d886ebd87d0f241
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058761"
---
# <a name="connection-strings"></a>Verbindungszeichenfolgen

Verbindungszeichenfolgen werden viel in Kusto-Steuerungsbefehlen, in der Kusto-API und gelegentlich auch in Abfragen verwendet.
Mithilfe von Verbindungszeichenfolgen lässt sich allgemein beschreiben, wo sich Ressourcen außerhalb von Kusto befinden und wie mit ihnen interagiert werden kann. Zu solchen Ressourcen zählen beispielsweise Blobs im Azure Blob Storage-Dienst sowie Azure SQL-Datenbanken.

Verbindungszeichenfolgen können unterschiedliche Formate haben:

* [Kusto-Verbindungszeichenfolgen](./kusto.md) beschreiben die Kommunikation mit einem Kusto-Dienstendpunkt.
  Kusto-Verbindungszeichenfolgen basieren auf dem [ADO.NET-Verbindungszeichenfolgenmodell](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax).
* [Storage-Verbindungszeichenfolgen](./storage.md) beschreiben, wie Kusto auf einen externen Speicherdienst wie Azure Blob Storage oder Azure Data Lake Storage verwiesen wird.
* SQL-Verbindungszeichenfolgen werden vom [Kusto-Plug-In „sql_request“](../../query/sqlrequestplugin.md) verwendet, um Anforderungen an den Azure DB-Dienst auszugeben, sowie vom [Befehl zum Exportieren von Daten nach SQL](../../management/data-export/export-data-to-sql.md).  
  Diese Verbindungszeichenfolgen entsprechen der Spezifikation [SqlClient-Verbindungszeichenfolgen](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings).

> [!NOTE]
> Einige Verbindungszeichenfolgen können auf Sicherheitsprinzipale verweisen. Weitere Informationen zum Angeben von Sicherheitsprinzipalen in Verbindungszeichenfolgen finden Sie unter [Prinzipale und Identitätsanbieter](../../management/access-control/principals-and-identity-providers.md).
