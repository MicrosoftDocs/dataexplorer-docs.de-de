---
title: estimate_data_size ()-Azure Daten-Explorer
description: In diesem Artikel wird estimate_data_size () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0901bddbfa8854e902ab60197164cf830215948
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224951"
---
# <a name="estimate_data_size"></a>estimate_data_size()

Gibt eine geschätzte Datengröße der ausgewählten Spalten des tabellarischen Ausdrucks in Bytes zurück.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**Syntax**

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, ` *Col2* `, ` ...`)`

**Argumente**

* *col1*, *Col2*: Auswahl von Spalten verweisen im tabellarischen Quell Ausdruck, die für die Datengrößen Schätzung verwendet werden. Verwenden Sie die `*` Syntax (Sternchen), um alle Spalten einzuschließen.

**Rückgabe**

* Die geschätzte Datengröße in Bytes der Daten Satz Größe. Die Schätzung basiert auf Datentypen und Werte Längen.

**Beispiele**

Berechnen der Gesamtgröße der Daten mit `estimated_data_size()` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|Gesamt|
|---|
|180|
