---
title: Alter-Merge Table Column-Docstrings-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden Alter-Merge Table Column-Docstrings in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7dd36181be1140d3960369b1c8a5284ed55e48f5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616489"
---
# <a name="alter-merge-table-column-docstrings"></a>Alter-Merge Table Column-Docstrings

Legt die `docstring` -Eigenschaft einer oder mehrerer Spalten der angegebenen Tabelle fest. Nicht explizit festgelegte Spalten **behalten** Ihren vorhandenen Wert für diese Eigenschaft bei, wenn Sie über einen verfügen.

Informationen zu alter Table Column-DocString finden Sie [unten](#alter-table-column-docstrings).

**Syntax**

`.alter-merge``table` *TableName* `column-docstring` `(` *col1* `,` *Docstring1* `:` *Docstring2*Docstring1 [Col2 Docstring2]... *Col2* `:``)`

**Beispiel** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>Alter Table Column-Docstrings

Legt die `docstring` -Eigenschaft einer oder mehrerer Spalten der angegebenen Tabelle fest. Für nicht explizit festgelegte Spalten wird diese Eigenschaft **entfernt**.

**Syntax**

`.alter``table` *TableName* `column-docstring` `(` *col1* `,` *Docstring1* `:` *Docstring2*Docstring1 [Col2 Docstring2]... *Col2* `:``)`

**Beispiel** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
