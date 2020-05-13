---
title: 'Richtlinien Verwaltung für die streamingansung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Richtlinien Verwaltung für die Streaminglösung in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 76844b764a8e21629c7d936f4c269d7d3ab1ec8c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373331"
---
# <a name="streaming-ingestion-policy-management"></a>Richtlinien Verwaltung für die streamingansung

Die Richtlinie für die streamingansung kann an eine Datenbank oder eine Tabelle angehängt werden, um die streamingerfassung an diese Standorte zuzulassen. Die Richtlinie definiert auch die Zeilen Speicher, die für die streamingansung verwendet werden.

Weitere Informationen zur streamingansung finden Sie unter Streaming-Erfassung [(Vorschau)](../../ingest-data-streaming.md). Weitere Informationen zur Richtlinie für die streamingansung finden Sie unter Streaming-Erfassungs [Richtlinie](streamingingestionpolicy.md).

## <a name="show-policy-streamingingestion"></a>. Anzeigen der Richtlinie "streamingingestion"

Der `.show policy streamingingestion` Befehl zeigt die Richtlinie für die streamingansung der Datenbank oder Tabelle an.

**Syntax**

`.show``database` `policy` `streamingingestion` 
 MyDatabase `.show` `table`MyTable `policy``streamingingestion`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit den folgenden Spalten zurück:

|Spalte    |type    |Beschreibung
|---|---|---
|PolicyName|`string`|Der Richtlinien Name: streamingingestionpolicy
|EntityName|`string`|Datenbank-oder Tabellenname
|Richtlinie    |`string`|Ein JSON-Objekt, das die Richtlinie für die streamingansung definiert, die als [Richtlinien Objekt für die streamingerfassung](#streaming-ingestion-policy-object)

**Beispiel**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|Richtlinie|Childentities|EntityType|
|---|---|---|---|---|
|Streamingingestionpolicy|DB1|{"Zahlofrowstores": 4}

### <a name="streaming-ingestion-policy-object"></a>Richtlinien Objekt für die streamingerfassung

|Eigenschaft  |type    |Beschreibung                                                       |
|----------|--------|------------------------------------------------------------------|
|"Zahlofrowstores" |`int`  |Die Anzahl der Zeilen Speicher, die der Entität zugewiesen sind.|
|Versiesageintervallimit|`TimeSpan?`|Optionales Limit für die Intervalle zwischen versiegelvorgängen in der Tabelle. Der gültige Bereich liegt zwischen 1 und 24 Stunden. Standardwert: 24 Stunden.|
|Versieher oldbytes|`int?`|Optionales Limit für die Datenmenge, die für einen einzelnen Versiegelung-Vorgang in der Tabelle benötigt wird. Der gültige Bereich für den Wert liegt zwischen 10 und 200 MB. Standard: 200 MSB.|

## <a name="alter-policy-streamingingestion"></a>. Alter Policy streamingingestion

`.alter policy streamingingestion`Mit dem Befehl wird die streamingingestion-Richtlinie der Datenbank oder der Tabelle festgelegt.

**Syntax**

* `.alter``database`MyDatabase `policy` `streamingingestion` *Streamingingestionpolicyobject*

* `.alter``database`MyDatabase `policy` `streamingingestion``enable`

* `.alter``database`MyDatabase `policy` `streamingingestion``disable`

* `.alter``table`MyTable `policy` `streamingingestion` *Streamingingestionpolicyobject*

* `.alter``table`MyTable `policy` `streamingingestion``enable`

* `.alter``table`MyTable `policy` `streamingingestion``disable`

**Hinweise**

1. *Streamingingestionpolicyobject* ist ein JSON-Objekt, für das das Richtlinien Objekt für die streamingerfassung definiert ist.

2. `enable`-Legen Sie die Richtlinie für die streamingansung auf 4 rowstores fest, wenn die Richtlinie nicht definiert ist oder bereits mit 0 rowstores definiert ist. andernfalls führt der Befehl nichts aus.

3. `disable`-Legen Sie die Richtlinie für die streamingansung auf 0 rowstores fest, wenn die Richtlinie bereits mit positiven rowstores definiert ist, andernfalls führt der Befehl keine Aktion aus.

**Beispiel**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>. Löschen der Richtlinie "streamingingestion"

Der `.delete policy streamingingestion` Befehl löscht die streamingingestion-Richtlinie aus der Datenbank oder Tabelle.

**Syntax** 

`.delete``database`MyDatabase `policy``streamingingestion`

`.delete``table`MyTable `policy``streamingingestion`

**Rückgabe**

Mit dem Befehl wird das Richtlinien Objekt "Table" oder "Database streamingingestion" gelöscht und anschließend die Ausgabe des entsprechenden Befehls " [. Show Policy streamingingestion](#show-policy-streamingingestion) " zurückgegeben.

**Beispiel**

```kusto
.delete database MyDatabase policy streamingingestion 
```