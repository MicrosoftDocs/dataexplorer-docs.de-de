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
ms.openlocfilehash: 899e54b46dd231db0bf1272c0eb1933dad474a47
ms.sourcegitcommit: 2ebd83369f247cf6dd91709f26e4ecd873489eaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83555014"
---
# <a name="show-table-schema"></a>.show table schema

Ruft das Schema ab, das in CREATE/ALTER-Befehlen und zusätzlichen Tabellen Metadaten verwendet werden soll.

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

```kusto
.show table TableName cslschema 
```

| Output-Parameter | Typ   | BESCHREIBUNG                                               |
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
.show table TableName schema as json
```

| Output-Parameter | Typ   | BESCHREIBUNG                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Der Name der Tabelle.                   |
| Schema           | String | Das Tabellen Schema im JSON-Format         |
| DatabaseName     | String | Die Datenbank, zu der die Tabelle gehört. |
| Ordner           | String | Tabellen Ordner                          |
| DocString        | String | DocString der Tabelle                       |
