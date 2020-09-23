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
ms.openlocfilehash: e04d71ad0e88d42f93f9f34576c4f5f6e752ddb6
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103251"
---
# <a name="log2"></a>log2()

`log2()` Gibt die Logarithmus Funktion der Basis 2 zurück.  

## <a name="syntax"></a>Syntax

`log2(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl > 0.

## <a name="returns"></a>Gibt zurück

* Der Logarithmus ist der Logarithmus zur Basis 2: die Umkehrung der Exponentialfunktion (Exp) mit Basis 2.
* `null` , wenn das Argument negativ oder NULL ist oder nicht in einen-Wert konvertiert werden kann `real` . 

## <a name="see-also"></a>Weitere Informationen

* Einen natürlichen Logarithmus (Basis-e) finden Sie unter [Log ()](log-function.md).
* Allgemeine (Basis 10) Logarithmus finden Sie unter [log10 ()](log10-function.md).