---
title: Der guiD-Datentyp - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den guiD-Datentyp in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 382b2da0ab791cf3e2fea0e1c785ee42634aab07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509859"
---
# <a name="the-guid-data-type"></a>Der GUID-Datentyp

Der `guid` `uuid`Datentyp ( , `uniqueid`) stellt einen global eindeutigen 128-Bit-Wert dar.

> [!WARNING]
> Zum Zeitpunkt der Erstellung dieses Dokuments wird der Typ `guid` noch nicht vollständig unterstützt.
> Die Hauptlücke besteht darin, dass ein Index für Spalten dieses Typs fehlt, was sich auf die Leistung von Abfragen auswirkt, die sich über diesen Typ prädizieren.
> Teams sollten daher stattdessen Werte vom Typ `string` verwenden.

## <a name="guid-literals"></a>guid-Literale

Um ein Literal `guid`vom Typ darzustellen, verwenden Sie das folgende Format:

```kusto
guid(74be27de-1e4e-49d9-b579-fe0b331d3642)
```

Das Sonderformular `guid(null)` stellt den [NULL-Wert dar.](null-values.md)