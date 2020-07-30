---
title: ASIN ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Asin () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 360a936d136cd01760175cdf5b5da2718d0cfd91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349498"
---
# <a name="asin"></a>asin()

Gibt den Winkel zurück, dessen Sinus die angegebene Zahl ist (die umgekehrte Operation von [`sin()`](sinfunction.md) ).

## <a name="syntax"></a>Syntax

`asin(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl im Bereich [-1, 1].

## <a name="returns"></a>Rückgabe

* Der Wert des Arkus Sinus von.`x`
* `null`Wenn `x` <-1 oder `x` > 1