---
title: nicht ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird nicht () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3224c48b963c051d0d65d27a2e64ec8317be30c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252191"
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