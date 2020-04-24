---
title: .show-Tabellenschema - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt das .show Tabellenschema in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 9223baa0242cc936b25a7d3293d102cb3966ed53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519770"
---
# <a name="show-table-schema"></a>.show Tabellenschema

Ruft das Schema ab, das in Create/Alter-Befehlen und zusätzlichen Tabellenmetadaten verwendet werden soll.

Erfordert [Datenbankbenutzerberechtigung](../management/access-control/role-based-authorization.md).

```
.show table TableName cslschema 
```
| Ausgabeparameter | type   | BESCHREIBUNG                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | Der Name der Tabelle.                                    |
| Schema           | String | Das Tabellenschema, das für Tabellenerstellung/-änderungen verwendet werden soll |
| DatabaseName     | String | Die Datenbank, zu der die Tabelle gehört                   |
| Ordner           | String | Ordner der Tabelle                                            |
| DocString        | String | Tabelle docstring                                         |


## <a name="show-table-schema-as-json"></a>.show Tabellenschema als JSON

Ruft das Schema im JSON-Format und zusätzliche Tabellenmetadaten ab.

Erfordert [Datenbankbenutzerberechtigung](../management/access-control/role-based-authorization.md).

```
.show table TableName schema as JSON
```

| Ausgabeparameter | type   | BESCHREIBUNG                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Der Name der Tabelle.                   |
| Schema           | String | Das Tabellenschema im JSON-Format         |
| DatabaseName     | String | Die Datenbank, zu der die Tabelle gehört |
| Ordner           | String | Ordner der Tabelle                          |
| DocString        | String | Tabelle docstring                       |