---
title: ASIN ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Asin () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e23ddda4bbc2e165adfd810e1dc27b5ffd14edf4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246745"
---
# <a name="asin"></a>asin()

Gibt den Winkel zurück, dessen Sinus die angegebene Zahl ist (die umgekehrte Operation von [`sin()`](sinfunction.md) ).

## <a name="syntax"></a>Syntax

`asin(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl im Bereich [-1, 1].

## <a name="returns"></a>Rückgabe

* Der Wert des Arkus Sinus von. `x`
* `null` Wenn `x` <-1 oder `x` > 1