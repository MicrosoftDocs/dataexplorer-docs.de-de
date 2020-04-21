---
title: Teilweise Abfragefehler - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden teilweise Abfragefehler in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d65256c0ac50a80e3e3b095e8d2348dbae5e585b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523119"
---
# <a name="partial-query-failures"></a>Partielle Abfragefehler

Ein *teilweiser Abfragefehler* ist ein Fehler beim Ausführen der Abfrage, die erst erkannt wird, nachdem die Abfrage die eigentliche Ausführungsphase gestartet hat. Zu diesem Zeitpunkt hat Kusto die `200 OK` HTTP-Statuszeile bereits an den Client zurückgegeben und kann sie nicht "zurücknehmen", daher muss sie den Fehler im Ergebnisstream angeben, der die Abfrageergebnisse an den Client zurückführt. (Möglicherweise wurden einige Ergebnisdaten bereits an den Aufrufer zurückgegeben.)

Es gibt mehrere Arten von partiellen Abfragefehlern:
* [Runaway-Abfragen](runawayqueries.md): Abfragen, die zu viele Ressourcen in Anspruch nehmen.
* [Ergebniskürzung](resulttruncation.md): Abfragen, deren Resultset abgeschnitten wurde, da sie einen Grenzwert überschritten haben.
* [Überläufe](overflow.md): Abfragen, die einen Überlauffehler auslösen.

Teilweise Abfragefehler können auf zwei Arten an den Client zurückgemeldet werden:

* Als Teil der Ergebnisdaten (eine spezielle Art von Datensatz zeigt an, dass dies nicht die Daten selbst sind). Dies ist der Standardweg.
* Als Teil der Tabelle "QueryStatus" im Ergebnisstream. Dies geschieht mithilfe `deferpartialqueryfailures` der Option im `properties` Steckplatz der Anforderung (`Kusto.Data.Common.ClientRequestProperties.OptionDeferPartialQueryFailures`).
  Clients, die dies tun, übernehmen die Verantwortung, den `QueryStatus` gesamten Ergebnisstream aus dem Dienst zu `Severity` `2` verbrauchen, das Ergebnis zu finden und sicherzustellen, dass kein Datensatz in diesem Ergebnis einen von oder einen kleineren Datensatz enthält. 