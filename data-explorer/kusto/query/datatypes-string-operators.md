---
title: 'Zeichen folgen Operatoren: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden Zeichen folgen Operatoren in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e16d90c8307392536b5971758ce7df15a9ea1b46
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977132"
---
# <a name="string-operators"></a>Zeichenfolgenoperatoren

In der folgenden Tabelle werden Operatoren für Zeichen folgen zusammengefasst

Operator        |BESCHREIBUNG                                                       |Groß-/Kleinschreibung|Beispiel (ergibt `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Equals                                                            |Ja           |`"aBc" == "aBc"`
`!=`            |Not Equals                                                        |Ja           |`"abc" != "ABC"`
`=~`            |Equals                                                            |Nein            |`"abc" =~ "ABC"`
`!~`            |Not Equals                                                        |Nein            |`"aBc" !~ "xyz"`
`has`           |Rechte Seite (RS) ist ein ganzer Begriff innerhalb der linken Seite (LS)     |Nein             |`"North America" has "america"`
`!has`          |RS ist kein vollständiger Begriff innerhalb der LS                                     |Nein             |`"North America" !has "amer"` 
`has_cs`        |Rechte Seite (RS) ist ein ganzer Begriff innerhalb der linken Seite (LS)     |Ja           |`"North America" has_cs "America"`
`!has_cs`       |RS ist kein vollständiger Begriff innerhalb der LS                                     |Ja           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS ist ein Begriffs Präfix in LHS                                       |Nein             |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS ist kein Begriffs Präfix in LHS                                   |Nein             |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS ist ein Begriffs Präfix in LHS                                       |Ja           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS ist kein Begriffs Präfix in LHS                                   |Ja           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS ist ein Begriff Suffix in LHS                                       |Nein             |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS ist kein Begriffs Suffix in LHS                                   |Nein             |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS ist ein Begriff Suffix in LHS                                       |Ja           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS ist kein Begriffs Suffix in LHS                                   |Ja           |`"North America" !hassuffix_cs "icA"`
`contains`      |RS tritt als Untersequenz der LS auf                                |Nein             |`"FabriKam" contains "BRik"`
`!contains`     |RS tritt nicht in LS auf                                         |Nein             |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RS tritt als Untersequenz der LS auf                                |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RS tritt nicht in LS auf                                         |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Nein             |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS ist keine anfängliche unter Sequenz von LHS                          |Nein             |`"Fabrikam" !startswith "kam"`
`startswith_cs` |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Ja           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS ist keine anfängliche unter Sequenz von LHS                          |Ja           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Nein             |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS ist keine schließende unter Sequenz von LHS                           |Nein             |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Ja           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS ist keine schließende unter Sequenz von LHS                           |Ja           |`"Fabrikam" !endswith "brik"`
`matches regex` |LS enthält eine Übereinstimmung für RS                                      |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`            |Entspricht einem der Elemente                                     |Ja           |`"abc" in ("123", "345", "abc")`
`!in`           |Entspricht keinem der Elemente                                 |Ja           |`"bca" !in ("123", "345", "abc")`
`in~`           |Entspricht einem der Elemente                                     |Nein             |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |Entspricht keinem der Elemente                                 |Nein             |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |Identisch mit `has` , funktioniert aber an einem der Elemente                    |Nein             |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>Leistungstipps

Verwenden Sie für eine bessere Leistung bei zwei Operatoren, die dieselbe Aufgabe ausführen, die Groß-/Kleinschreibung.
Beispiel:

* Verwenden Sie `=~`anstelle von`==`
* Verwenden Sie `in~`anstelle von`in`
* Verwenden Sie `contains`anstelle von`contains_cs`

Wenn Sie das vorhanden sein eines Symbols oder eines alphanumerischen Worts testen möchten, das durch nicht-alphanumerische Zeichen (oder den Anfang oder das Ende eines Felds) gebunden ist, verwenden `has` Sie oder. `in` `has`führt eine schnellere `contains`Leistung `startswith`als, `endswith`oder aus.

Beispielsweise wird die erste dieser Abfragen schneller ausgeführt:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>Verstehen von Zeichen folgen Begriffen

Standardmäßig indiziert Kusto alle Spalten, einschließlich Spalten vom Typ `string`.
Tatsächlich werden für diese Spalten mehrere Indizes erstellt, abhängig von den tatsächlichen Daten. Für den Benutzer werden diese Indizes nicht direkt verfügbar gemacht (mit Ausnahme ihrer positiven Auswirkung auf die Abfrage `string` Leistung), mit Ausnahme der Operatoren, die `has` als Teil Ihres Namens vorliegen: `has`, `!has`, `hasprefix`, `!hasprefix`usw. Diese Operatoren sind ein besonderes Zeichen dafür, dass ihre Semantik durch die Art der Codierung der Spalte vorgegeben ist. anstatt eine "einfache" Teil Zeichenfolge abzugleichen, entsprechen diese Operatoren **den Begriffen**.

Zum besseren Verständnis der Begriffs basierten Übereinstimmung muss zunächst ein grundlegendes Verständnis der Begriffe sein. Standardmäßig wird jeder `string` Wert von Kusto in maximale Folge von alphanumerischen ASCII-Zeichen umgewandelt, die jeweils in einen Begriff umgewandelt werden. Im folgenden `string`sind `Kusto`die Begriffe z. b., `WilliamGates3rd`und die folgenden Teil Zeichenfolgen: `ad67d136`, `c1db`, `4f9f`, `88ef`, `d94f3b6b0b5a`:

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Standardmäßig erstellt Kusto einen Begriffs Index, der aus allen Begriffen mit **vier oder mehr Zeichen**besteht, und dieser Index wird von `has`, `!has`usw. verwendet, wenn Begriffe gesucht werden, die auch aus vier Zeichen bestehen. (Wenn die Abfrage nach einem Begriff sucht, der kleiner als vier Zeichen ist, oder wie `contains` z. b. einen Operator verwendet, kehrt Kusto das Scannen der Werte in der Spalte zurück, wenn keine Entsprechung festgestellt werden kann, was weitaus langsamer ist als der Begriff im Begriff Index nach oben.)

