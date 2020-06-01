---
title: Streamingaberfassungs-Richtlinie-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Richtlinie zum Streamen von Daten in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: 312e6cad984491b39bd9a77f0b34d142798e922e
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257993"
---
# <a name="streaming-ingestion-policy"></a>Streamingerfassungsrichtlinie

## <a name="streaming-ingestion-target-scenario"></a>Szenario für streamingingserfassung

Die streamingangserfassung ist für Szenarien vorgesehen, die eine geringe Latenz erfordern, mit einer Erfassungs Zeit von weniger als 10 Sekunden für verschiedene Volumedaten. Sie wird verwendet, um die operative Verarbeitung von vielen Tabellen in einer oder mehreren Datenbanken zu optimieren, bei denen der Datenstrom in jeder Tabelle relativ klein ist (einige wenige Datensätze pro Sekunde), das Gesamtvolumen der Datenerfassung jedoch hoch (Tausende von Datensätzen pro Sekunde) ist.

Verwenden Sie die klassische (Massen-) Erfassung anstelle der streamingerfassung, wenn die Datenmenge auf mehr als 4 GB pro Stunde und Tabelle anwächst. 

* Informationen zum Implementieren dieses Features finden Sie unter [Streaming](../../ingest-data-streaming.md)-Erfassung.
* Weitere Informationen über Befehle zum Steuern der streamingansung finden Sie unter [Steuern von Befehlen für die Verwaltung der streamingerfassungs-Richtlinie](streamingingestion-policy.md)
 
## <a name="streaming-ingestion-policy-definition"></a>Richtlinien Definition für die streamingerfassung

Die Richtlinie für die streamingansung enthält die folgenden Eigenschaften:

* **Isaktiviert**:
  * definiert den Status der streamingerfassungs-Funktionalität für die Tabelle/Datenbank.
  * obligatorisch, kein Standardwert, muss explizit auf " *true* " oder " *false* " festgelegt werden.
* **Hintallocatedrate**:
  * Wenn Set ein Hinweis auf die stündliche Datenmenge in Gigabyte ist, die für die Tabelle erwartet wird. Dieser Hinweis unterstützt das System beim Anpassen der Menge an Ressourcen, die einer Tabelle zur Unterstützung der streamingerfassung zugewiesen werden.
  * Standardwert *null* (nicht festgelegt)

Um die streamingansung für eine Tabelle zu aktivieren, definieren Sie die Richtlinie für die streamingerfassung mit *aktiviertem isenable* auf *true*. Diese Definition kann für eine Tabelle selbst oder für die Datenbank festgelegt werden.
Wenn Sie diese Richtlinie auf Datenbankebene definieren, werden die gleichen Einstellungen auf alle vorhandenen und zukünftigen Tabellen in der Datenbank angewendet. Wenn die Richtlinie für die streamingerfassung sowohl auf der Tabellen-als auch auf der Datenbankebene festgelegt ist, hat die Einstellung auf Tabellenebene Vorrang Diese Einstellung bedeutet, dass die streamingerfassung in der Regel für die Datenbank aktiviert, aber speziell für bestimmte Tabellen oder umgekehrt deaktiviert werden kann.

> [!NOTE]
> Wenn eine Tabelle die streamingerfassung nicht direkt durchführt, sondern nur über eine Update Richtlinie, muss für diese Tabelle keine Richtlinie für die streamingansung definiert werden.


## <a name="set-the-data-rate-hint"></a>Festlegen des Datenraten Hinweises
Die Richtlinie für die streamingansung kann einen Hinweis auf die stündliche Datenmenge enthalten, die für die Tabelle erwartet wird. Mithilfe dieses Hinweises kann das System die Menge der Ressourcen anpassen, die für diese Tabelle zur Unterstützung der streamingerfassung reserviert werden.
Legen Sie den Hinweis fest, wenn die Rate der Streamingdaten in die Tabelle 1 GB/Stunde überschreitet.
Wenn Sie _hintallocatedrate_ in der streamingerfassungs-Richtlinie für die Datenbank festlegen, legen Sie Sie durch die Tabelle mit der höchsten erwarteten Datenrate fest. Es wird nicht empfohlen, den effektiven Hinweis für eine Tabelle auf einen Wert festzulegen, der wesentlich höher als die erwartete Spitzen Zahl für stündlich ist. Diese Einstellung kann sich negativ auf die Abfrageleistung auswirken.
