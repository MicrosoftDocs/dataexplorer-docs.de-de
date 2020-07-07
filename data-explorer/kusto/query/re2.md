---
title: 'Reguläre Ausdrücke: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden reguläre Ausdrücke in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 6bdf666b46adea8105b61fc2b907fc060530ba96
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967586"
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
- [trim()](trimfunction.md)
- [()](trimendfunction.md)
- [TrimStart ()](trimstartfunction.md)

Die Syntax für reguläre Ausdrücke, die von Kusto unterstützt wird, ist der der [RE2-Bibliothek](https://github.com/google/re2/wiki/Syntax). Diese Ausdrücke müssen in Kusto als Literale codiert werden `string` , und alle Zeichen folgen, die in der Kusto-Reihenfolge zitiert werden, gelten. Der reguläre Ausdruck `\A` entspricht z. b. dem Anfang einer Zeile und wird in Kusto als Zeichenfolgenliteralzeichen angegeben `"\\A"` (Beachten Sie den "zusätzlichen" umgekehrten Schrägstrich ( `\` ).
