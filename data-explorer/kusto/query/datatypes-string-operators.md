---
title: Zeichenfolgenoperatoren - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Zeichenfolgenoperatoren in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91955ef5877e6c054917f70c655fc4b7cd3f277b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516472"
---
# <a name="string-operators"></a>Zeichenfolgenoperatoren

Die folgende Tabelle fasst Operatoren für Zeichenfolgen zusammen:

Operator        |BESCHREIBUNG                                                       |Groß-/Kleinschreibung|Beispiel (ergibt `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Equals                                                            |Ja           |`"aBc" == "aBc"`
`!=`            |Not Equals                                                        |Ja           |`"abc" != "ABC"`
`=~`            |Equals                                                            |Nein            |`"abc" =~ "ABC"`
`!~`            |Not Equals                                                        |Nein            |`"aBc" !~ "xyz"`
`has`           |Rechte Seite (RS) ist ein ganzer Begriff innerhalb der linken Seite (LS)     |Nein            |`"North America" has "america"`
`!has`          |RS ist kein vollständiger Begriff innerhalb der LS                                     |Nein            |`"North America" !has "amer"` 
`has_cs`        |Rechte Seite (RS) ist ein ganzer Begriff innerhalb der linken Seite (LS)     |Ja           |`"North America" has_cs "America"`
`!has_cs`       |RS ist kein vollständiger Begriff innerhalb der LS                                     |Ja           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS ist ein Begriffspräfix in LHS                                       |Nein            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS ist kein Begriffspräfix in LHS                                   |Nein            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS ist ein Begriffspräfix in LHS                                       |Ja           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS ist kein Begriffspräfix in LHS                                   |Ja           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS ist ein Begriffsuffix in LHS                                       |Nein            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS ist kein Begriffsuffix in LHS                                   |Nein            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS ist ein Begriffsuffix in LHS                                       |Ja           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS ist kein Begriffsuffix in LHS                                   |Ja           |`"North America" !hassuffix_cs "icA"`
`contains`      |RS tritt als Untersequenz der LS auf                                |Nein            |`"FabriKam" contains "BRik"`
`!contains`     |RS tritt nicht in LS auf                                         |Nein            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RS tritt als Untersequenz der LS auf                                |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RS tritt nicht in LS auf                                         |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Nein            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS ist keine anfängliche Folge von LHS                          |Nein            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Ja           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS ist keine anfängliche Folge von LHS                          |Ja           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Nein            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS ist keine schließende Folge von LHS                           |Nein            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Ja           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS ist keine schließende Folge von LHS                           |Ja           |`"Fabrikam" !endswith "brik"`
`matches regex` |LS enthält eine Übereinstimmung für RS                                      |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`            |Entspricht einem der Elemente                                     |Ja           |`"abc" in ("123", "345", "abc")`
`!in`           |Entspricht keinem der Elemente                                 |Ja           |`"bca" !in ("123", "345", "abc")`
`in~`           |Entspricht einem der Elemente                                     |Nein            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |Entspricht keinem der Elemente                                 |Nein            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |Wie `has` bei einem der Elemente funktioniert                    |Nein            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>Leistungstipps

Um eine bessere Leistung zu erzielen, verwenden Sie die Groß-/Kleinschreibung, wenn zwei Operatoren dieselbe Aufgabe ausführen.
Beispiel:

* anstelle `=~`von verwenden`==`
* anstelle `in~`von verwenden`in`
* anstelle `contains`von verwenden`contains_cs`

Wenn Sie das Vorhandensein eines Symbols oder alphanumerischen Wortes testen, das durch nicht-alphanumerische Zeichen (oder `has` den `in`Anfang oder das Ende eines Felds) gebunden ist, verwenden Sie oder . `has`führt schneller `contains` `startswith`als `endswith`, oder .

Die erste dieser Abfragen wird z. B. schneller ausgeführt:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>Grundlegendes zu Zeichenfolgenbegriffen

Standardmäßig indiziert Kusto alle Spalten, `string`einschließlich Spalten vom Typ .
Tatsächlich werden mehrere Indizes für solche Spalten erstellt, abhängig von den tatsächlichen Daten. Dem Benutzer werden diese Indizes nicht direkt verfügbar gemacht (mit Ausnahme ihrer positiven Auswirkungen `string` auf `has` die Abfrageleistung natürlich), mit Ausnahme der Operatoren, die als Teil ihres Namens verfügen: `has`, `!has`, `hasprefix`, `!hasprefix`, usw. Diese Operatoren sind insofern besonders, als ihre Semantik durch die Art und Weise diktiert wird, wie die Spalte codiert wird; Anstatt eine "einfache" Teilzeichenfolgezuspielung zu führen, stimmen diese Operatoren mit **den Begriffen**überein.

Um begriffsbasierte Übereinstimmung zu verstehen, muss man zuerst verstehen, was ein Begriff ist. Standardmäßig teilt Kusto `string` jeden Wert in maximale Sequenzen von alphanumerischen ASCII-Zeichen auf, und jedes dieser Werte wird in einen Begriff umgewandelt. Im `string`Folgenden sind die Begriffe `Kusto`z. B. , `WilliamGates3rd` `ad67d136`und `c1db` `4f9f`die `88ef` `d94f3b6b0b5a`folgenden Teilzeichenfolgen: , , , , :

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Standardmäßig erstellt Kusto einen Begriffsindex, der aus allen Begriffen besteht, `has`die `!has`aus vier oder mehr Zeichen bestehen, und dieser Index wird von verwendet, usw., wenn Begriffe nachgesucht werden, die ebenfalls vier Zeichen oder mehr sind. (Wenn die Abfrage nach einem Begriff sucht, der `contains` kleiner als vier Zeichen ist, oder z. B. einen Operator verwendet, kehrt Kusto zum Scannen der Werte in der Spalte zurück, wenn sie keine Übereinstimmung ermitteln kann, die viel langsamer ist als das Nachsehen des Begriffs im Termindex.)

