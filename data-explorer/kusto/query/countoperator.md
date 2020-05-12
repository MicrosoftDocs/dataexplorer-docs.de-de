---
title: 'count-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den count-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 9a34734ebfee94646b2b2f15730f14f9d2709c6d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227507"
---
# <a name="count-operator"></a>count-Operator

Gibt die Anzahl der Datensätze in der Eingabedaten Satz Gruppe zurück.

**Syntax**

`T | count`

**Argumente**

*T:* Die tabellarischen Daten, deren Datensätze gezählt werden.

**Rückgabe**

Diese Funktion gibt eine Tabelle mit einem einzelnen Datensatz und einer Spalte vom Typ `long`zurück. Der Wert der einzigen Zelle ist die Anzahl von Datensätzen in *T*. 

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
