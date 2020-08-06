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
ms.openlocfilehash: 7c4ee69c4f82c25c6f4cf7d4b63ad9a659892a28
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802994"
---
# <a name="preview-plugin"></a>preview-Plug-In

Gibt eine Tabelle mit bis zu der angegebenen Anzahl von Zeilen aus der Eingabedaten Satz Gruppe und der Gesamtanzahl von Datensätzen in der Eingabedaten Satz Gruppe zurück.

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>Syntax

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

## <a name="returns"></a>Gibt zurück

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