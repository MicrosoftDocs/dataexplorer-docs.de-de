---
title: 'Bewährte Methoden für Abfragen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden bewährte Methoden für Abfragen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: e6db181188250d28689cca64762e13973f2f33f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128817"
---
# <a name="best-practices"></a>Bewährte Methoden 

## <a name="general"></a>Allgemein

Es gibt mehrere "DOS" und "es", die Sie befolgen können, um die Abfrage schneller auszuführen.

### <a name="do"></a>Sie sollten

*   Verwenden Sie zunächst Zeitfilter. Kusto ist hochgradig optimiert, um Zeitfilter zu verwenden.
*   Bei Verwendung von Zeichen folgen Operatoren:
    *   Bevorzugen Sie den Operator, wenn Sie nach `has` `contains` vollständigen Tokens suchen. `has`ist leistungsfähiger, da es nicht in der Suche nach Teil Zeichenfolgen ist.
    *   Bevorzugen Sie ggf. die Verwendung von Operatoren mit Berücksichtigung der Groß-/Kleinschreibung, da Sie leistungsfähiger Verwenden Sie z. b `==` `=~` `in` `in~` `contains_cs` `contains` `contains` / `contains_cs` `has` / `has_cs` . die Verwendung von "Over", "Over" und "Over"
*   Bevorzugen Sie anstelle der Verwendung von eine bestimmte Spalte `*` (Volltextsuche in allen Spalten).
*   Wenn Sie feststellen, dass die meisten Ihrer Abfragen mit dem Extrahieren von Feldern aus [dynamischen Objekten](./scalar-data-types/dynamic.md) über mehrere Millionen von Zeilen zu tun haben, sollten Sie diese Spalte bei der Erfassungs Zeit materialisieren. Auf diese Weise zahlen Sie nur einmal für die Spalten Extraktion.  
*   Wenn Sie über eine- `let` Anweisung verfügen, deren Wert Sie mehrmals verwenden, sollten Sie die [Funktion "Materialize ()](./materializefunction.md) " verwenden (siehe einige [bewährte Methoden](#materialize-function) zur Verwendung von `materialize()` ).

### <a name="dont"></a>Sie sollten auf keinen Fall

*   Versuchen Sie nicht, neue Abfragen ohne `limit [small number]` oder `count` am Ende auszuführen.
    Das Ausführen von ungebundenen Abfragen über ein unbekanntes DataSet kann GSB der Ergebnisse ergeben, die an den Client zurückgegeben werden, was zu einer langsamen Reaktion führt und der Cluster ausgelastet ist.
*   Wenn Sie Konvertierungen (JSON, String usw.) über 1 Milliarde Datensätze anwenden, müssen Sie die Abfrage neu strukturieren, um die Menge der Daten zu reduzieren, die in die Konvertierung eingespeist werden.
*   Verwenden `tolower(Col) == "lowercasestring"` Sie für Vergleiche ohne Berücksichtigung der Groß-/Kleinschreibung Verwenden Sie stattdessen `Col =~ "lowercasestring"`.
    *   Wenn Ihre Daten bereits in Kleinbuchstaben (oder in Großbuchstaben) vorhanden sind, sollten Sie keine Vergleiche ohne Berücksichtigung der Groß-/Kleinschreibung vermeiden und `Col == "lowercasestring"` stattdessen (oder `Col == "UPPERCASESTRING"` ) verwenden.
*   Filtern Sie nach einer berechneten Spalte nicht, wenn Sie nach einer Tabellenspalte filtern können. Anders ausgedrückt: anstelle von: `T | extend _value = <expression> | where predicate(_value)` `T | where predicate(<expression>)` .

## <a name="summarize-operator"></a>summarize-Operator

*   Wenn die Group by-Schlüssel des Zusammenfassungs Operators eine hohe Kardinalität aufweisen (bewährte Vorgehensweise: oberhalb von 1 Million), empfiehlt es sich, den [Hinweis. Strategy = shuffle](./shufflequery.md)zu verwenden.

## <a name="join-operator"></a>join-Operator

*   Wenn Sie den [Join-Operator](./joinoperator.md)verwenden, wählen Sie die Tabelle mit weniger Zeilen als die erste Tabelle aus (am weitesten links). 
*   Wenn Sie [joinoperatordaten](./joinoperator.md) Cluster übergreifend verwenden, führen Sie die Abfrage auf der rechten Seite des Joins aus (wo sich die meisten Daten befinden).
*   Wenn die linke Seite klein ist (bis zu 100.000 Datensätze) und die Rechte Seite groß ist, verwenden Sie [Hint. Strategy = Broadcast](./broadcastjoin.md).
*   Wenn beide Seiten der Verknüpfung zu groß sind und die jointaste eine hohe Kardinalität hat, verwenden Sie [Hint. Strategy = shuffle](./shufflequery.md).
    
## <a name="parse-operator-and-extract-function"></a>Analyse Operator und Extract ()-Funktion

*   der Analyse [Operator](./parseoperator.md) (einfacher Modus) ist nützlich, wenn die Werte in der Ziel Spalte Zeichen folgen enthalten, die alle das gleiche Format oder Muster aufweisen.
Verwenden Sie z. b. für eine Spalte mit Werten wie `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` , wenn Sie die Werte jedes Felds extrahieren, `parse` anstelle mehrerer Anweisungen den-Operator `extract()` .
*   die [extract ()-Funktion](./extractfunction.md) ist hilfreich, wenn die analysierten Zeichen folgen nicht alle das gleiche Format oder Muster aufweisen.
Extrahieren Sie in solchen Fällen die erforderlichen Werte mithilfe eines Regex.

## <a name="materialize-function"></a>Materialize ()-Funktion

*   Wenn Sie die [Materialize ()-Funktion](./materializefunction.md)verwenden, versuchen Sie, alle möglichen Operatoren zu übermitteln, die das materialisierte DataSet verringern, und die Semantik der Abfrage weiterhin beibehalten. Beispielsweise Filter oder nur erforderliche Spalten.
    
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

* Der Filter für Text ist gegenseitig und kann auf den materialisieren-Ausdruck geschoben werden.
    Die Abfrage benötigt nur diese Spalten `Timestamp` , `Text` , `Resource1` und `Resource2` daher wird empfohlen, diese Spalten innerhalb des materialisierten Ausdrucks zu projizieren.
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   Wenn die Filter in dieser Abfrage nicht identisch sind:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   Dies kann sich als lohnenswert erweisen (wenn der kombinierte Filter das materialisierte Ergebnis drastisch reduziert), um beide Filter für das materialisierte Ergebnis durch einen logischen `or` Ausdruck wie in der folgenden Abfrage zu kombinieren. Behalten Sie jedoch die Filter in den einzelnen Union-Beinen bei, um die Semantik der Abfrage beizubehalten:
     
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
    