---
title: Spalte-Docstrings der Alter-Merge-Tabelle - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Spalte "Spalte-Docstrings" der Alter-Merge-Tabelle in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75d298f35a215af5da443f673278e4a252c24cc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522388"
---
# <a name="alter-merge-table-column-docstrings"></a>alter-merge-Tabelle Column-docstrings

Legt `docstring` die Eigenschaft einer oder mehrerer Spalten der angegebenen Tabelle fest. Spalten, die nicht explizit festgelegt sind, **behalten** ihren vorhandenen Wert für diese Eigenschaft bei, sofern sie einen werthaben.

Änderungstabelle column-docstring finden Sie [unten](#alter-table-column-docstrings).

**Syntax**

`.alter-merge``table` *Tabellenname* `column-docstring` *Col1* `:` `:` `,` *Col2* *Docstring1* *Docstring2*Col1 Docstring1 [ Col2 Docstring2 ]... `(``)`

**Beispiel** 

```
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>Ändern der Tabelle Spalten-Docstrings

Legt `docstring` die Eigenschaft einer oder mehrerer Spalten der angegebenen Tabelle fest. In Spalten, die nicht explizit festgelegt sind, wird diese Eigenschaft **entfernt.**

**Syntax**

`.alter``table` *Tabellenname* `column-docstring` *Col1* `:` `:` `,` *Col2* *Docstring1* *Docstring2*Col1 Docstring1 [ Col2 Docstring2 ]... `(``)`

**Beispiel** 

```
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```