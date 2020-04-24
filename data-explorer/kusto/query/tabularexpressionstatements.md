---
title: Tabellenförmige Ausdrucksanweisungen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Tabularausdrucksanweisungen in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1209f96ed99de79d3fa6ac00e3a115a00a6248e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506612"
---
# <a name="tabular-expression-statements"></a>Anweisungen für tabellarische Ausdrücke

Die tabellarische Ausdrucksanweisung ist das, was Menschen normalerweise im Sinn haben, wenn sie über Abfragen sprechen. Diese Anweisung wird in der Regel zuletzt in der Anweisungsliste angezeigt, und sowohl ihre Eingabe als auch ihre Ausgabe bestehen aus Tabellen oder tabellenförmigen Datensätzen.

Kusto verwendet ein Datenflussmodell für die tabellenförmige Ausdrucksanweisung. Die typische Struktur einer tabellenförmigen Ausdrucksanweisung ist eine Zusammensetzung von *tabellenartigen Datenquellen* (z. B. Kusto-Tabellen), *tabellarischen Datenoperatoren* (z. B. Filtern und Projektionen) und potenziell *Renderoperatoren*. Die Komposition wird durch das`|`Pipe-Zeichen ( dargestellt, das der Anweisung eine sehr regelmäßige Form verleiht, die den Fluss von Tabellendaten von links nach rechts visuell darstellt.
Jeder Operator akzeptiert einen tabellarischen Datensatz "aus der Leitung" und zusätzliche Eingaben (einschließlich anderer Tabellendatensätze) aus dem Text des Operators und gibt dann einen tabellarischen Datensatz an den nächsten Folgenden-Operator aus:   

*source1* `|` *operator1* `|` *operator2* `|` *renderInstruction*

Im folgenden Beispiel ist `Logs` die Quelle (ein Verweis auf eine Tabelle `where` in der aktuellen Datenbank), der erste Operator (der Datensätze aus seiner `count` Eingabe gemäß einem Prädikat pro Datensatz herausfiltert), und der zweite Operator ist (der die Anzahl der Datensätze in seinem Eingabedatensatz zählt):

```kusto
Logs | where Timestamp > ago(1d) | count
```

Im folgenden komplexeren Beispiel `join` wird der Operator verwendet, um Datensätze aus zwei Eingabedatensätzen zu kombinieren: einem, der ein Filter für die Tabelle ist, und einem anderen, der `Logs` ein Filter für die `Events` Tabelle ist.

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>Tabellarische Datenquellen

Eine **tabellarische Datenquelle** erstellt Datensätze, die von **tabellarischen Datenoperatoren**weiterverarbeitet werden sollen. Kusto unterstützt eine Reihe dieser Quellen:

* Tabellenverweise (die sich auf eine Kusto-Tabelle beziehen, in der Kontextdatenbank oder einem anderen Cluster/einer anderen Datenbank.)
* Der Tabellarische [Bereichsoperator](rangeoperator.md).
* Der [Druckoperator](printoperator.md).
* Aufruf einer Funktion, die eine Tabelle zurückgibt.
* Ein [Tabellenliteral](datatableoperator.md) ("Datatable").