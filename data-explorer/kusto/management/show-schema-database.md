---
title: '. Anzeigen des Datenbankschemas: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie das Datenbankschema in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b77fe78ec43bce775774ae95c1ea713d03873cf4
ms.sourcegitcommit: c351c2c8ab6e184827c4702eb0ec8bf783c7bbd3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874796"
---
# <a name="show-database-schema-commands"></a>. Datenbankschema Befehle anzeigen

Die folgenden Befehle zeigen das Datenbankschema als Tabelle, JSON-Objekt oder CSL-Skript an.

## <a name="show-databases-schema"></a>. Anzeigen des Datenbankschemas

**Syntax**

`.show``database` *DatabaseName* `schema` [ `details` ] [ `if_later_than` *"Version"*] 

`.show``databases` `(` *DatabaseName1* `,` .. `)` . `schema``details` 
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ... `)` `schema``details`

**Rückgabe**

Gibt eine flache Liste der Struktur der ausgewählten Datenbanken mit allen Tabellen und Spalten in einer einzelnen Tabelle oder einem JSON-Objekt zurück.
Bei Verwendung mit einer Version wird die Datenbank nur zurückgegeben, wenn Sie eine höhere Version als die bereitgestellte Version ist.

> [!NOTE]
> Die Version sollte nur im Format "vMM.mm" bereitgestellt werden. Mm stellt die Hauptversion und mm die neben Version dar.

**Beispiel** 
 
Die Datenbank "TestDB" verfügt über eine Tabelle mit dem Namen "Events".

```kusto
.show database TestDB schema 
```

**Ausgabe**

|DatabaseName|TableName|ColumnName|ColumnType|Isdefaulbar|Isdefaultcolumn|Prettyname|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|Ereignisse|||True|False||       
|TestDB|Ereignisse| Name|System.String|True|False||     
|TestDB|Ereignisse| StartTime|  System.DateTime|True|False||    
|TestDB|Ereignisse| EndTime|    System.DateTime|True|False||        
|TestDB|Ereignisse| City|   System.String|True| False||     
|TestDB|Ereignisse| SessionID|  System.Int32|True|  True|| 

**Beispiel** 

Im folgenden Beispiel wird die Datenbank nur zurückgegeben, wenn Sie eine neuere Version als die bereitgestellte Version ist.
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```

**Ausgabe**

|DatabaseName|TableName|ColumnName|ColumnType|Isdefaulbar|Isdefaultcolumn|Prettyname|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|Ereignisse|||True|False||       
|TestDB|Ereignisse| Name|System.String|True|False||     
|TestDB|Ereignisse| StartTime|  System.DateTime|True|False||    
|TestDB|Ereignisse| EndTime|    System.DateTime|True|False||        
|TestDB|Ereignisse| City|   System.String|True| False||     
|TestDB|Ereignisse| SessionID|  System.Int32|True|  True||  

Da eine niedrigere Version als die aktuelle Datenbankversion bereitgestellt wurde, wurde das Schema "TestDB" zurückgegeben. Das Bereitstellen einer gleichen oder einer höheren Version hätte ein leeres Ergebnis generiert.

## <a name="show-database-schema-as-json"></a>. Anzeigen des Datenbankschemas als JSON

**Syntax**

`.show``database` *DatabaseName* `schema` [ `if_later_than` *"Version"*] `as``json`
 
`.show``databases` `(` *DatabaseName1* `,` .. `)` . `schema` `as``json`
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ... `)` `schema` `as``json`

**Rückgabe**

Gibt eine flache Liste der Struktur der ausgewählten Datenbanken mit allen Tabellen und Spalten als JSON-Objekt zurück.
Bei Verwendung mit einer Version wird die Datenbank nur zurückgegeben, wenn Sie eine höhere Version als die bereitgestellte Version ist.

**Beispiel** 
 
```kusto
.show database TestDB schema as json
```

**Ausgabe**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

## <a name="show-database-schema-as-csl-script"></a>. Anzeigen des Datenbankschemas als CSL-Skript

Generiert ein CSL-Skript mit allen erforderlichen Befehlen, um eine Kopie des angegebenen (oder aktuellen) Datenbankschemas zu erstellen.

**Syntax**

`.show``database` *DatabaseName* `schema` `as` `csl` `script` [ `with(` *Optionen* `)` ]

**Argumente**

Die folgenden *Optionen* sind optional:

* `IncludeEncodingPolicies`: ( `true`  |  `false` ): Wenn `true` , werden Codierungs Richtlinien auf Datenbank-/Tabellen-/Spaltenebene eingeschlossen. Wird standardmäßig auf `false` festgelegt. 
* `IncludeSecuritySettings`: (): Standardwert ist `true`  |  `false` `false` . Gibt an `true` , dass die folgenden Optionen eingeschlossen werden:
  * Autorisierte Prinzipale auf Datenbank-/Tabellenebene.
  * Sicherheitsrichtlinien auf Zeilenebene auf Tabellenebene.
  * Eingeschränkte Anzeige Zugriffsrichtlinien auf Tabellenebene.
* `IncludeIngestionMappings`: (): Wenn, werden Erfassungs Zuordnungen `true`  |  `false` `true` auf Tabellenebene eingeschlossen. Wird standardmäßig auf `false` festgelegt. 

**Rückgabe**

Das Skript, das als Zeichenfolge zurückgegeben wird, enthält Folgendes:

* Befehle zum Erstellen der Datenbank und zum Festlegen des entsprechenden Namens, falls vorhanden.
  * Mit dem generierten Befehl wird eine flüchtige Datenbank erstellt, die beim Hinzufügen zum Skript auskommentiert wird.
* Befehle zum Erstellen aller Tabellen in der Datenbank.
* Befehle zum Festlegen aller Richtlinien für Datenbank/Tabellen/Spalten, die den ursprünglichen Richtlinien entsprechen.
* Befehle zum Erstellen oder Ändern aller benutzerdefinierten Funktionen in der Datenbank.

**Beispiel** 
 
```kusto
.show database TestDB schema as csl script

.show database TestDB schema as csl script with(IncludeSecuritySettings = true)
```
