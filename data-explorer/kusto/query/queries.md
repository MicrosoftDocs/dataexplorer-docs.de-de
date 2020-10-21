---
title: 'Abfragen: Azure-Daten-Explorer'
description: In diesem Artikel werden Abfragen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8be14a48f5b24d344454d9aa5012f48e7d7e2b8e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250962"
---
# <a name="query-operators"></a>Abfrageoperatoren

Eine Abfrage ist ein Schreib geschützter Vorgang für die erfassten Daten eines Kusto-Engine-Clusters. Abfragen werden immer im Kontext einer bestimmten Datenbank im Cluster ausgeführt. Sie können auch auf Daten in einer anderen Datenbank oder sogar in einem anderen Cluster verweisen.

Da es sich bei Ad-hoc-Abfragen von Daten um das Szenario mit der höchsten Priorität für Kusto handelt, ist die Syntax der Kusto-Abfragesprache für nicht erfahrene Benutzer optimiert, die Abfragen für Ihre Daten erstellen und ausführen und eindeutig erkennen können, was jede Abfrage tut (logisch).

Die Sprachsyntax ist die eines Datenflusses, bei dem "Data" "tabellarische Daten" (Daten in mindestens einer Zeilen-/Spalt-Recht eckigen Form) bedeutet. Eine Abfrage besteht mindestens aus Quelldaten verweisen (verweisen auf Kusto-Tabellen) und einem oder mehreren **Abfrage Operatoren** , die nacheinander angewendet werden. Dies wird visuell durch die Verwendung eines Senk Zeichens ( `|` ) zum Begrenzen von Operatoren angegeben.

Beispiel:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```

Jeder Filter mit einem senkrechten Strich ( `|` ) als Präfix ist eine Instanz eines *Operators*mit einigen Parametern. Die Eingabe des Operators ist die Tabelle, die das Ergebnis der vorhergehenden Pipeline ist. In den meisten Fällen sind alle Parameter [skalare Ausdrücke](./scalar-data-types/index.md) über die Spalten der Eingabe hinweg.
In einigen Fällen sind die Parameter die Namen von Eingabespalten, und in einigen Fällen ist der Parameter eine zweite Tabelle. Das Ergebnis einer Abfrage ist immer eine Tabelle, auch wenn sie nur eine Spalte und eine Zeile enthält.

`T` wird in der Abfrage verwendet, um die vorangehende Pipeline oder Quell Tabelle anzugeben.
