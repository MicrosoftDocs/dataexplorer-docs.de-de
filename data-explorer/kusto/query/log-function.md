---
title: Log ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird log () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 283cd1427dedb04b036d7cb23e650d8f0651a58c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347135"
---
# <a name="log"></a>log()

`log()`Gibt die natürliche Logarithmus Funktion zurück.  

## <a name="syntax"></a>Syntax

`log(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl > 0.

## <a name="returns"></a>Rückgabe

* Der natürliche Logarithmus ist der Basis-e-Logarithmus: die Umkehrung der natürlichen Exponentialfunktion (Exp).
* `null`, wenn das Argument negativ oder NULL ist oder nicht in einen-Wert konvertiert werden kann `real` . 

**Weitere Informationen**

* Allgemeine (Basis 10) Logarithmus finden Sie unter [log10 ()](log10-function.md).
* Informationen zu Basis-2-Logarithmen finden Sie unter [log2 ()](log2-function.md)