---
title: Journalverwaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Journalverwaltung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: bbe5ab4bda42fdfd9382c7e71da85789c13f6987
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520790"
---
# <a name="journal-management"></a>Journalverwaltung

 `Journal`enthält Informationen zu Metadatenvorgängen, die in der Kusto-Datenbank ausgeführt werden.

Die Metadatenoperationen können das Ergebnis eines Kontrollbefehls sein, der vom Benutzer oder internen Steuerungsbefehlen ausgeführt wird, die vom System ausgeführt werden (z. B. Drop-Extents durch Aufbewahrung).

**Hinweise:**

- Metadatenoperationen, die das *Hinzufügen* `.ingest`neuer `.append` `.move` Ausdehnungen (z. B. `Journal`, und andere) umfassen, haben keine übereinstimmenden Ereignisse, die im angezeigt werden.
- Die Daten in den Spalten des Resultsets sowie das Format, in dem es dargestellt wird, sind *nicht* vertraglich, und daher wird eine Abhängigkeit von ihnen *nicht* empfohlen.

|event        |EventTimestamp     |Datenbank  |EntityName|UpdatedEntityName|EntityVersion|EntityContainerName|
|-------------|-------------------|----------|----------|-----------------|-------------|-------------------|
|CREATE-TABELLE |2017-01-05 14:25:07|InterneDb|MyTable1  |MyTable1         |v7.0         |InterneDb         |
|UMBENENNEN-TABELLE |2017-01-13 10:30:01|InterneDb|MyTable1  |MyTable2         |v8.0         |InterneDb         |  

|OriginalEntityState|AktualisiertEntityState                                              |ChangeCommand                                                                                                          |Prinzipal            |
|-------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|
|.                  |Name: MyTable1, Attribute: Name='[MyTable1]. [col1]', Typ='I32'|.create Tabelle MyTable1 (col1:int)                                                                                      |imike@fabrikam.com
|.                  |Die db-Eigenschaften (zu lange, um hier angezeigt zu werden)               |.create Datenbank TestDB persisthttps://imfbkm.blob.core.windows.net/md('https://imfbkm.blob.core.windows.net/data", '' ")|AAD app id=76263cdb-abcd-545644e9c404
|Name: MyTable1, Attribute: Name='[MyTable1]. [col1]', Typ='I32'|Name: MyTable2, Attribute: Name='[MyTable1]. [col1]', Typ='I32'|.rename tabelle MyTable1 in MyTable2|rdmik@fabrikam.com


Ereignis - der Name des Metadatenereignisses.

EventTimestamp - der Ereigniszeitstempel.

Datenbank - Die Metadaten dieser Datenbank wurden nach dem Ereignis geändert.

EntityName - der Entitätsname, für den der Vorgang ausgeführt wurde, vor der Änderung.

UpdatedEntityName - der neue Entitätsname nach der Änderung.

EntityVersion - die neue Metadatenversion (db/cluster) nach der Änderung.

EntityContainerName - der Entitätscontainername (d. h.: entity=column, container=table).

OriginalEntityState - der Status der Entität (Entitätseigenschaften) vor der Änderung.

UpdatedEntityState - der neue Status nach der Änderung.

ChangeCommand - der ausgeführte Steuerbefehl, der die Metadatenänderung ausgelöst hat.

Principal - der Prinzipal (Benutzer/App), der den Steuerbefehl ausgeführt hat.
                    
## <a name="show-journal"></a>.show Journal

Der `.show journal` Befehl gibt eine Liste der Metadatenänderungen zurück, auf Datenbanken/Clustern hat der Benutzer Administratorzugriff.

**Berechtigungen**

Jeder (Clusterzugriff) kann den Befehl ausführen. 

Zu den zurückgegebenen Ergebnissen gehören: 
1. Alle Journaleinträge des Benutzers, der den Befehl ausführt. 
2. Alle Journaleinträge von Datenbanken, auf die der Benutzer, der den Befehl ausführt, Administratorzugriff hat. 
3. Alle Clusterjournaleinträge, wenn der Benutzer, der den Befehl ausführt, ein Clusteradministrator ist. 

## <a name="show-database-databasename-journal"></a>.show Database *DatabaseName* Journal 

Der `.show` `database` Befehl *DatabaseName* `journal` gibt journal für die spezifischen Datenbankmetadatenänderungen zurück.

**Berechtigungen**

Jeder (Clusterzugriff) kann den Befehl ausführen. Zu den zurückgegebenen Ergebnissen gehören: 
1. Alle Journaleinträge von Database *DatabaseName,* wenn der Benutzer, der den Befehl ausführt, ein Datenbankadministrator in *DatabaseName*ist. 
2. Andernfalls werden alle Journaleinträge von Database *DatabaseName* und des Benutzers, der den Befehl ausführt. 

