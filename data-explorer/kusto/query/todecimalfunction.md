---
title: $ Decimal ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird das-Zeichen () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f699508436c9e2533661a440be2ac8f5f8d94688
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350773"
---
# <a name="todecimal"></a>todecimal()

Konvertiert Eingaben in eine Dezimalzahl Darstellung.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>Syntax

`todecimal(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in einen Dezimalwert konvertiert wird. 

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine Dezimalzahl sein.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
 
*Hinweis*: Verwenden Sie möglichst [Real ()](./scalar-data-types/real.md) .