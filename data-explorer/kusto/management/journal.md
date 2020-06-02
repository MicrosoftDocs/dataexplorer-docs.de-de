---
title: 'Journal Verwaltung: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt die Journal Verwaltung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 64b0f8179a328ce811747b05a90516fd8b6029be
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294405"
---
# <a name="journal-management"></a>Journal Verwaltung

 `Journal`enthält Informationen zu metadatenvorgängen, die in der Azure Daten-Explorer-Datenbank ausgeführt werden.

Die Metadatenvorgänge können von einem Steuerelement Befehl, der von einem Benutzer ausgeführt wurde, oder von vom System ausgeführten internen Steuerungs Befehlen (z. b. Drop-Blöcke nach Beibehaltung) resultieren.

> [!NOTE]
> * Bei metadatenvorgängen, in denen das *Hinzufügen* neuer Blöcke (z. b. `.ingest` , `.append` , usw.) `.move` dargestellt wird, sind keine übereinstimmenden Ereignisse in der `Journal`
> * Die Daten in den Spalten des Resultsets sowie das Format, in dem es angezeigt wird, sind nicht Contract. 
  Es wird nicht empfohlen, eine Abhängigkeit davon zu treffen.

|event        |EventTimestamp     |Datenbank  |EntityName|Updatedentityname|Entityversion|EntityContainerName|
|-------------|-------------------|----------|----------|-----------------|-------------|-------------------|
|CREATE-TABLE |2017-01-05 14:25:07|Internaldb|MyTable1  |MyTable1         |v 7.0         |Internaldb         |
|Umbenennen-Tabelle |2017-01-13 10:30:01|Internaldb|MyTable1  |MyTable2         |v 8.0         |Internaldb         |  

|Originalentitystate|Updatedentitystate                                              |Changecommand                                                                                                          |Prinzipal            |
|-------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|
|.                  |Name: MyTable1, Attribute: Name = ' [MyTable1]. [col1] ', Type = ' i32 '|. Create Table MyTable1 (col1: int)                                                                                      |imike@fabrikam.com
|.                  |Die Daten Bank Eigenschaften (zu lang, um hier angezeigt zu werden)         |. Create Database TestDB persistent (@ " https://imfbkm.blob.core.windows.net/md ", @ " https://imfbkm.blob.core.windows.net/data ")|Azure AD App-ID = 76263cdb-ABCD-545644e9c404
|Name: MyTable1, Attribute: Name = ' [MyTable1]. [col1] ', Type = ' i32 '|Name: MyTable2, Attribute: Name = ' [MyTable1]. [col1] ', Type = ' i32 '|. RENAME TABLE MyTable1 to MyTable2|rdmik@fabrikam.com

|Element                 |BESCHREIBUNG                                                              |                                
|---------------------|-------------------------------------------------------------------------|
|Ereignis                |Der Name des metadatenereignisses.                                                  |
|EventTimestamp       |Der Zeitstempel des Ereignisses.                                                      |                        
|Datenbank             |Die Metadaten dieser Datenbank wurden nach dem Ereignis geändert.                |
|EntityName           |Der Name der Entität, für die der Vorgang ausgeführt wurde, vor der Änderung.    |
|Updatedentityname    |Der neue Entitäts Name nach der Änderung.                                     |
|Entityversion        |Die neue Metadatenversion (DB/Cluster) nach der Änderung               |
|EntityContainerName  |Der Entitätencontainername (Entity = Column, Container = Table)               |
|Originalentitystate  |Der Zustand der Entität (Entitäts Eigenschaften) vor der Änderung.            |
|Updatedentitystate   |Der neue Zustand nach der Änderung.                                           |
|Changecommand        |Der ausgeführte Steuerelement Befehl, der die Metadatenänderung ausgelöst hat          |
|Prinzipal            |Der Prinzipal (Benutzer/APP), der den Befehl "Control" ausgeführt hat.               |
    
## <a name="show-journal"></a>. Show Journal

Der `.show journal` Befehl gibt eine Liste mit Metadatenänderungen für Datenbanken oder den Cluster zurück, auf die der Benutzer Administrator Zugriff hat.

**Berechtigungen**

Jeder (Cluster Zugriff) kann den Befehl ausführen. 

Folgende Ergebnisse werden zurückgegeben: 
- Alle Journal Einträge des Benutzers, der den Befehl ausführt. 
- Alle Journal Einträge von Datenbanken, auf die der Benutzer, der den Befehl ausführt, über Administrator Zugriff verfügt. 
- Alle Cluster Journal Einträge, wenn der Benutzer, der den Befehl ausführt, ein Cluster Administrator ist. 

## <a name="show-database-databasename-journal"></a>. Anzeigen des Database *DatabaseName* -Journal 

Der `.show` `database` *DatabaseName* - `journal` Befehl gibt das Journal für die spezifischen Daten Bank Metadatenänderungen zurück.

**Berechtigungen**

Jeder (Cluster Zugriff) kann den Befehl ausführen. Die zurückgegebenen Ergebnisse umfassen Folgendes: 
- Alle Journal Einträge von Database *DatabaseName* , wenn der Benutzer, der den Befehl ausführt, ein Datenbankadministrator in *DatabaseName*ist. 
- Andernfalls alle Journal Einträge der Datenbank `DatabaseName` und des Benutzers, der den Befehl ausführt. 
