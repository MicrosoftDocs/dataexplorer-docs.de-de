---
title: 'Vorschau-Plug-in: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt das Vorschau-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3d54852577281b66ed7754e419acbabbba989e7c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346081"
---
# <a name="preview-plugin"></a>preview-Plug-In

Gibt eine Tabelle mit bis zu der angegebenen Anzahl von Zeilen aus der Eingabedaten Satz Gruppe und der Gesamtanzahl von Datensätzen in der Eingabedaten Satz Gruppe zurück.

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>Syntax

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

## <a name="returns"></a>Rückgabe

Das `preview` Plug-in gibt zwei Ergebnistabellen zurück:
* Eine Tabelle mit bis zu der angegebenen Anzahl von Zeilen.
  Beispielsweise entspricht die obige Beispiel Abfrage der Ausführung von `T | take 50` .
* Eine Tabelle mit einer einzelnen Zeile/Spalte, die die Anzahl der Datensätze in der Eingabedaten Satz Gruppe enthält.
  Beispielsweise entspricht die obige Beispiel Abfrage der Ausführung von `T | count` .

**Tipps**

Wenn `evaluate` eine tabellarische Quelle mit einem komplexen Filter vorangestellt ist, oder ein Filter, der auf die meisten Quell Tabellen Spalten verweist, empfiehlt es sich, die-Funktion zu verwenden [`materialize`](materializefunction.md) . Zum Beispiel:

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```