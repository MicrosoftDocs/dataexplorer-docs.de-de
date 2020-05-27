---
title: 'Erfassungs Fehler: Azure-Daten-Explorer'
description: In diesem Artikel werden Erfassungs Fehler in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 7684ea11b03113051580e3e19aef0d9ac3f13585
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011481"
---
# <a name="ingestion-failures"></a>Erfassungs Fehler

## <a name="show-ingestion-failures"></a>. einerfassungs Fehler anzeigen


Dieser Befehl gibt ein Resultset mit allen Erfassungs Fehlern zurück, die auftreten, wenn die Befehle zur Erfassung der [Daten](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) Erfassung ausgeführt werden.


> [!NOTE]
> Erfassungs Fehler, die während anderer Teile des Erfassungs Flusses auftreten, werden nicht im Resultset dieses Befehls angezeigt. Ein solcher Fehler kann z. b. auftreten, bevor Befehle zur Erfassung von Daten an den Kusto-Datenmodul Dienst gesendet werden.

> Weitere Informationen zum Überwachen von Fehlern, die in Flows auftreten, die eine in der [Warteschlange](../api/netfx/about-kusto-ingest.md#queued-ingestion)befindliche Erfassung betreffen, finden Sie in [diesem Handbuch](../api/netfx/kusto-ingest-client-status.md).

**Syntax**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |Gibt alle aufgezeichneten Erfassungs Fehler zurück.  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |Gibt einen gefilterten Satz von Erfassungs Fehlern zurück.
|`.show``ingestion` `failures` `with(OperationId="` *OperationId*`")` |Gibt Erfassungs Fehler für eine bestimmte Vorgangs-ID zurück.

**Ergebnisse**
 
|Output-Parameter           |Type     |BESCHREIBUNG                                                                              |
|---------------------------|---------|-----------------------------------------------------------------------------------------|
|OperationId                |String   |Vorgangs Bezeichner, der zum Anzeigen zusätzlicher Vorgangs Details verwendet werden kann. <br> Befehl zum [Anzeigen von Vorgängen](operations.md) </br> 
|Datenbank                   |String   |Datenbank, auf der der Fehler aufgetreten ist
|Tabelle                      |String   |Tabelle, in der der Fehler aufgetreten ist
|Failedon                   |Datetime |Datum/Uhrzeit (in UTC), als der Fehler registriert wurde 
|Ingestionsourcepath        |String   |Identifiziert die Erfassungs Quelle (in der Regel ein Azure-BLOB-URI). 
|Details                    |String   |Fehlerdetails. Bietet Einblicke in die tatsächliche Erfassungs Fehlerursache
|Failurekind                |String   |Typ des Fehlers (permanent/vorübergehend)
|RootActivityId             |String   |Stamm Aktivitäts-ID.
|Operationkind              |String   |Der Typ der Erfassungs Operation (Phase), in dem der Fehler registriert wurde
|Originatesfromupdatepolicy |Boolescher Wert | Gibt an, ob der Fehler beim Ausführen einer [Update Richtlinie](update-policy.md) registriert wurde.
 
**Beispiel**
 
|OperationId |Datenbank |Tabelle |Failedon |Ingestionsourcepath |Details |Failurekind |RootActivityId |Operationkind |Originatesfromupdatepolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395gehörlos |DB1 |Table1 |2017-02-14 22:25:03.1147331 |... URL... |Der Stream mit der ID "* * * * *. csv" weist ein falsch formatiertes CSV-Format auf. * |Dauerhaft |3c883942-e446-4999-9b00-d4c664f 06ef6 |Dataingestpull | 0
|841-Datei-4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |... URL... |Der Stream mit der ID "* * * * *. csv" weist ein falsch formatiertes CSV-Format auf. * |Dauerhaft |48571bdb-B714-4f 32-8ddc-4001838a956c |Dataingestpull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |... URL... |Der Stream mit der ID "* * * * *. csv" weist ein falsch formatiertes CSV-Format auf. * |Dauerhaft |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |Dataingestpull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |... URL... |Unbekannter Fehler: Ausnahme vom Typ "System. Exception". |Transient (vorübergehend) |9b7bb017-471e-48f6-9c96-d16fcf938d2a |Dataingestpull | 0
|9edb3ecc-f 4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |... URL... |Fehler beim Herunterladen des BLOBs: der Client konnte den Vorgang nicht innerhalb des angegebenen Timeouts beenden. |Dauerhaft |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |Dataingestpull | 0
