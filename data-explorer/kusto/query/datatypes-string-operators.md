---
title: 'Zeichen folgen Operatoren: Azure Daten-Explorer'
description: In diesem Artikel werden Zeichen folgen Operatoren in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.openlocfilehash: 1c394d9125bd354cab21d3087c273f983627a0d6
ms.sourcegitcommit: c351c2c8ab6e184827c4702eb0ec8bf783c7bbd3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874779"
---
# <a name="string-operators"></a>Zeichenfolgenoperatoren

Kusto bietet eine Vielzahl von Abfrage Operatoren zum Durchsuchen von Zeichen folgen Datentypen. Im folgenden Artikel wird beschrieben, wie Zeichen folgen Begriffe indiziert werden, die Zeichen folgen Abfrage Operatoren aufgelistet werden und Tipps zur Leistungsoptimierung erhalten.

## <a name="understanding-string-terms"></a>Verstehen von Zeichen folgen Begriffen

Kusto indiziert alle Spalten, einschließlich Spalten vom Typ `string` . Abhängig von den eigentlichen Daten werden mehrere Indizes für diese Spalten erstellt. Diese Indizes sind nicht direkt verfügbar, werden jedoch in Abfragen mit den `string` Operatoren verwendet, die `has` als Teil Ihres Namens enthalten sind, z `has` . b.,, `!has` `hasprefix` , `!hasprefix` . Die Semantik dieser Operatoren wird durch die Art der Codierung der Spalte vorgegeben. Anstatt eine "einfache" Teil Zeichenfolge abzugleichen, entsprechen diese Operatoren *den Begriffen*.

### <a name="what-is-a-term"></a>Was ist ein Begriff? 

Standardmäßig wird jeder `string` Wert in maximale Sequenz von alphanumerischen ASCII-Zeichen unterteilt, und jede dieser Sequenzen wird in einen Begriff umgewandelt.
Im folgenden sind die Begriffe z. b. `string` `Kusto` , `WilliamGates3rd` und die folgenden Teil Zeichenfolgen: `ad67d136` , `c1db` , `4f9f` , `88ef` , `d94f3b6b0b5a` .

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto erstellt einen Begriffs Index, der aus allen Begriffen mit *vier oder mehr Zeichen* besteht, und dieser Index wird von `has` , `!has` usw. verwendet. Wenn die Abfrage nach einem Begriff sucht, der kleiner als vier Zeichen ist, oder einen `contains` Operator verwendet, kehrt Kusto das Scannen der Werte in der Spalte wieder, wenn keine Entsprechung ermittelt werden kann. Diese Methode ist viel langsamer als der Begriff im Begriffs Index.

## <a name="operators-on-strings"></a>Operatoren für Zeichen folgen

> [!NOTE]
> In der folgenden Tabelle werden die folgenden Abkürzungen verwendet:
> * RHS = Rechte Seite des Ausdrucks
> * LHS = linke Seite des Ausdrucks
> 
> Bei Operatoren mit einem `_cs` Suffix wird Groß-/Kleinschreibung

Operator        |BESCHREIBUNG                                                       |Groß-/Kleinschreibung|Beispiel (ergibt `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Equals                                                            |Ja           |`"aBc" == "aBc"`
`!=`            |Not Equals                                                        |Ja           |`"abc" != "ABC"`
`=~`            |Equals                                                            |Nein            |`"abc" =~ "ABC"`
`!~`            |Not Equals                                                        |Nein            |`"aBc" !~ "xyz"`
`has`           |Rechte Seite (RS) ist ein ganzer Begriff innerhalb der linken Seite (LS)     |Nein             |`"North America" has "america"`
`!has`          |RHS ist kein vollständiger Begriff in LHS                                     |Nein             |`"North America" !has "amer"` 
`has_cs`        |RHS ist ein ganzer Begriff in LHS                                        |Ja           |`"North America" has_cs "America"`
`!has_cs`       |RHS ist kein vollständiger Begriff in LHS                                     |Ja           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS ist ein Begriffs Präfix in LHS                                       |Nein             |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS ist kein Begriffs Präfix in LHS                                   |Nein             |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS ist ein Begriffs Präfix in LHS                                       |Ja           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS ist kein Begriffs Präfix in LHS                                   |Ja           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS ist ein Begriff Suffix in LHS                                       |Nein             |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS ist kein Begriffs Suffix in LHS                                   |Nein             |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS ist ein Begriff Suffix in LHS                                       |Ja           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS ist kein Begriffs Suffix in LHS                                   |Ja           |`"North America" !hassuffix_cs "icA"`
`contains`      |Rechte Seite kommt als Teilsequenz von linker Seite vor                                |Nein             |`"FabriKam" contains "BRik"`
`!contains`     |Rechte Seite kommt auf linker Seite nicht vor                                         |Nein             |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Rechte Seite kommt als Teilsequenz von linker Seite vor                                |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Rechte Seite kommt auf linker Seite nicht vor                                         |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Nein             |`"Fabrikam" startswith "fab"`
`!startswith`   |Rechte Seite ist keine öffnende Teilsequenz von linker Seite                          |Nein             |`"Fabrikam" !startswith "kam"`
`startswith_cs` |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Ja           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|Rechte Seite ist keine öffnende Teilsequenz von linker Seite                          |Ja           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Nein             |`"Fabrikam" endswith "Kam"`
`!endswith`     |Rechte Seite ist keine schließende Teilsequenz von linker Seite                           |Nein             |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Ja           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |Rechte Seite ist keine schließende Teilsequenz von linker Seite                           |Ja           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |Linke Seite enthält eine Übereinstimmung für rechte Seite                                      |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`            |Entspricht einem der Elemente                                     |Ja           |`"abc" in ("123", "345", "abc")`
`!in`           |Entspricht keinem der Elemente                                 |Ja           |`"bca" !in ("123", "345", "abc")`
`in~`           |Entspricht einem der Elemente                                     |Nein             |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |Entspricht keinem der Elemente                                 |Nein             |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |Identisch `has` mit, funktioniert aber an einem der Elemente                    |Nein             |`"North America" has_any("south", "north")`

> [!TIP]
> Alle Operatoren `has` , die die Suche nach indizierten *Begriffen* mit vier oder mehr Zeichen und nicht mit Teil Zeichenfolgen-Übereinstimmungen enthalten. Ein Begriff wird erstellt, indem die Zeichenfolge in Sequenzen von alphanumerischen ASCII-Zeichen unterteilt wird. Siehe Grundlegendes zu [Zeichen folgen Begriffen](#understanding-string-terms).

## <a name="performance-tips"></a>Leistungstipps

Verwenden Sie für eine bessere Leistung bei zwei Operatoren, die dieselbe Aufgabe ausführen, die Groß-/Kleinschreibung.
Beispiel:

* Verwenden Sie anstelle von `=~``==`
* Verwenden Sie anstelle von `in~``in`
* Verwenden Sie anstelle von `contains``contains_cs`

Wenn Sie das vorhanden sein eines Symbols oder eines alphanumerischen Worts, das durch nicht-alphanumerische Zeichen gebunden ist, oder durch den Anfang oder das Ende eines Felds testen möchten, verwenden Sie `has` oder `in` . 
`has` funktioniert schneller als `contains` , `startswith` oder `endswith` .

Beispielsweise wird die erste dieser Abfragen schneller ausgeführt:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
