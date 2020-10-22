---
title: 'Project-Rename-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der Projekt Umbenennungs Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9c46c8d0271516c56c3549212ad7b74f721ce81b
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356502"
---
# <a name="project-rename-operator"></a>project-rename-Operator

Benennt Spalten in der Ergebnis Ausgabe um.

```kusto
T | project-rename new_column_name = column_name
```

## <a name="syntax"></a>Syntax

*T* `| project-rename` *newcolumnname*  =  *existingcolumnname* [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: die Eingabe Tabelle.
* *Newcolumnname:* Der neue Name einer Spalte. 
* *Existingcolumnname:* Der vorhandene Name einer Spalte. 

## <a name="returns"></a>Rückgabe

Eine Tabelle, in der die Spalten in derselben Reihenfolge wie in einer vorhandenen Tabelle mit umbenannten Spalten vorhanden sind.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|k|c|
