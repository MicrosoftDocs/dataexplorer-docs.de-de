---
title: Datenexport – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird der Datenexport in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: 88bb9e6541d9dc5c934affc8f777f836aad86ae1
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373493"
---
# <a name="data-export"></a>Datenexport

Der Datenexport umfasst die Ausführung einer Kusto-Abfrage und das Schreiben der Ergebnisse, sodass die Abfrageergebnisse für eine spätere Überprüfung verfügbar sind.

Für den Datenexport stehen mehrere Methoden zur Verfügung:

* **Clientseitiger Export:** In seiner einfachsten Form kann der Datenexport auf der Clientseite durchgeführt werden. In diesem Fall führt der Client eine Abfrage für den Dienst aus, liest die Ergebnisse und schreibt sie anschließend. Diese Art des Datenexports hängt vom verwendeten Clienttool ab. Als Ziel für den Export wird meistens das lokale Dateisystem verwendet, in dem das Tool ausgeführt wird. Dieses Modell wird unter anderem von [Kusto.Explorer](../../tools/kusto-explorer.md) und von der [Webbenutzeroberfläche](../../../web-query-data.md) unterstützt.

* **Dienstseitiger Export (Pull):** Wenn es sich beim Ziel des Exports um eine Kusto-Tabelle handelt (im gleichen Cluster/in der gleichen Datenbank wie die Abfrage oder in einem anderen Cluster/einer anderen Datenbank), verwenden Sie den Ablauf „Erfassung aus einer Abfrage“ für die Zieltabelle. Hierbei wird eine Abfrage ausgeführt, und deren Ergebnisse werden sofort in einer Kusto-Tabelle erfasst. Weitere Informationen finden Sie unter [Datenerfassung](../data-ingestion/index.md).



* **Dienstseitiger Export (Push):** Die oben genannten Methoden unterliegen gewissen Einschränkungen, da die Abfrageergebnisse über eine einzelne Netzwerkverbindung zwischen dem Producer, der die Abfrage durchführt, und dem Consumer, der die Ergebnisse schreibt, gestreamt werden müssen. Für skalierbare Datenexporte bietet Kusto ein pushbasiertes Exportmodell, bei dem der Dienst, der die Abfrage durchführt, auch auf optimierte Weise die Ergebnisse der Abfrage schreibt. Dieses Modell wird durch eine Reihe von Steuerungsbefehlen vom Typ `.export` verfügbar gemacht, die das Exportieren von Abfrageergebnissen in eine [externe Tabelle](export-data-to-an-external-table.md), in eine [SQL-Tabelle](export-data-to-sql.md) oder in einen [externen Blobspeicher](export-data-to-storage.md) unterstützen.
  
  Befehle für dienstseitige Exporte sind durch die verfügbare Datenexportkapazität des Clusters beschränkt. 
  Mithilfe des Befehls [.show capacity](../../management/diagnostics.md#show-capacity) können Sie die Gesamtkapazität sowie die bereits beanspruchte Kapazität und die Restkapazität für Datenexporte anzeigen.

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>Empfehlungen für die Geheimnisverwaltung bei Verwendung von Datenexportbefehlen

Beim Exportieren von Daten in ein Remoteziel (wie Azure Blob Storage oder Azure SQL-Datenbank) werden im Idealfall implizit die Anmeldeinformationen des Sicherheitsprinzipals verwendet, der den Datenexportbefehl ausführt. In einigen Szenarien ist das allerdings nicht möglich. Azure Blob Storage unterstützt beispielsweise nur seine eigenen Token, nicht aber das Konzept eines Sicherheitsprinzipals. Aus diesem Grund unterstützt Kusto die Inlineangabe der erforderlichen Anmeldeinformationen als Teil des Steuerungsbefehls für den Datenexport. Im Anschluss folgen einige Sicherheitsempfehlungen für diese Vorgehensweise:

Verwenden Sie [verschleierte Zeichenfolgenliterale](../../query/scalar-data-types/string.md#obfuscated-string-literals) (beispielsweise `h@"..."`), wenn Sie Geheimnisse an Kusto senden.
Diese Geheimnisse werden dann von Kusto bereinigt, sodass sie in keiner intern ausgegebenen Ablaufverfolgung erscheinen.

Kennwörter und ähnliche Geheimnisse sollten außerdem sicher gespeichert und bei Bedarf von der Anwendung gepullt werden.
