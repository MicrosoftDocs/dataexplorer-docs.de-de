---
title: Abfragen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Abfragen in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2ac338600320d3f83a92e22e405f630ee308df2f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510777"
---
# <a name="queries"></a>Abfragen

Eine Abfrage ist ein schreibgeschützter Vorgang für die aufgenommenen Daten eines Kusto Engine-Clusters. Abfragen werden immer im Kontext einer bestimmten Datenbank im Cluster ausgeführt (obwohl sie auch auf Daten in einer anderen Datenbank oder sogar in einem anderen Cluster verweisen können).

Da die Ad-hoc-Abfrage von Daten das Szenario mit der obersten Priorität von Kusto ist, ist die Kusto Query Language-Syntax für nicht-fachkundige Benutzer optimiert, die Abfragen über ihre Daten erstellen und ausführen und in der Lage sind, eindeutig zu verstehen, was jede Abfrage (logisch) tut.

Die Sprachsyntax ist die eines Datenflusses, bei dem "Daten" wirklich "Tabellendaten" bedeutet (Daten in einer oder mehreren Zeilen/Spalten rechteckige Form). Eine Abfrage besteht mindestens aus Quelldatenverweisen (Referenzen auf Kusto-Tabellen) und einem oder mehreren **Abfrageoperatoren,** `|`die nacheinander angewendet werden und visuell durch die Verwendung eines Rohrzeichens ( ) angezeigt werden, um Operatoren zu begrenzen.

Beispiel:

```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```
    
Jeder Filter mit einem senkrechten Strich ( `|` ) als Präfix ist eine Instanz eines *Operators*mit einigen Parametern. Die Eingabe des Operators ist die Tabelle, die das Ergebnis der vorhergehenden Pipeline ist. In den meisten Fällen sind alle Parameter [skalare Ausdrücke](./scalar-data-types/index.md) über die Spalten der Eingabe hinweg.
In einigen Fällen sind die Parameter die Namen von Eingabespalten, und in einigen Fällen ist der Parameter eine zweite Tabelle. Das Ergebnis einer Abfrage ist immer eine Tabelle, auch wenn sie nur eine Spalte und eine Zeile enthält.

## <a name="reference-query-operators"></a>Referenz: Abfrageoperatoren

> `T` wird in den folgenden Abfragebeispielen verwendet, um die vorhergehende Pipeline oder Quelltabelle anzugeben.