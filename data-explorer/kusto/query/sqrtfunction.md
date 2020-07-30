---
title: SQRT ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird sqrt () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 76f5c8c5f8c1a0b9f685ae88df1ab624dc446150
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350960"
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