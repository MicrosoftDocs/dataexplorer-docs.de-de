---
title: Datenbankcursor - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Datenbankcursor in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90ec677a7eaf1f326509828b5415b022742fd9ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521317"
---
# <a name="database-cursors"></a>Datenbankcursor

Ein **Datenbankcursor** ist ein Objekt auf Datenbankebene, das es ermöglicht, eine Datenbank mehrmals abzufragen und konsistente Ergebnisse zu erzielen, selbst wenn parallel zu den Abfragen laufende Vorgänge zum Anfügen von Daten/Datenaufbewahrung stattfinden.

Datenbankcursor sind so konzipiert, dass sie zwei wichtige Szenarien behandeln:

* Die Möglichkeit, dieselbe Abfrage mehrmals zu wiederholen und die gleichen Ergebnisse zu erhalten, solange die Abfrage den "gleichen Datensatz" angibt.

* Die Möglichkeit, eine "genau einmalige" Abfrage auszuführen (eine Abfrage, die nur die Daten "sieht", die eine vorherige Abfrage nicht gesehen hat, weil die Daten damals nicht verfügbar waren).
   Dies ermöglicht es beispielsweise, alle neu angekommenen Daten in einer Tabelle zu durchlaufen, ohne Angst zu haben, denselben Datensatz zweimal zu verarbeiten oder Datensätze versehentlich zu überspringen.

Der Datenbankcursor wird in der Abfragesprache als Skalarwert des Typs `string`dargestellt. Der tatsächliche Wert sollte als undurchsichtig betrachtet werden, und es gibt keine Unterstützung für einen anderen Vorgang als das Speichern des Werts und/oder die Verwendung der unten aufgeführten Cursorfunktionen.

## <a name="cursor-functions"></a>Cursorfunktionen

Kusto bietet drei Funktionen, um die beiden oben genannten Szenarien zu implementieren:

* [cursor_current()](../query/cursorcurrent.md): Verwenden Sie diese Funktion, um den aktuellen Wert des Datenbankcursors abzurufen.
   Sie können diesen Wert als Argument für die beiden anderen Funktionen verwenden.
   Diese Funktion hat auch `current_cursor()`ein Synonym, .

* [cursor_after(rhs:string)](../query/cursorafterfunction.md): Diese spezielle Funktion kann für Tabellendatensätze verwendet werden, für die die [IngestionTime-Richtlinie](ingestiontime-policy.md) aktiviert ist. Es gibt einen skalaren `bool` Wert des Typs `ingestion_time()` zurück, der angibt, ob der Datenbankcursorwert des Datensatzes hinter dem `rhs` Datenbankcursorwert liegt.

* [cursor_before_or_at(rhs:string)](../query/cursorbeforeoratfunction.md): Diese spezielle Funktion kann für die Tabellendatensätze verwendet werden, für die die [IngestionTime-Richtlinie](ingestiontime-policy.md) aktiviert ist. Es gibt einen skalaren `bool` Wert des Typs `ingestion_time()` zurück, der angibt, ob der Datenbankcursorwert des Datensatzes hinter dem `rhs` Datenbankcursorwert liegt.

Die beiden speziellen`cursor_after` `cursor_before_or_at`Funktionen ( und ) haben auch einen Nebeneffekt: Wenn sie verwendet werden, `@ExtendedProperties` gibt Kusto den aktuellen Wert des **Datenbankcursors** an das Resultset der Abfrage aus. Der Eigenschaftsname für `Cursor`den Cursor ist `string`, und sein Wert ist ein einzelner . Beispiel:

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>Beschränkungen

Datenbankcursor können nur für Tabellen verwendet werden, für die die [IngestionTime-Richtlinie](ingestiontime-policy.md) aktiviert wurde. Jeder Datensatz in einer solchen Tabelle ist mit dem Wert des Datenbankcursors verknüpft, der bei der Aufnahme des Datensatzes wirksam war, und daher kann die [ingestion_time()-Funktion](../query/ingestiontimefunction.md) verwendet werden.

Das Datenbankcursorobjekt enthält keinen aussagekräftigen Wert, es sei denn, die Datenbank verfügt über mindestens eine Tabelle, für die eine [IngestionTime-Richtlinie](ingestiontime-policy.md) definiert ist.
Darüber hinaus ist nur gewährleistet, dass dieser Wert nach Bedarf durch den Erfassungsverlauf in solche Tabellen aktualisiert wird und die Abfragen ausgeführt werden, die auf solche Tabellen verweisen. In anderen Fällen kann es aktualisiert werden oder nicht.

Der Erfassungsprozess überträgt zuerst die Daten (so dass sie für Abfragen verfügbar sind) und weist jedem Datensatz erst dann einen tatsächlichen Cursorwert zu. Dies bedeutet, dass, wenn man versucht, Daten unmittelbar nach dem Abschluss der Aufnahme mit einem Datenbankcursor abzufragen, die Ergebnisse möglicherweise noch nicht die letzten hinzugefügten Datensätze enthalten, da ihnen der Cursorwert noch nicht zugewiesen wurde. Ebenso kann das wiederholte Abrufen des aktuellen Datenbankcursorwerts denselben Wert zurückgeben (auch wenn die Aufnahme zwischendurch erfolgt ist), da nur cursor commit seinen Wert aktualisiert.

## <a name="example-processing-of-records-exactly-once"></a>Beispiel: Bearbeitung von Datensätzen genau einmal

Angenommen `Employees` Tabelle `[Name, Salary]`mit Schema .
Um neue Datensätze kontinuierlich zu verarbeiten, während sie in die Tabelle aufgenommen werden, gehen Sie wie folgt vor:

```kusto
// [Once] Enable the IngestionTime policy on table Employees
.set table Employees policy ingestiontime true

// [Once] Get all the data that the Employees table currently holds 
Employees | where cursor_after('')

// The query above will return the database cursor value in
// the @ExtendedProperties result set. Lets assume that it returns
// the value '636040929866477946'

// [Many] Get all the data that was added to the Employees table
// since the previous query was run using the previously-returned
// database cursor 
Employees | where cursor_after('636040929866477946') // -> 636040929866477950

Employees | where cursor_after('636040929866477950') // -> 636040929866479999

Employees | where cursor_after('636040929866479999') // -> 636040939866479000
```