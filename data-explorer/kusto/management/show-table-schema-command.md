---
title: '. Anzeigen des Tabellen Schemas: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie das Tabellen Schema in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: e2a550f0ea755181d39524876833cff4281608b4
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618342"
---
# <a name="show-table-schema"></a>.show table schema

Ruft das Schema ab, das in CREATE/ALTER-Befehlen und zusätzlichen Tabellen Metadaten verwendet werden soll.

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

```kusto
.show table TableName cslschema 
```

| Output-Parameter | type   | BESCHREIBUNG                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | Der Name der Tabelle.                                    |
| Schema           | String | Das Tabellen Schema, das zum Erstellen/Ändern von Tabellen verwendet werden soll. |
| DatabaseName     | String | Die Datenbank, zu der die Tabelle gehört.                   |
| Ordner           | String | Tabellen Ordner                                            |
| DocString        | String | DocString der Tabelle                                         |


## <a name="show-table-schema-as-json"></a>. Anzeigen des Tabellen Schemas als JSON

Ruft das Schema im JSON-Format und zusätzliche Tabellen Metadaten ab.

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

```kusto
.show table TableName schema as JSON
```

| Output-Parameter | type   | BESCHREIBUNG                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Der Name der Tabelle.                   |
| Schema           | String | Das Tabellen Schema im JSON-Format         |
| DatabaseName     | String | Die Datenbank, zu der die Tabelle gehört. |
| Ordner           | String | Tabellen Ordner                          |
| DocString        | String | DocString der Tabelle                       |
