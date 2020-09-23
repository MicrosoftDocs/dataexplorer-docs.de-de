---
title: log10 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt log10 () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 62813f5680e89c2bca0a6ec547fd7055ddc0e414
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103181"
---
# <a name="log10"></a>log10()

`log10()` Gibt die allgemeine Logarithmus Funktion (Basis 10) zurück.  

## <a name="syntax"></a>Syntax

`log10(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl > 0.

## <a name="returns"></a>Gibt zurück

* Der gängige Logarithmus ist der Logarithmus zur Basis 10: die Umkehrung der Exponentialfunktion (Exp) mit Basis 10.
* `null` , wenn das Argument negativ oder NULL ist oder nicht in einen-Wert konvertiert werden kann `real` . 

## <a name="see-also"></a>Weitere Informationen

* Einen natürlichen Logarithmus (Basis-e) finden Sie unter [Log ()](log-function.md).
* Informationen zu Basis-2-Logarithmen finden Sie unter [log2 ()](log2-function.md)