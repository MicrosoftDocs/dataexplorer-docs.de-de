---
title: '. Anzeigen von Tabellen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie Tabellen in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3da4f705d3182c52d06c7767a12d9be15a219e5c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618325"
---
# <a name="show-tables"></a>. Anzeigen von Tabellen

Gibt eine Menge zurück, die die angegebene Tabelle oder alle Tabellen in der Datenbank enthält.

Erfordert die [Database Viewer-Berechtigung](../management/access-control/role-based-authorization.md).

```kusto
.show tables
.show tables (T1, ..., Tn)
```

**Ausgabe**

|Output-Parameter |type |BESCHREIBUNG
|---|---|---
|TableName  |String |Der Name der Tabelle.
|DatabaseName  |String |Die Datenbank, zu der die Tabelle gehört.
|Ordner |String |Der Ordner der Tabelle.
|DocString |String |Eine Zeichenfolge, die die Tabelle dokumentiert.

**Ausgabe Beispiel**

|Tabellenname |Datenbankname |Ordner | DocString
|---|---|---|---
|Table1 |DB1 |Protokolle |Enthält Dienst Protokolle
|Table2 |DB1 | Berichterstellung |
|Table3 |DB1 | | Erweiterte Informationen |
|Table4 |DB2 | Metriken| Enthält Leistungsinformationen zu Diensten
