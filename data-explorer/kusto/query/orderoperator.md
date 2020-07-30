---
title: 'Order-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Auftrags Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 510e2de8a30a422955c0cbfcbdf3a0f50e46dbc5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346557"
---
# <a name="order-operator"></a>order-Operator 

Sortiert die Zeilen der Eingabetabelle in Reihenfolge nach einer oder mehreren Spalten.

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Der Order-Operator ist ein Alias für den Sort-Operator. Weitere Informationen finden Sie unter [Sort-Operator](sortoperator.md) .

## <a name="syntax"></a>Syntax

*T* - `| order by` *Spalte* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: die zu sortierende Tabellen Eingabe.
* *Column*: Spalte von *T* , nach der sortiert werden soll. Der Typ der Werte muss „Numerisch“, „Datum“, „Uhrzeit“ oder „Zeichenfolge“ sein.
* `asc` : Sortierung in aufsteigender Reihenfolge. Die Standardeinstellung ist `desc`, also absteigend.
* `nulls first`(der Standard `asc` Wert für Order) platziert die NULL-Werte am Anfang und `nulls last` (der Standard `desc` Wert für Order) platziert die NULL-Werte am Ende.

