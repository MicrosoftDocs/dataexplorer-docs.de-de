---
title: 'Abfragen: Azure-Daten-Explorer'
description: In diesem Artikel werden Abfragen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d1527208b81c1bd3bbf5fa93a251f7acf4b44662
ms.sourcegitcommit: 97404e9ed4a28cd497d2acbde07d00149836d026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832534"
---
# <a name="query-operators"></a>Abfrageoperatoren

Eine Abfrage ist ein Schreib geschützter Vorgang für die erfassten Daten eines Kusto-Engine-Clusters. Abfragen werden immer im Kontext einer bestimmten Datenbank im Cluster ausgeführt. Sie können auch auf Daten in einer anderen Datenbank oder sogar in einem anderen Cluster verweisen.

Da es sich bei Ad-hoc-Abfragen von Daten um das Szenario mit der höchsten Priorität für Kusto handelt, ist die Syntax der Kusto-Abfragesprache für nicht erfahrene Benutzer optimiert, die Abfragen für Ihre Daten erstellen und ausführen und eindeutig erkennen können, was jede Abfrage tut (logisch).

Die Sprachsyntax ist die eines Datenflusses, bei dem "Data" "tabellarische Daten" (Daten in mindestens einer Zeilen-/Spalt-Recht eckigen Form) bedeutet. Eine Abfrage besteht mindestens aus Quelldaten verweisen (verweisen auf Kusto-Tabellen) und einem oder mehreren **Abfrage Operatoren** , die nacheinander angewendet werden. Dies wird visuell durch die Verwendung eines Senk Zeichens ( `|` ) zum Begrenzen von Operatoren angegeben.

Zum Beispiel:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```

Jeder Filter mit einem senkrechten Strich ( `|` ) als Präfix ist eine Instanz eines *Operators*mit einigen Parametern. Die Eingabe des Operators ist die Tabelle, die das Ergebnis der vorhergehenden Pipeline ist. In den meisten Fällen sind alle Parameter [skalare Ausdrücke](./scalar-data-types/index.md) über die Spalten der Eingabe hinweg.
In einigen Fällen sind die Parameter die Namen von Eingabespalten, und in einigen Fällen ist der Parameter eine zweite Tabelle. Das Ergebnis einer Abfrage ist immer eine Tabelle, auch wenn sie nur eine Spalte und eine Zeile enthält.

`T` wird in der Abfrage verwendet, um die vorangehende Pipeline oder Quell Tabelle anzugeben.
