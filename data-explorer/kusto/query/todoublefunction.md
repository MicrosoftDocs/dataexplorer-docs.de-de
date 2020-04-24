---
title: todouble()/toreal() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt todouble()/toreal() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb9c976f1646f71fcf8b345899037461f58f4ef0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506323"
---
# <a name="todoubletoreal"></a>todouble()/toreal()

Konvertiert die Eingabe in einen `real`Wert vom Typ . (`todouble()` `toreal()` und sind Synonyme.)

```kusto
toreal("123.4") == 123.4
```

**Syntax**

`toreal(`*Expr*`)`
`todouble(`*Expr*`)`

**Argumente**

* *Expr*: Ein Ausdruck, dessen Wert in `real`einen Wert vom Typ konvertiert wird.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist `real`das Ergebnis ein Wert vom Typ .
Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis der Wert `real(null)`.

*Hinweis*: Bevorzugen Sie die Verwendung von [double() oder real()](./scalar-data-types/real.md) wenn möglich.