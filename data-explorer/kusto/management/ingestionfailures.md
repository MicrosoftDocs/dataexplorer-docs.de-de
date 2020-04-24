---
title: Verunstumungsfehler - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Erfassungsfehler in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 699fd01e9a284179bf2749b58581392d2170f0ca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520943"
---
# <a name="ingestion-failures"></a>Ver- und Verscheidungsfehler

## <a name="show-ingestion-failures"></a>.zeigen Einnahmefehler

Dieser Befehl gibt eine Ergebnismenge zurück, die alle Erfassungsfehler enthält, die während der Ausführung von [Datenerfassungssteuerungsbefehlen](data-ingestion/index.md)aufgetreten sind.

*Hinweise*: 
- Aufnahmefehler, die während anderer Teile des Aufnahmeflusses auftreten (z. B. bevor Datenerfassungssteuerungsbefehle an Kustos Data Engine-Dienst gesendet werden), werden im Resultset dieses Befehls nicht angezeigt.
- Für Überwachungsfehler, die in Flows auftreten, die die Aufnahme in die [Warteschlange](../api/netfx/about-kusto-ingest.md#queued-ingestion)beinhalten, wird empfohlen, [diesem Handbuch](../api/netfx/kusto-ingest-client-status.md)zu folgen.

**Syntax**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |Gibt alle aufgezeichneten Aufnahmefehler zurück  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |Gibt einen gefilterten Satz von Aufnahmefehlern zurück
|`.show``ingestion` `failures` *OperationId* OperationId `with(OperationId="``")` |Gibt Aufnahmefehler für bestimmte Vorgangs-ID zurück

**Ergebnisse**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|OperationId |String |Operation Identifier. Dieser Parameter kann verwendet werden, um zusätzliche Vorgangsdetails anzuzeigen, indem der Befehl [.show operations](operations.md) ausgeführt wird. 
|Datenbank |String |Datenbank, bei der der Fehler aufgetreten ist
|Tabelle |String |Tabelle, auf der der Fehler aufgetreten ist
|FailedOn |Datetime |Datum/Uhrzeit (in UTC), wenn der Fehler registriert wurde 
|IngestionSourcePath |String |Identifiziert die Aufnahmequelle (normalerweise ein Azure Blob-URI) 
|Details |String |Fehlerdetails. Bietet Einblick in die tatsächliche Ursache des Aufnahmefehlers
|FailureKind |String |Typ des Fehlers (Permanent/Transient)
|RootActivityId |String |Stammaktivitäts-ID.
|OperationKind |String |Der Aufnahmevorgangstyp (Phase), in dem der Fehler registriert wurde
|OriginatesFromUpdatePolicy |Boolean | Gibt an, ob der Fehler beim Ausführen einer [Aktualisierungsrichtlinie](update-policy.md) registriert wurde
 
**Beispiel**
 
|OperationId |Datenbank |Tabelle |FailedOn |IngestionSourcePath |Details |FailureKind |RootActivityId |OperationKind |OriginatesFromUpdatePolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22:25:03.1147331 |... Url... |Stream mit id '*****.csv' hat ein fehlerhaftes Csv-Format |Dauerhaft |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |... Url... |Stream mit id '*****.csv' hat ein fehlerhaftes Csv-Format |Dauerhaft |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |... Url... |Stream mit id '*****.csv' hat ein fehlerhaftes Csv-Format |Dauerhaft |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |... Url... |Unbekannter Fehler: Ausnahme vom Typ 'System.Exception' wurde ausgelöst |Kurzlebig |9b7bb017-471e-48f6-9c96-d16fcf938d2a |DataIngestPull | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |... Url... |Fehler beim Herunterladen von Blob: Der Client konnte den Vorgang nicht innerhalb des angegebenen Timeouts beenden. |Dauerhaft |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull | 0