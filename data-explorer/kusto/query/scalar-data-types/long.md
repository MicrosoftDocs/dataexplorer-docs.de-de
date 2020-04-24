---
title: Der lange Datentyp - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den langen Datentyp in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b8c51f216b8515e483daf64d9783210ff35ceef3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509706"
---
# <a name="the-long-data-type"></a>Der lange Datentyp

Der `long` Datentyp stellt eine signierte, 64 Bit breite Ganzzahl dar.

## <a name="long-literals"></a>lange Liter

Literale des `long` Datentyps können in der folgenden Syntax angegeben werden:

`long``(` *Wert*`)`

Wo *Value* die folgenden Formen annehmen kann:
* Eine weitere oder eine Ziffer, in diesem Fall ist der Literalwert die Dezimaldarstellung dieser Ziffern. Ist z. `long(12)` B. die `long`Zahl zwölf des Typs .
* Das Präfix `0x` gefolgt von einer oder mehreren Hex-Ziffern. `long(0xf)` entspricht beispielsweise `long(15)`.
* Ein Minuszeichen (`-`) gefolgt von einer oder mehreren Ziffern. `long(-1)` Die Zahl ist z. B. die Zahl minus eins vom Typ `long`.
* `null`, in diesem Fall ist dies der [NULL-Wert](null-values.md) des `long` Datentyps. Daher ist `long` `long(null)`der Nullwert des Typs .

Kusto unterstützt auch Literale `long` `long(` / `)` des Typs ohne Präfix/Suffi nur für die ersten beiden Formen. Daher `123` ist ein Literal `long`vom `0x123`Typ `-2` , wie es ist , ist aber `-` **kein** Literal (es wird derzeit als die unäre Funktion interpretiert, die auf das Literal `2` vom Typ long angewendet wird).
 
Zum Konvertieren von long in hex string - siehe [tohex() Funktion](../tohexfunction.md).