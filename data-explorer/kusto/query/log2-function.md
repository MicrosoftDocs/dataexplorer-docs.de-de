---
title: log2 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt log2 () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: a404dfba70f3a624acc08e70ee4b24935d1d7135
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347101"
---
# <a name="log2"></a>log2()

`log2()`Gibt die Logarithmus Funktion der Basis 2 zurück.  

## <a name="syntax"></a>Syntax

`log2(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl > 0.

## <a name="returns"></a>Rückgabe

* Der Logarithmus ist der Logarithmus zur Basis 2: die Umkehrung der Exponentialfunktion (Exp) mit Basis 2.
* `null`, wenn das Argument negativ oder NULL ist oder nicht in einen-Wert konvertiert werden kann `real` . 

**Weitere Informationen**

* Einen natürlichen Logarithmus (Basis-e) finden Sie unter [Log ()](log-function.md).
* Allgemeine (Basis 10) Logarithmus finden Sie unter [log10 ()](log10-function.md).