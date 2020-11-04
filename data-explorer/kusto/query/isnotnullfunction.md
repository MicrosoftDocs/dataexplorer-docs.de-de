---
title: IsNotNull ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IsNotNull () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 44161dcdc34232225f4b133fe0a569fb818c3f0f
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349374"
---
# <a name="isnotnull"></a>isnotnull()

Gibt zurück, `true` Wenn das Argument nicht NULL ist.

## <a name="syntax"></a>Syntax

`isnotnull(`[ *Wert* ]`)`

`notnull(`[ *Wert* ] `)` -Alias für `isnotnull`

## <a name="example"></a>Beispiel

```kusto
T | where isnotnull(PossiblyNull) | count
```
