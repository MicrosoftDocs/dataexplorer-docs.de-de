---
title: SQRT ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird sqrt () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 81b31d8a692a2f68cdb8dd68b5c5f1e75057d3b0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242510"
---
# <a name="sqrt"></a>sqrt()

Gibt die Quadratwurzel Funktion zurück.  

## <a name="syntax"></a>Syntax

`sqrt(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl >= 0.

## <a name="returns"></a>Rückgabe

* Eine positive Zahl, sodass Folgendes gilt: `sqrt(x) * sqrt(x) == x`
* `null`, wenn das Argument negativ ist oder nicht in einen `real`-Wert konvertiert werden kann. 