---
title: 'count-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den count-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: bf595e27d7b4881dca7b5e2a370c90a8407a8c78
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252600"
---
# <a name="count-operator"></a>count-Operator

Gibt die Anzahl der Datensätze in der Eingabedaten Satz Gruppe zurück.

## <a name="syntax"></a>Syntax

`T | count`

## <a name="arguments"></a>Argumente

*T:* Die tabellarischen Daten, deren Datensätze gezählt werden.

## <a name="returns"></a>Rückgabe

Diese Funktion gibt eine Tabelle mit einem einzelnen Datensatz und einer Spalte vom Typ `long`zurück. Der Wert der einzigen Zelle ist die Anzahl von Datensätzen in *T*. 

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
