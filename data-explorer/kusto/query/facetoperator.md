---
title: 'facetoperator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den facetoperator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2398652e7cad7456d294f11353cfdfe049080503
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249065"
---
# <a name="facet-operator"></a>facet-Operator

Gibt eine Gruppe von Tabellen zurück, jeweils eine für jede angegebene Spalte.
Jede Tabelle gibt die Liste der Werte an, die von der zugehörigen Spalte übernommen werden.
Eine zusätzliche Tabelle kann mit der-Klausel erstellt werden `with` .

## <a name="syntax"></a>Syntax

*T* `| facet by` *ColumnName* [ `, ` ...] [ `with (` *filterpipe*`)`

## <a name="arguments"></a>Argumente

* *ColumnName:* Der Name der Spalte in der Eingabe, die als Ausgabe Tabelle zusammengefasst werden soll.
* *filterpipe:* Ein Abfrage Ausdruck, der auf die Eingabe Tabelle angewendet wird, um eine der Ausgaben zu erhalten.

## <a name="returns"></a>Rückgabe

Mehrere Tabellen: eine für die `with` -Klausel und eine für jede Spalte.

## <a name="example"></a>Beispiel

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```