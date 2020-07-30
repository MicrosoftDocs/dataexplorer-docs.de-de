---
title: HourOfDay ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird HourOfDay () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a001a2f9faa1eb7ea3636ee6fbfde3cb0489158
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347509"
---
# <a name="hourofday"></a>hourofday()

Gibt die ganzzahlige Zahl zurück, die die Stundenzahl des angegebenen Datums darstellt.

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

## <a name="syntax"></a>Syntax

`hourofday(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Gibt zurück

`hour number`des Tages (0-23).