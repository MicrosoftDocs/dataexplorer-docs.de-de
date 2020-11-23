---
title: 'Entitäts Namen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden die Entitäts Namen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: a155accc2072e59ef74a1f5f39ff33e652493666
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324345"
---
# <a name="entity-names"></a>Entitätsnamen

Kusto-Entitäten (Datenbanken, Tabellen, Spalten und gespeicherte Funktionen, Cluster sind eine Ausnahme) werden benannt. Der Name einer Entität identifiziert die Entität und ist garantiert im Gültigkeitsbereich ihres Containers eindeutig, wenn der Typ angegeben ist.
(So können z. b. zwei Tabellen in derselben Datenbank nicht denselben Namen haben, aber eine Tabelle und eine Datenbank haben möglicherweise denselben Namen, da Sie sich nicht im gleichen Bereich befinden, und eine Tabelle und eine gespeicherte Funktion können denselben Namen haben, da Sie nicht denselben Entitätstyp aufweisen.)

Beim Auflösen von Entitäts Namen wird die **Groß-/Kleinschreibung** beachtet (Sie können z. b. nicht auf eine Tabelle mit dem Namen `ThisTable` As verweisen `thisTABLE` ).

Entitäts Namen sind ein Beispiel **für** Bezeichner. Andere Bezeichner enthalten die Namen von Parametern für Funktionen und die Bindung eines Namens über eine [Let-Anweisung](../letstatement.md).

## <a name="entity-pretty-names"></a>Entitäts Namen

Einige Entitäten (z. b. Datenbanken) haben möglicherweise zusätzlich zu Ihrem Entitäts Namen einen **sehr Namen**. Zum Verweisen auf die Entität in Abfragen (z. b. Entitäts Namen) können ganz Namen verwendet werden, aber im Gegensatz zu Entitäts Namen sind ganz Namen im Kontext ihres Containers nicht notwendigerweise eindeutig. Wenn ein Container mehrere Entitäten mit dem gleichen Namen aufweist, kann der-Name nicht verwendet werden, um auf die Entität zu verweisen.

Mit hübschen Namen können Anwendungen der mittleren Ebene automatisch Erstellungs Entitäts Namen (z. b. UUIDs) zu Namen zuordnen, die für Anzeige-und Verweis Zwecke Menschen lesbar sind.

## <a name="identifier-naming-rules"></a>Bezeichnerbenennungs Regeln

Bezeichner werden verwendet, um verschiedene Entitäten (Entitäten oder anderweitig) zu benennen.
Gültige Bezeichnernamen befolgen die folgenden Regeln:
* Sie haben zwischen 1 und 1024 Zeichen lang.
* Sie können Buchstaben, Ziffern, Unterstriche ( `_` ), Leerzeichen, Punkte ( `.` ) und Bindestriche ( `-` ) enthalten.
  * Bezeichner, die nur aus Buchstaben, Ziffern und unterstrichen bestehen, erfordern keine Anführungszeichen, wenn auf den Bezeichner verwiesen wird.
  * Bezeichner, die den letzten Wert (Leerzeichen, Punkte oder Bindestriche) enthalten, erfordern Anführungszeichen (siehe unten).
* Dabei wird die Groß-/Kleinschreibung beachtet.

## <a name="identifier-quoting"></a>Bezeichnerzitat

Bezeichner, die mit einigen Schlüsselwörtern für die Abfragesprache identisch sind oder eines der oben genannten Sonderzeichen aufweisen, erfordern Anführungszeichen, wenn direkt durch eine Abfrage auf Sie verwiesen wird:

|Abfragetext         |Kommentare                          |
|-------------------|----------------------------------|
| `entity`          |Entitäts Namen ( `entity` ), die keine Sonderzeichen enthalten oder einem sprach Schlüsselwort zugeordnet sind, erfordern keine Anführungszeichen.|
|`['entity-name']`  |Entitäts Namen, die Sonderzeichen (hier:) enthalten, `-` müssen mit `['` und `']` oder `["` mit und angegeben werden. `"]`|
|`["where"]`        |Entitäts Namen, die sprach Schlüsselwörter sind, müssen in Anführungszeichen mit `['` und `']` oder mit `["` und angegeben werden `"]`|

## <a name="naming-your-entities-to-avoid-collisions-with-kusto-language-keywords"></a>Benennen von Entitäten zur Vermeidung von Konflikten mit Schlüsselwörtern der Kusto-Sprache

Da die Kusto-Abfragesprache eine Reihe von Schlüsselwörtern enthält, die die gleichen Benennungs Regeln wie Bezeichner aufweisen, ist es möglich, Entitäts Namen zu haben, die tatsächlich Schlüsselwörter sind, aber das verweisen auf diese Namen ist schwierig (eine muss Sie angeben).

Alternativ dazu können Sie auch Entitäts Namen auswählen, die garantiert nie mit einem Kusto-Schlüsselwort "kollidieren". Die folgenden Garantien werden vorgenommen:

1. Die Kusto-Abfragesprache definiert kein Schlüsselwort, das mit einem Großbuchstaben ( `A` für `Z` ) beginnt.
2. Die Kusto-Abfragesprache definiert kein Schlüsselwort, das mit einem einzelnen Unterstrich ( `_` ) beginnt.
3. Die Kusto-Abfragesprache definiert kein Schlüsselwort, das mit einem einzelnen Unterstrich ( `_` ) endet.

Die Kusto-Abfragesprache reserviert alle Bezeichner, die mit einer Sequenz von zwei unterstrichen () beginnen oder enden `__` . Benutzer können solche Namen nicht zur eigenen Verwendung definieren.








