---
title: Entitätsnamen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Entitätsnamen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 2fecbd538a53d8eb02e55b0bd1def2186467b019
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509417"
---
# <a name="entity-names"></a>Entitätsnamen

Kusto-Entitäten (Datenbanken, Tabellen, Spalten und gespeicherte Funktionen; Cluster sind eine Ausnahme) werden benannt. Der Name einer Entität identifiziert die Entität und ist aufgrund ihres Typs garantiert eindeutig im Bereich ihres Containers.
(Z. B. können zwei Tabellen in derselben Datenbank nicht denselben Namen haben, aber eine Tabelle und eine Datenbank haben möglicherweise denselben Namen, da sie nicht im gleichen Bereich liegen, und eine Tabelle und eine gespeicherte Funktion haben möglicherweise denselben Namen, da sie nicht vom gleichen Entitätstyp sind.)

Entitätsnamen werden bei Der enmitierenden Zwecken **berücksichtigt** (sie können also `ThisTable` `thisTABLE`z. B. nicht auf eine Tabelle mit dem Namen verweisen).

Entitätsnamen sind ein Beispiel für **Bezeichner**. Andere Bezeichner umfassen die Namen von Parametern an Funktionen und das Binden eines Namens durch eine [let-Anweisung](../letstatement.md).

## <a name="entity-pretty-names"></a>Entität hübsche Namen

Einige Entitäten (z. B. Datenbanken) haben möglicherweise zusätzlich zu ihrem Entitätsnamen einen **hübschen Namen**. Hübsche Namen können verwendet werden, um die Entität in Abfragen (z. B. Entitätsnamen) zu referenzieren, aber im Gegensatz zu Entitätsnamen sind hübsche Namen nicht unbedingt im Kontext ihres Containers eindeutig. Wenn ein Container über mehrere Entitäten mit demselben hübschen Namen verfügt, kann der hübsche Name nicht verwendet werden, um auf die Entität zu verweisen.

Hübsche Namen ermöglichen es Anwendungen der mittleren Ebene, Entitätsnamen (z. B. UUIDs) automatisch zu Namen zuzuordnen, die für Anzeige- und Referenzzwecke lesbar sind.

## <a name="identifier-naming-rules"></a>Bezeichnerbenennungsregeln

<!-- TODO: This section should be reviewed and moved to its own page -->

Bezeichner werden verwendet, um verschiedene Entitäten (Entitäten oder auf andere Weise) zu benennen.
Gültige Bezeichnernamen folgen diesen Regeln:
* Sie haben zwischen 1 und 1024 Zeichen lang.
* Sie können Buchstaben, Ziffern, Unterstriche (`_`),`.`Leerzeichen, Punkte`-`( ) und Bindestriche ( ) enthalten.
  * Bezeichner, die nur aus Buchstaben, Ziffern und Unterstrichen bestehen, erfordern kein Annoten, wenn auf den Bezeichner verwiesen wird.
  * Bezeichner, die endlich einen von (Leerzeichen, Punkte oder Bindestriche) enthalten, erfordern ein Zitieren (siehe unten).
* Sie sind großfeines Beispiel.

## <a name="identifier-quoting"></a>Identifier-Zitat

Bezeichner, die mit einigen Schlüsselwörtern in der Abfragesprache identisch sind oder über eines der oben genannten Sonderzeichen verfügen, müssen zitiert werden, wenn sie direkt von einer Abfrage referenziert werden:

|Abfragetext         |Kommentare                          |
|-------------------|----------------------------------|
| `entity`          |Entitätsnamen`entity`( ), die keine Sonderzeichen oder Zuordnungzuzeichen zu einem Sprachschlüsselwort enthalten, erfordern kein Anführen|
|`['entity-name']`  |Entitätsnamen, die Sonderzeichen `-`enthalten (hier: `']` ) `["` müssen mit `['` und oder mit`"]`|
|`["where"]`        |Entitätsnamen, bei denen es sich `['` `']` um `["` Sprachschlüsselwörter handelt, müssen mit`"]`|

## <a name="naming-your-entities-to-avoid-collisions-with-kusto-language-keywords"></a>Benennen ihrer Entitäten, um Kollisionen mit Kusto-Sprachschlüsselwörtern zu vermeiden

Da die Kusto-Abfragesprache eine Reihe von Schlüsselwörtern enthält, die die gleichen Namensregeln wie Bezeichner haben, ist es möglich, Entitätsnamen zu haben, die eigentlich Schlüsselwörter sind, aber dann wird das Verweisen auf diese Namen schwierig (man muss sie zitieren).

Alternativ kann man Entitätsnamen auswählen, die garantiert nie mit einem Kusto-Schlüsselwort "kollidieren". Folgende Garantien werden gegeben:

1. Die Kusto-Abfragesprache definiert kein Schlüsselwort, das`A` `Z`mit einem Großbuchstaben ( zu ) beginnt.
2. Die Kusto-Abfragesprache definiert kein Schlüsselwort, das`_`mit einem einzelnen Unterstrich ( ) beginnt.
3. Die Kusto-Abfragesprache definiert kein Schlüsselwort, das`_`mit einem einzelnen Unterstrich endet ( ).

Die Kusto-Abfragesprache reserviert alle Bezeichner, die mit einer`__`Sequenz von zwei Unterstrichzeichen beginnen oder enden ( ); Benutzer können solche Namen nicht für ihren eigenen Gebrauch definieren.








