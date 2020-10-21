---
title: Log ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird log () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 2b7c4f0ac50c35dba0a1d59540fdaafb0cda2bfd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241454"
---
# <a name="log"></a>log()

`log()` Gibt die natürliche Logarithmus Funktion zurück.  

## <a name="syntax"></a>Syntax

`log(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl > 0.

## <a name="returns"></a>Rückgabe

* Der natürliche Logarithmus ist der Basis-e-Logarithmus: die Umkehrung der natürlichen Exponentialfunktion (Exp).
* `null` , wenn das Argument negativ oder NULL ist oder nicht in einen-Wert konvertiert werden kann `real` . 

## <a name="see-also"></a>Weitere Informationen

* Allgemeine (Basis 10) Logarithmus finden Sie unter [log10 ()](log10-function.md).
* Informationen zu Basis-2-Logarithmen finden Sie unter [log2 ()](log2-function.md)