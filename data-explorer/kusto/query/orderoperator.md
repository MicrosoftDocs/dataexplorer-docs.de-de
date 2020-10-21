---
title: 'Order-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Auftrags Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f552143be8c02cece19030fc7b6f79d5a4bdf4a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241392"
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
* `nulls first` (der Standard `asc` Wert für Order) platziert die NULL-Werte am Anfang und `nulls last` (der Standard `desc` Wert für Order) platziert die NULL-Werte am Ende.

