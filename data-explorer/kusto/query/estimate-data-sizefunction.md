---
title: estimate_data_size() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird estimate_data_size() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9664a7c9caf0506cdb7274eed5e143f89c82cebb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515724"
---
# <a name="estimate_data_size"></a>estimate_data_size()

Gibt eine geschätzte Datengröße in Bytes der ausgewählten Spalten des Tabellenausdrucks zurück.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**Syntax**

`estimate_data_size(*)`

`estimate_data_size(`*col1*`, `*col2*`, `...`)`

**Argumente**

* *col1*, *col2*: Auswahl von Spaltenverweisen im Tabellenausdruck der Quelle, die für die Datengrößenschätzung verwendet werden. Um alle Spalten `*` einzuschließen, verwenden Sie die Syntax (Sternchen).

**Rückgabe**

* Die geschätzte Datengröße in Bytes der Datensatzgröße. Die Schätzung basiert auf Datentypen und Wertelängen.

**Beispiele**

Berechnen der Gesamtdatengröße mit: `estimated_data_size()`

```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|Gesamt|
|---|
|180|