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
ms.openlocfilehash: 14895e17d77e3bbf1d98d2d68221930d3f291774
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644619"
---
# <a name="count-operator"></a>count-Operator

Gibt die Anzahl der Datensätze in der Eingabedaten Satz Gruppe zurück.

## <a name="syntax"></a>Syntax

`T | count`

## <a name="arguments"></a>Argumente

*T:* Die tabellarischen Daten, deren Datensätze gezählt werden.

## <a name="returns"></a>Gibt zurück

Diese Funktion gibt eine Tabelle mit einem einzelnen Datensatz und einer Spalte vom Typ `long`zurück. Der Wert der einzigen Zelle ist die Anzahl von Datensätzen in *T*. 

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

## <a name="see-also"></a>Siehe auch

Weitere Informationen über die count ()-Aggregations Funktion finden Sie unter [count () (Aggregations Funktion)](count-aggfunction.md).
