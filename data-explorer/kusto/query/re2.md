---
title: Reguläre Ausdrücke - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden reguläre Ausdrücke in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0bc5dc6705d6cada446716f2f9d84618322ecd76
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510522"
---
# <a name="regular-expressions"></a>Reguläre Ausdrücke

Die Syntax des regulären RE2-Elements beschreibt die Syntax der Bibliothek für reguläre Ausdrücke, die von Kusto (re2) verwendet wird.
Es gibt einige Funktionen in Kusto, die Zeichenfolgenabgleich, Auswahl und Extraktion mithilfe eines regulären Ausdrucks durchführen

- [countof()](countoffunction.md)
- [Auszug()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse-Operator](parseoperator.md)
- [ersetzen()](replacefunction.md)
- [trim()](trimfunction.md)
- [trimend()](trimendfunction.md)
- [trimstart()](trimstartfunction.md)

Die von Kusto unterstützte Syntax für reguläre Ausdrücke ist die der [re2-Bibliothek](https://github.com/google/re2/wiki/Syntax)und wird unten beschrieben. Beachten Sie, dass diese Ausdrücke in `string` Kusto als Literale codiert werden müssen und alle Zeichenfolgen-Zitatregeln von Kusto gelten. Der reguläre Ausdruck `\A` entspricht z. B. dem Anfang einer Zeile (siehe Tabelle unten) und wird in Kusto als Zeichenfolgenliteral `"\\A"` angegeben (beachten Sie das Zeichen "extra" backslash (`\`).)

