---
title: 'Reguläre Ausdrücke: Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden reguläre Ausdrücke in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513079"
---
# <a name="re2-syntax"></a>RE2-Syntax

Die RE2-Syntax für reguläre Ausdrücke beschreibt die Syntax der Bibliothek für reguläre Ausdrücke, die von Kusto verwendet wird (re2).
Es gibt einige Funktionen in Kusto, die Zeichenfolgenabgleich, Auswahl und Extraktion mithilfe eines regulären Ausdrucks ausführen.

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse-Operator](parseoperator.md)
- [replace()](replacefunction.md)
- [trim()](trimfunction.md)
- [trimend()](trimendfunction.md)
- [trimstart()](trimstartfunction.md)

Die Syntax für reguläre Ausdrücke, die von Kusto unterstützt wird, ist die Syntax der [re2-Bibliothek](https://github.com/google/re2/wiki/Syntax). Diese Ausdrücke müssen in Kusto als `string`-Literale codiert werden, und alle Zitatregeln für Zeichenfolgen von Kusto gelten. Der reguläre Ausdruck `\A` gleicht z. B. mit dem Anfang einer Zeile ab und wird in Kusto als Zeichenfolgenliteral `"\\A"` angegeben (beachten Sie den „zusätzlichen“ umgekehrten Schrägstrich (`\`)).
