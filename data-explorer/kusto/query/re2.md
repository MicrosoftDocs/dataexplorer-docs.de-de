---
title: 'Reguläre Ausdrücke: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden reguläre Ausdrücke in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513079"
---
# <a name="re2-syntax"></a>RE2-Syntax

RE2 Syntax für reguläre Ausdrücke beschreibt die Syntax der Bibliothek für reguläre Ausdrücke, die von Kusto verwendet wird (RE2).
Es gibt einige Funktionen in Kusto, die Zeichen folgen Übereinstimmung, Auswahl und Extraktion mithilfe eines regulären Ausdrucks ausführen.

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse-Operator](parseoperator.md)
- [Replace ()](replacefunction.md)
- [Trim ()](trimfunction.md)
- [()](trimendfunction.md)
- [TrimStart ()](trimstartfunction.md)

Die Syntax für reguläre Ausdrücke, die von Kusto unterstützt wird, ist der der [RE2-Bibliothek](https://github.com/google/re2/wiki/Syntax). Diese Ausdrücke müssen in Kusto als Literale codiert werden `string` , und alle Zeichen folgen, die in der Kusto-Reihenfolge zitiert werden, gelten. Der reguläre Ausdruck `\A` entspricht z. b. dem Anfang einer Zeile und wird in Kusto als Zeichenfolgenliteralzeichen angegeben `"\\A"` (Beachten Sie den "zusätzlichen" umgekehrten Schrägstrich ( `\` ).
