---
title: Bewährte Methoden für Abfragen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden bewährte Methoden für Abfragen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 3458c2fcd7fab3345f65393d7e9a13e844088a9f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663859"
---
# <a name="query-best-practices"></a>Best Practices für Abfragen 

## <a name="general"></a>Allgemein

Es gibt mehrere "Dos and Don'ts", denen Sie folgen können, damit Ihre Abfrage schneller ausgeführt wird.

### <a name="do"></a>Sie sollten

*   Verwenden Sie zunächst Zeitfilter. Kusto ist hochgradig optimiert, um Zeitfilter zu nutzen.
*   Bei Verwendung von Zeichenfolgenoperatoren:
    *   Bevorzugen `has` Sie `contains` Operator über bei der Suche nach vollständigen Token. `has`ist performant, da es nicht nach Unterzeichenfolgen suchen muss.
    *   Bevorzugen Sie die Verwendung von Operatoren mit Groß-/Kleinschreibung, da sie leistungsstärker sind. Bevorzugen `==` Sie z. `=~` `in` B. über `in~`, über und `contains` / `contains_cs` `has` / `has_cs` `contains_cs` mehr `contains` (aber wenn Sie ganz vermeiden und verwenden können, ist das noch besser).
*   Bevorzugen Sie das Suchen in `*` einer bestimmten Spalte, anstatt sie zu verwenden (Volltextsuche in allen Spalten)
*   Wenn Sie feststellen, dass sich die meisten Abfragen mit dem Extrahieren von Feldern aus [dynamischen Objekten](./scalar-data-types/dynamic.md) über Millionen von Zeilen hinweg befassen, sollten Sie diese Spalte zur Aufnahmezeit materialisieren. Auf diese Weise zahlen Sie nur einmal für die Spaltenextraktion.  
*   Wenn Sie `let` über eine Anweisung verfügen, deren Wert Sie mehr als einmal verwenden, sollten `materialize()`Sie die [Funktion materialize()](./materializefunction.md) verwenden (siehe einige bewährte [Methoden](#materialize-function) zur Verwendung ).

### <a name="dont"></a>Sie sollten auf keinen Fall

*   Versuchen Sie keine neuen `limit [small number]` `count` Abfragen ohne oder am Ende.
    Das Ausführen ungebundener Abfragen über unbekannte Datensätze kann zu GBs von Ergebnissen führen, die an den Client zurückgegeben werden, was zu einer langsamen Antwort und einer ausgelasteten Reaktion des Clusters führt.
*   Wenn Sie feststellen, dass Sie Conversions (JSON, Zeichenfolge usw.) über 1 Milliarde Datensätze anwenden, gestalten Sie Ihre Abfrage neu, um die In die Konvertierung eingespeiste Datenmenge zu reduzieren.
*   Verwenden `tolower(Col) == "lowercasestring"` Sie nicht, um Groß-/Kleinschreibung unempfindliche Vergleiche zu machen. Verwenden Sie stattdessen `Col =~ "lowercasestring"`.
    *   Wenn sich Ihre Daten bereits in Kleinbuchstaben (oder Großbuchstaben) befinden, `Col == "lowercasestring"` vermeiden `Col == "UPPERCASESTRING"`Sie die Verwendung von Groß-/Kleinschreibungsvergleichen, und verwenden Sie stattdessen (oder ).
*   Filtern Sie nicht nach einer berechneten Spalte, wenn Sie nach einer Tabellenspalte filtern können. Mit anderen Worten: `T | extend _value = <expression> | where predicate(_value)`statt `T | where predicate(<expression>)`, tun: .

## <a name="summarize-operator"></a>summarize-Operator

*   Wenn die Gruppe nach Schlüsseln des summarize-Operators mit hoher Kardinalität (Best Practice: über 1 Million) ist, wird empfohlen, die [verwendungsgemäß.strategy=shuffle](./shufflequery.md)zu verwenden.

## <a name="join-operator"></a>join-Operator

*   Wenn Sie den [Join-Operator](./joinoperator.md)verwenden, wählen Sie die Tabelle mit weniger Zeilen als erste aus (links). 
*   Wenn Sie [Join-Operatordaten](./joinoperator.md) über Cluster hinweg verwenden, führen Sie die Abfrage auf der "rechten" Seite der Verknüpfung aus (wo sich die meisten Daten befinden).
*   Wenn die linke Seite klein (bis zu 100.000 Datensätze) und die rechte Seite groß ist, verwenden Sie [hint.strategy=broadcast](./broadcastjoin.md).
*   Wenn beide Seiten der Verknüpfung zu groß sind und der Verknüpfungsschlüssel mit hoher Kardinalität ist, verwenden Sie [hint.strategy=shuffle](./shufflequery.md).
    
## <a name="parse-operator-and-extract-function"></a>parse operator und extract() funktion

*   [der analyseoperator](./parseoperator.md) (einfacher Modus) ist nützlich, wenn die Werte in der Zielspalte Zeichenfolgen enthalten, die alle das gleiche Format oder Muster verwenden.
Verwenden Sie z. B. `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`für eine Spalte mit Werten wie `parse` , beim `extract()` Extrahieren der Werte jedes Felds den Operator anstelle mehrerer Anweisungen.
*   [extract() Funktion](./extractfunction.md) ist nützlich, wenn die analysierten Zeichenfolgen nicht alle dem gleichen Format oder Muster folgen.
Extrahieren Sie in solchen Fällen die erforderlichen Werte mit einem REGEX.

## <a name="materialize-function"></a>materialize()-Funktion

*   Versuchen Sie bei Verwendung der [funktionmaterialize(),](./materializefunction.md)alle möglichen Operatoren zu drücken, die den materialisierten Datensatz reduzieren und die Semantik der Abfrage weiterhin beibehält. Filter oder Projekt nur erforderliche Spalten.
    
    **Beispiel:**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* Der Filter für Text ist wechselseitig und kann zum Materialisierungsausdruck verschoben werden.
    Die Abfrage benötigt `Timestamp`nur `Text` `Resource1` diese `Resource2` Spalten , , und daher wird empfohlen, diese Spalten innerhalb des materialisierten Ausdrucks zu projizieren.
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   Wenn die Filter nicht identisch sind wie in dieser Abfrage:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   Es kann sich lohnen (wenn der kombinierte Filter das materialisierte Ergebnis drastisch reduziert), beide Filter auf dem materialisierten Ergebnis durch einen logischen `or` Ausdruck wie in der Abfrage unten zu kombinieren. Behalten Sie jedoch die Filter in jedem Union-Bein bei, um die Semantik der Abfrage beizubehalten:
     
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```
    