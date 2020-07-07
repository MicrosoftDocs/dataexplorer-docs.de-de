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
ms.openlocfilehash: 9f71a46e6c747b7f9d9f6a3ba2d2f8a308635128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967212"
---
# <a name="alter-merge-table-column-docstrings"></a>.alter-merge table column-docstrings

Legt die- `docstring` Eigenschaft einer oder mehrerer Spalten der angegebenen Tabelle fest. Nicht explizit festgelegte Spalten **behalten** Ihren vorhandenen Wert für diese Eigenschaft bei, wenn Sie über einen verfügen.

Informationen zu alter Table Column-DocString finden Sie [unten](#alter-table-column-docstrings).

**Syntax**

`.alter-merge``table` *TableName* `column-docstring` `(` *Col1* `:` *Docstring1* [ `,` *Col2* `:` *Docstring2*]...`)`

**Beispiel** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>Alter Table Column-Docstrings

Legt die- `docstring` Eigenschaft einer oder mehrerer Spalten der angegebenen Tabelle fest. Für nicht explizit festgelegte Spalten wird diese Eigenschaft **entfernt**.

**Syntax**

`.alter``table` *TableName* `column-docstring` `(` *Col1* `:` *Docstring1* [ `,` *Col2* `:` *Docstring2*]...`)`

**Beispiel** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
