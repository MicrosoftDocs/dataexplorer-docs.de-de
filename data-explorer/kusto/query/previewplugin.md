---
title: Vorschau-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Vorschau-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18bda0a4348d0c0eb2776bf124c57397f318a989
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510981"
---
# <a name="preview-plugin"></a>Vorschau-Plugin

Gibt eine Tabelle mit bis zu der angegebenen Anzahl von Zeilen aus dem Eingabedatensatzsatz und der Gesamtzahl der Datensätze im Eingabedatensatzsatz zurück.

```kusto
T | evaluate preview(50)
```

**Syntax**

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

**Rückgabe**

Das `preview` Plugin gibt zwei Ergebnistabellen zurück:
* Eine Tabelle mit bis zu der angegebenen Anzahl von Zeilen.
  Die obige Beispielabfrage entspricht beispielsweise `T | take 50`der Ausführung von .
* Eine Tabelle mit einer einzelnen Zeile/Spalte, die die Anzahl der Datensätze im Eingabedatensatzsatz enthält.
  Die obige Beispielabfrage entspricht beispielsweise `T | count`der Ausführung von .

**Tipps**

Wenn `evaluate` eine tabellarische Quelle vorangestellt wird, die einen komplexen Filter enthält, oder [`materialize`](materializefunction.md) ein Filter, der auf die meisten Quelltabellenspalten verweist, ziehen Sie es vor, die Funktion zu verwenden. Beispiel:

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```