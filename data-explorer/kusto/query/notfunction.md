---
title: nicht ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird nicht () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fed2a55c8fa1c7689c087ccdeaa64ff576bea401
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346591"
---
# <a name="not"></a>not()

Kehrt den Wert des Arguments um `bool` .

```kusto
not(false) == true
```

## <a name="syntax"></a>Syntax

`not(`*expr*`)`

## <a name="arguments"></a>Argumente

* *expr*: ein `bool` Ausdruck, der umgekehrt werden soll.

## <a name="returns"></a>Rückgabe

Gibt den umgekehrten logischen Wert des `bool` Arguments zurück.