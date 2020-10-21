---
title: 'Tabellen Ausdrucks Anweisungen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt Tabellen Ausdrucks Anweisungen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 943281c2e06aecfffab72ca0f98597c19938d936
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250765"
---
# <a name="tabular-expression-statements"></a>Anweisungen für tabellarische Ausdrücke

Die tabellarische Ausdrucks Anweisung ist das, was Sie in der Regel berücksichtigen, wenn Sie über Abfragen sprechen. Diese Anweisung wird in der Regel zuletzt in der Anweisungs Liste angezeigt, und sowohl die Eingabe als auch Ihre Ausgabe bestehen aus Tabellen oder Tabellen Datasets.

Kusto verwendet ein Datenfluss Modell für die tabellarische Ausdrucks Anweisung. Die typische Struktur einer tabellarischen Ausdrucks Anweisung ist eine Zusammensetzung von *tabellarischen Datenquellen* (z. b. Kusto-Tabellen), *Tabellendaten Operatoren* (z. b. Filter und Projektionen) und potenziell *renderingoperatoren*. Die Komposition wird durch das senkrechte Zeichen ( `|` ) dargestellt und gibt der Anweisung ein sehr reguläres Formular, das den Fluss von Tabellendaten von links nach rechts visuell darstellt.
Jeder Operator akzeptiert einen tabellarischen Datensatz „aus der Pipe“ und weitere Eingaben (einschließlich anderer tabellarischer Datensätze) aus dem Textkörper des Operators und sendet dann wie folgt ein tabellarisches Dataset an den nächsten Operator:    

*Quelle1* `|` *Operator1* `|` *Operator2* `|` *renderinstruction*

Im folgenden Beispiel ist die Quelle `Logs` (ein Verweis auf eine Tabelle in der aktuellen Datenbank), der erste Operator `where` (mit dem Datensätze aus der Eingabe gemäß einem einzelnen Daten Satz Prädikat herausgefiltert werden), und der zweite Operator ist `count` (der die Anzahl der Datensätze im Eingabe DataSet zählt):

```kusto
Logs | where Timestamp > ago(1d) | count
```

Im folgenden komplexeren Beispiel wird der- `join` Operator zum Kombinieren von Datensätzen aus zwei Eingabe Datasets verwendet: einer, bei dem es sich um einen Filter für die Tabelle handelt, und ein weiterer, bei dem es sich um `Logs` einen Filter für die `Events` Tabelle handelt.

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

Eine **tabellarische Datenquelle** erzeugt Sätze von Datensätzen, die weiter von **Tabellendaten Operatoren**verarbeitet werden sollen. Kusto unterstützt eine Reihe von folgenden Quellen:

* Tabellen Verweise (die sich auf eine Kusto-Tabelle in der Kontext Datenbank oder einem anderen Cluster bzw. einer anderen Datenbank beziehen)
* Der Tabellen [Bereichs Operator](rangeoperator.md).
* Der [Druck Operator](printoperator.md).
* Ein Aufruf einer Funktion, die eine Tabelle zurückgibt.
* Ein [tabellenliteralzeichen](datatableoperator.md) ("datdatababel").