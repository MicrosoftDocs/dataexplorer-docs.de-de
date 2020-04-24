---
title: Facettenoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Facettenoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b6c87fc044e0f00f77e28e85d89757a69835164
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515316"
---
# <a name="facet-operator"></a>facet-Operator

Gibt einen Satz von Tabellen zurück, eine für jede angegebene Spalte.
Jede Tabelle gibt die Liste der Werte an, die von ihrer Spalte übernommen werden.
Eine zusätzliche Tabelle kann mithilfe `with` der Klausel erstellt werden.

**Syntax**

*T* `| facet by` *Spaltenname* [`, ` ...] [`with (` *filterPipe*`)`

**Argumente**

* *Spaltenname:* Der Name der Spalte in der Eingabe, die als Ausgabetabelle zusammengefasst werden soll.
* *filterPipe:* Ein Abfrageausdruck, der auf die Eingabetabelle angewendet wird, um eine der Ausgaben zu erzeugen.

**Rückgabe**

Mehrere Tabellen: eine `with` für die Klausel und eine für jede Spalte.

**Beispiel**

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```