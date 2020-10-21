---
title: log10 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt log10 () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 978e40f3a2727b08dd404068ad364c7416361f66
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241414"
---
# <a name="log10"></a>log10()

`log10()` Gibt die allgemeine Logarithmus Funktion (Basis 10) zurück.  

## <a name="syntax"></a>Syntax

`log10(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl > 0.

## <a name="returns"></a>Rückgabe

* Der gängige Logarithmus ist der Logarithmus zur Basis 10: die Umkehrung der Exponentialfunktion (Exp) mit Basis 10.
* `null` , wenn das Argument negativ oder NULL ist oder nicht in einen-Wert konvertiert werden kann `real` . 

## <a name="see-also"></a>Weitere Informationen

* Einen natürlichen Logarithmus (Basis-e) finden Sie unter [Log ()](log-function.md).
* Informationen zu Basis-2-Logarithmen finden Sie unter [log2 ()](log2-function.md)