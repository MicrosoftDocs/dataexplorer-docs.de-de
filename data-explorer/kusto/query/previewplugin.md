---
title: 'Vorschau-Plug-in: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt das Vorschau-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 455ff1d4d3a42c09a39673028405d51b7acd1f5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251633"
---
# <a name="preview-plugin"></a>preview-Plug-In

Gibt eine Tabelle mit bis zu der angegebenen Anzahl von Zeilen aus der Eingabedaten Satz Gruppe und der Gesamtanzahl von Datensätzen in der Eingabedaten Satz Gruppe zurück.

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>Syntax

`T``|` `evaluate` `preview(` *Nummeriofrows*`)`

## <a name="returns"></a>Rückgabe

Das `preview` Plug-in gibt zwei Ergebnistabellen zurück:
* Eine Tabelle mit bis zu der angegebenen Anzahl von Zeilen.
  Beispielsweise entspricht die obige Beispiel Abfrage der Ausführung von `T | take 50` .
* Eine Tabelle mit einer einzelnen Zeile/Spalte, die die Anzahl der Datensätze in der Eingabedaten Satz Gruppe enthält.
  Beispielsweise entspricht die obige Beispiel Abfrage der Ausführung von `T | count` .

> [!TIP]
> Wenn `evaluate` eine tabellarische Quelle mit einem komplexen Filter vorangestellt ist, oder ein Filter, der auf die meisten Quell Tabellen Spalten verweist, empfiehlt es sich, die-Funktion zu verwenden [`materialize`](materializefunction.md) . Beispiel:

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```