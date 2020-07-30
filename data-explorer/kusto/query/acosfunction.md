---
title: acos ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden acos () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d1daf36e85eec4c8543ba14be153a9d6069e1cd1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349855"
---
# <a name="acos"></a>acos()

Gibt den Winkel zurück, dessen Kosinus die angegebene Zahl ist (umgekehrter Vorgang von [`cos()`](cosfunction.md) ).

## <a name="syntax"></a>Syntax

`acos(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl im Bereich [-1, 1].

## <a name="returns"></a>Rückgabe

* Der Wert für den Arkus Kosinus von.`x`
* `null`Wenn `x` <-1 oder `x` > 1