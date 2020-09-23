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
ms.openlocfilehash: e81266bf43da93d2b36f0be5846e5d74f3157c7f
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103219"
---
# <a name="log"></a>log()

`log()` Gibt die natürliche Logarithmus Funktion zurück.  

## <a name="syntax"></a>Syntax

`log(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl > 0.

## <a name="returns"></a>Gibt zurück

* Der natürliche Logarithmus ist der Basis-e-Logarithmus: die Umkehrung der natürlichen Exponentialfunktion (Exp).
* `null` , wenn das Argument negativ oder NULL ist oder nicht in einen-Wert konvertiert werden kann `real` . 

## <a name="see-also"></a>Weitere Informationen

* Allgemeine (Basis 10) Logarithmus finden Sie unter [log10 ()](log10-function.md).
* Informationen zu Basis-2-Logarithmen finden Sie unter [log2 ()](log2-function.md)