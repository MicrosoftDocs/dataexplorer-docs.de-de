---
title: .show-Tabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden .show-Tabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a8faf307a241d1ba0f73436d9503a56c9078e471
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519634"
---
# <a name="show-tables"></a>.anzeigen Tabellen

Gibt einen Satz zurück, der die angegebene Tabelle oder alle Tabellen in der Datenbank enthält.

Erfordert [Database Viewer-Berechtigung](../management/access-control/role-based-authorization.md).

```
.show tables
.show tables (T1, ..., Tn)
```

**Ausgabe**

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|---
|TableName  |String |Der Name der Tabelle.
|DatabaseName  |String |Die Datenbank, zu der die Tabelle gehört.
|Ordner |String |Der Ordner der Tabelle.
|DocString |String |Eine Zeichenfolge, die die Tabelle dokumentiert.

**Ausgabebeispiel**

|Tabellenname |Datenbankname |Ordner | DocString
|---|---|---|---
|Table1 |DB1 |Protokolle |Enthält Dienstprotokolle
|Table2 |DB1 | Berichterstellung |
|Table3 |DB1 | | Erweiterte Informationen |
|Tabelle4 |DB2 | Metriken| Enthält Leistungsleistungsinformationen