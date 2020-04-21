---
title: Abfragekonsistenz - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Abfragekonsistenz in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: b66540af2d2d4bef4571249474cd618e69eb2261
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523102"
---
# <a name="query-consistency"></a>Abfragekonsistenz

Kusto unterstützt zwei Abfragekonsistenzmodelle: **stark** und **schwach.**

Stark konsistente Abfragen (Standard) haben eine "Read-my-changes"-Garantie. Ein Client, der einen Steuerelementbefehl sendet und eine positive Bestätigung erhält, dass der Befehl erfolgreich abgeschlossen wurde, wird garantiert, dass jede unmittelbar folgende Abfrage die Ergebnisse des Befehls beobachtet.

Schwach konsistente Abfragen (muss vom Client explizit aktiviert werden) geben die Garantie nicht aus. Clients, die Abfragen durchführen, können eine gewisse Latenz (in der Regel 1-2 Minuten) zwischen Änderungen und Abfragen beobachten, die diese Änderungen widerspiegeln.

Der Vorteil von schwach konsistenten Abfragen besteht darin, dass die Auslastung des Clusterknotens, der Datenbankänderungen verarbeitet, verringert wird. Im Allgemeinen wird empfohlen, dass Kunden zuerst das stark konsistente Modell ausprobieren und bei unbedingt erforderlicher Verwendung schwacher Konsistenz verwenden.

Das Wechseln zu schwach konsistenten `queryconsistency` Abfragen erfolgt durch Festlegen der Eigenschaft beim Ausführen eines [REST-API-Aufrufs](../api/rest/request.md). Benutzer des Kusto .NET-Clients können sie auch in der [Kusto-Verbindungszeichenfolge](../api/connection-strings/kusto.md) oder als Flag in den [Clientanforderungseigenschaften](../api/netfx/request-properties.md)festlegen.