---
title: Datenexport – Azure Data Explorer
description: In diesem Artikel wird der Datenexport in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: e6afcf86a02f1f74fe024c1b94d7f9458a72a4e7
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763423"
---
# <a name="data-export"></a>Datenexport

Der Datenexport ist der Prozess, der eine Kusto-Abfrage ausführt und die Ergebnisse schreibt. Die Abfrageergebnisse sind zur späteren Überprüfung verfügbar.

Für den Datenexport stehen mehrere Methoden zur Verfügung:

## <a name="client-side-export"></a>Clientseitiger Export
  In der einfachsten Form kann der Datenexport auf Clientseite erfolgen. Der Client führt eine Abfrage für den Dienst aus, liest die Ergebnisse ein und schreibt sie anschließend.
Diese Art des Datenexports hängt vom verwendeten Clienttool ab. Als Ziel für den Export wird in der Regel das lokale Dateisystem verwendet, in dem das Tool ausgeführt wird. Dieses Modell wird unter anderem von [Kusto.Explorer](../../tools/kusto-explorer.md) und der [Webbenutzeroberfläche](../../../web-query-data.md) verwendet.

## <a name="service-side-export-pull"></a>Dienstseitiger Export (Pull)
  Wenn es sich beim Ziel des Exports um eine Tabelle im gleichen oder einem anderen Cluster/in der gleichen oder einer anderen Datenbank wie die Abfrage handelt, verwenden Sie den Ablauf „Erfassung aus einer Abfrage“ für die Zieltabelle. Hierbei wird eine Abfrage ausgeführt, und deren Ergebnisse werden sofort in einer Tabelle erfasst. Weitere Informationen finden Sie unter [Erfassung von Abfragen](../../management/data-ingestion/ingest-from-query.md).

## <a name="service-side-export-push"></a>Dienstseitiger Export (Push)
  Die oben genannten Methoden [Clientseitiger Export](#client-side-export)und [Dienstseitiger Export (Pull)](#service-side-export-pull) sind beschränkt. Die Abfrageergebnisse müssen über eine einzelne Netzwerkverbindung zwischen dem Producer, der die Abfrage durchführt, und dem Consumer, der die Ergebnisse schreibt, gestreamt werden.
Verwenden Sie für einen skalierbaren Datenexport das pushbasiertes Exportmodell, bei dem der Dienst, der die Abfrage durchführt, auch die Ergebnisse der Abfrage auf optimierte Weise schreibt. Dieses Modell wird durch eine Reihe von Steuerungsbefehlen vom Typ `.export` verfügbar gemacht, die das Exportieren von Abfrageergebnissen in eine [externe Tabelle](export-data-to-an-external-table.md), eine [SQL-Tabelle](export-data-to-sql.md) oder einen [externen Blobspeicher](export-data-to-storage.md) unterstützen.
  
  Befehle für dienstseitige Exporte sind durch die verfügbare Datenexportkapazität des Clusters beschränkt.
Mithilfe des Befehls [.show capacity](../../management/diagnostics.md#show-capacity) können Sie die Gesamtkapazität sowie die bereits beanspruchte Kapazität und die Restkapazität für Datenexporte anzeigen.

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>Empfehlungen für die Geheimnisverwaltung bei Verwendung von Datenexportbefehlen

Im Idealfall exportieren Sie Daten in ein Remoteziel, z. B. Azure Blob Storage und Azure SQL-Datenbank. Verwenden Sie implizit die Anmeldeinformationen des Sicherheitsprinzipals, der den Datenexportbefehl ausführt. Diese Methode ist in einigen Szenarien nicht möglich. Azure Blob Storage unterstützt beispielsweise nur seine eigenen Token, nicht aber das Konzept eines Sicherheitsprinzipals.
Dieses Feature unterstützt die Inlineangabe der erforderlichen Anmeldeinformationen als Teil des Steuerungsbefehls für den Datenexport.

So führen Sie den Export auf sichere Weise aus:

* Verwenden Sie [verschleierte Zeichenfolgenliterale](../../query/scalar-data-types/string.md#obfuscated-string-literals) wie etwa `h@"..."`, wenn Sie Geheimnisse versenden. Die Geheimnisse werden bereinigt, sodass sie in einer intern ausgegebenen Ablaufverfolgung nicht angezeigt werden.

* Kennwörter und ähnliche Geheimnisse sollten sicher gespeichert und bei Bedarf von der Anwendung gepullt werden.
