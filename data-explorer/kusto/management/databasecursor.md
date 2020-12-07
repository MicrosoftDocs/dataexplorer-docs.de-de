---
title: 'Datenbankcursorn: Azure Daten-Explorer'
description: Dieser Artikel beschreibt Datenbankcursorn in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a3deb388c5a57f3400eb5fbe24f77a31e48b69c
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303330"
---
# <a name="database-cursors"></a>Datenbankcursor

Ein **Daten Bank Cursor** ist ein Objekt auf Datenbankebene, mit dem Sie eine Datenbank mehrmals Abfragen können. Sie erhalten auch dann konsistente Ergebnisse, wenn- `data-append` oder- `data-retention` Vorgänge parallel zu den Abfragen ausgeführt werden.

Datenbankcursorn sind für zwei wichtige Szenarien konzipiert:

* Die Möglichkeit, dieselbe Abfrage mehrmals zu wiederholen und die gleichen Ergebnisse zu erhalten, solange die Abfrage "dasselbe Dataset" angibt.

* Die Möglichkeit, eine "genau einmal"-Abfrage zu erstellen. Diese Abfrage "sieht" nur die Daten, die von einer vorherigen Abfrage nicht angezeigt wurden, weil die Daten nicht verfügbar waren.
   Mit der Abfrage können Sie z. b. alle neu eingegangenen Daten in einer Tabelle durchlaufen, ohne zu befürchten, dass derselbe Datensatz zweimal verarbeitet oder Datensätze versehentlich übersprungen werden.

Der Daten Bank Cursor wird in der Abfragesprache als Skalarwert des Typs dargestellt `string` . Der tatsächliche Wert sollte als nicht transparent angesehen werden, und es gibt keine Unterstützung für andere Vorgänge als zum Speichern des Werts oder zum Verwenden der unten aufgeführten Cursor Funktionen.

## <a name="cursor-functions"></a>Cursor Funktionen

Kusto bietet drei Funktionen, die die Implementierung der beiden oben genannten Szenarien unterstützen:

* [cursor_current ()](../query/cursorcurrent.md): Verwenden Sie diese Funktion, um den aktuellen Wert des Daten Bank Cursors abzurufen.
   Sie können diesen Wert als Argument für die beiden anderen Funktionen verwenden.
   Diese Funktion verfügt auch über das Synonym `current_cursor()` .

* [cursor_after (RHS: String)](../query/cursorafterfunction.md): diese spezielle Funktion kann für Tabellendaten Sätze verwendet werden, für die die [ingestiontime-Richtlinie](ingestiontime-policy.md) aktiviert ist. Es gibt einen Skalarwert des Typs zurück, der `bool` angibt, ob der `ingestion_time()` Daten Bank Cursor Wert des Datensatzes nach dem `rhs` Wert des Daten Bank Cursors liegt.

* [cursor_before_or_at (RHS: String)](../query/cursorbeforeoratfunction.md): diese spezielle Funktion kann für die Tabellendaten Sätze verwendet werden, für die die [ingestiontime-Richtlinie](ingestiontime-policy.md) aktiviert ist. Es gibt einen Skalarwert des Typs zurück, der `bool` angibt, ob der `ingestion_time()` Daten Bank Cursor Wert des Datensatzes vor oder am `rhs` Daten Bank Cursor Wert liegt.

Die beiden speziellen Funktionen ( `cursor_after` und `cursor_before_or_at` ) haben ebenfalls einen Nebeneffekt: Wenn Sie verwendet werden, gibt Kusto den **aktuellen Wert des Daten Bank Cursors** an das `@ExtendedProperties` Resultset der Abfrage aus. Der Eigenschaftsname für den Cursor ist `Cursor` , und sein Wert ist eine einzelne `string` . 

Beispiel:

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>Beschränkungen

Datenbankcursorn können nur mit Tabellen verwendet werden, für die die [ingestiontime-Richtlinie](ingestiontime-policy.md) aktiviert wurde. Jeder Datensatz in einer solchen Tabelle ist mit dem Wert des Daten Bank Cursors verknüpft, der beim Erfassen des Datensatzes wirksam war.
Daher kann die [ingestion_time ()](../query/ingestiontimefunction.md) -Funktion verwendet werden.

Das Daten Bank Cursor Objekt enthält keinen sinnvollen Wert, es sei denn, die Datenbank enthält mindestens eine Tabelle, für die eine [ingestiontime-Richtlinie](ingestiontime-policy.md) definiert ist.
Dieser Wert wird garantiert, wenn er vom Erfassungs Verlauf benötigt wird, in Tabellen und die Abfragen ausgeführt, die auf diese Tabellen verweisen. In anderen Fällen wird dies möglicherweise aktualisiert.

Der Erfassungsprozess führt zuerst einen Commit für die Daten aus, sodass er für Abfragen verfügbar ist, und weist nur dann jedem Datensatz einen tatsächlichen Cursor Wert zu. Wenn Sie versuchen, Daten unmittelbar nach dem Erfassungs Abschluss mithilfe eines Daten Bank Cursors abzufragen, enthalten die Ergebnisse möglicherweise noch nicht die letzten hinzugefügten Datensätze, da Ihnen noch nicht der Cursor Wert zugewiesen wurde. Außerdem kann das Abrufen des aktuellen Daten Bank Cursor Werts wiederholt denselben Wert zurückgeben, auch wenn die Erfassung dazwischen erfolgt ist, da nur ein Cursor Commit seinen Wert aktualisieren kann.

## <a name="example-processing-records-exactly-once"></a>Beispiel: genaue Verarbeitung von Datensätzen

Verwenden Sie für eine Tabelle `Employees` mit Schema `[Name, Salary]` den folgenden Prozess, um neue Datensätze fortlaufend zu verarbeiten, wenn Sie in der Tabelle erfasst werden:

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
