---
title: 'Abfrage Konsistenz: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt die Konsistenz der Abfrage in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 8b4d1df4dc9a035764f9d50a4f9c4dcf452da67e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512264"
---
# <a name="query-consistency"></a>Abfragekonsistenz

Kusto unterstützt zwei Abfrage Konsistenz Modelle: **stark** und **schwach**.

Stark konsistente Abfragen (Standard) haben eine "Read-My-Changes"-Garantie. Wenn Sie einen Befehls Steuerungs Befehl senden und Bestätigung erhalten, dass der Befehl erfolgreich abgeschlossen wurde, werden Sie sicherstellen, dass jede unmittelbar nachfolgende Abfrage die Ergebnisse des Befehls berücksichtigt.

Schwach konsistente Abfragen, die explizit vom Client aktiviert werden müssen, verfügen nicht über diese Garantie. Bei Clients, die Abfragen ausführen, wird möglicherweise eine Latenzzeit (in der Regel 1-2 Minuten) zwischen den Änderungen und den Abfragen beobachtet

Der Vorteil von schwach konsistenten Abfragen ist, dass die Auslastung des Cluster Knotens, der Daten Bank Änderungen verarbeitet, reduziert wird. Im Allgemeinen wird empfohlen, zuerst das stark konsistente Modell zu testen. Wechseln Sie zur Verwendung von schwach konsistenten Abfragen, wenn dies erforderlich ist.

Wenn Sie zu schwach konsistenten Abfragen wechseln möchten, legen Sie die-Eigenschaft fest, `queryconsistency` Wenn Sie einen [Rest-API](../api/rest/request.md)-Befehl Benutzer des .NET-Clients können Sie auch in der [Kusto-Verbindungs Zeichenfolge](../api/connection-strings/kusto.md) oder als Flag in den [Eigenschaften der Client Anforderung](../api/netfx/request-properties.md)festlegen.
