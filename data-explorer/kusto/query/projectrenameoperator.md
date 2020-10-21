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
ms.openlocfilehash: 83a4dba3f426189de615640ab48e1fa60f9e5537
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242199"
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
