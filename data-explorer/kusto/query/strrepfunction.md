---
title: trerep ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5ee36d40035ab2afd19af2da2a0cb174ee365ca
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251437"
---
# <a name="strrep"></a>strrep()

Die angegebene [Zeichenfolge](./scalar-data-types/string.md) wird wiederholt.

* Wenn das erste oder das dritte Argument keinen Zeichen Folgentyp hat, wird es zwangsweise in eine Zeichenfolge konvertiert.

## <a name="syntax"></a>Syntax

`strrep(`*Wert*,*Multiplikator*, [*Trennzeichen*]`)`

## <a name="arguments"></a>Argumente

* *Wert*: Eingabe Ausdruck
* *Multiplikator*: positiver ganzzahliger Wert (von 1 bis 1024)
* *Trennzeichen*: ein optionaler Zeichen folgen Ausdruck (Standard: leere Zeichenfolge)

## <a name="returns"></a>Rückgabe

Der Wert wird so oft wie angegeben wiederholt, mit *Trenn*Zeichen verkettet.

Falls der *Multiplikator* größer ist als der maximal zulässige Wert (1024), wird die Eingabe Zeichenfolge 1024 mal wiederholt.
 
## <a name="example"></a>Beispiel

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|Abcabc|123.123.123|00:00:03 00:00:03|