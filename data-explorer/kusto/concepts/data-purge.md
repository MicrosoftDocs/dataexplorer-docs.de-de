---
title: 'Daten Löschung: Azure-Daten-Explorer'
description: In diesem Artikel wird das Löschen von Daten in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 05/12/2020
ms.openlocfilehash: 144e56ee89cb35900b8e55cdbcdce597b26f8a68
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225995"
---
# <a name="data-purge"></a>Datenbereinigung

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Als Datenplattform unterstützt Azure Daten-Explorer die Möglichkeit, einzelne Datensätze zu löschen, indem Kusto `.purge` und zugehörige Befehle verwendet werden. Sie können auch [eine gesamte Tabelle](#purging-an-entire-table)löschen.  

> [!WARNING]
> Das Löschen von Daten über den- `.purge` Befehl dient zum Schutz persönlicher Daten und sollte nicht in anderen Szenarien verwendet werden. Er ist nicht für die Unterstützung von häufigen Lösch Anforderungen oder das Löschen großer Datenmengen konzipiert und hat möglicherweise eine erhebliche Auswirkung auf die Leistung des Dienstanbieter.

## <a name="purge-guidelines"></a>Richtlinien zum Löschen

Entwerfen Sie sorgfältig das Datenschema, und untersuchen Sie relevante Richtlinien, bevor Sie persönliche Daten in Azure Daten-Explorer speichern.

1. In einem Best-Case-Szenario ist die Beibehaltungs Dauer für diese Daten ausreichend kurz, und die Daten werden automatisch gelöscht.
1. Wenn die Verwendung der Beibehaltungs Dauer nicht möglich ist, isolieren Sie alle Daten, die Datenschutzregeln unterliegen, in wenigen Azure-Daten-Explorer Tabellen. Verwenden Sie optimal eine Tabelle, und verknüpfen Sie Sie aus allen anderen Tabellen. Mit dieser Isolation können Sie den Daten Lösch [Vorgang](#purge-process) für eine kleine Anzahl von Tabellen mit sensiblen Daten ausführen und alle anderen Tabellen vermeiden.
1. Der Aufrufer sollte jeden Versuch durchführen, die Ausführung von `.purge` Befehlen in 1-2-Befehle pro Tabelle pro Tag zu verpacken. Geben Sie nicht mehrere Befehle mit eindeutigen Benutzer Identitäts Prädikaten aus. Senden Sie stattdessen einen einzelnen Befehl, dessen Prädikat alle Benutzer Identitäten enthält, die gelöscht werden müssen.

## <a name="purge-process"></a>Prozess bereinigen

Der Prozess der selektiven Löschung von Daten aus Azure Daten-Explorer erfolgt in den folgenden Schritten:

1. Phase 1: Geben Sie eine Eingabe mit einem Azure-Daten-Explorer Tabellennamen und einem Daten Satz Prädikat an, das angibt, welche Datensätze gelöscht werden sollen. Kusto scannt die Tabelle, in der Daten-Shards identifiziert werden, die am Daten Lösch Vorgängen beteiligt sind. Die identifizierten Shards enthalten mindestens einen Datensatz, für den das Prädikat true zurückgibt.
1. Phase 2: (vorläufiges löschen) ersetzen Sie jeden datenshard in der Tabelle (in Schritt (1) identifiziert) durch eine wieder aufgenommene Version. Die wieder aufgenommene Version sollte nicht über die Datensätze verfügen, für die das Prädikat true zurückgibt. Wenn neue Daten nicht in der Tabelle erfasst werden, geben Abfragen am Ende dieser Phase keine Daten mehr zurück, für die das Prädikat "true" zurückgibt. Die Dauer der leeren Lösch Phase für das Löschen hängt von den folgenden Parametern ab: 
     * Die Anzahl der Datensätze, die bereinigt werden müssen. 
     * Aufzeichnen der Verteilung in den Daten Shards im Cluster 
     * Die Anzahl der Knoten im Cluster  
     * Die Reservekapazität für Löschvorgänge
     * Mehrere weitere Faktoren können die Dauer von Phase 2 zwischen einigen Sekunden und vielen Stunden variieren.
1. Phase 3: (Hard Delete) arbeiten Sie alle Speicher Artefakte zurück, die möglicherweise die "nicht verarbeitbaren" Daten aufweisen, und löschen Sie Sie aus dem Speicher. Diese Phase erfolgt mindestens fünf Tage nach dem Abschluss der vorherigen Phase, aber nicht mehr als 30 Tage nach dem anfänglichen Befehl. Diese Zeitachsen werden so festgelegt, dass Sie den Datenschutzanforderungen entsprechen.

Durch das Ausgeben eines `.purge` Befehls wird dieser Prozess ausgelöst, der einige Tage in Anspruch nimmt. Wenn die Dichte der Datensätze, für die das Prädikat gilt, ausreichend groß ist, werden alle Daten in der Tabelle durch den Prozess erneut eingefügt. Diese Wiederherstellung hat erhebliche Auswirkungen auf die Leistung und die COGS.

## <a name="purge-limitations-and-considerations"></a>Entfernen von Einschränkungen und Überlegungen

* Der Löschvorgang ist endgültig und nicht rückgängig. Es ist nicht möglich, diesen Prozess rückgängig zu machen oder Daten wiederherzustellen, die gelöscht wurden. Befehle wie z. b. [Rückgängig-Tabellen](../management/undo-drop-table-command.md) Ablage können keine gelöschte Daten wiederherstellen Ein Rollback der Daten auf eine frühere Version kann nicht vor dem letzten Löschbefehl durchgeführt werden.

* Überprüfen Sie vor dem Ausführen der Löschung das Prädikat, indem Sie eine Abfrage ausführen und überprüfen, ob die Ergebnisse dem erwarteten Ergebnis entsprechen. Sie können auch den zweistufigen Prozess verwenden, der die erwartete Anzahl von Datensätzen zurückgibt, die bereinigt werden. 

* Der `.purge` Befehl wird für den Datenverwaltung Endpunkt ausgeführt: `https://ingest-[YourClusterName].[region].kusto.windows.net` .
   Der-Befehl erfordert [Datenbankadministrator](../management/access-control/role-based-authorization.md) -Berechtigungen für die entsprechenden Datenbanken. 
* Aufgrund der Auswirkungen auf den Löschvorgang auf die Leistung und um sicherzustellen, dass die Lösch [Richtlinien](#purge-guidelines) befolgt wurden, sollte der Aufrufer das Datenschema so ändern, dass die minimalen Tabellen relevante Daten und Batch Befehle pro Tabelle enthalten, um die erheblichen Auswirkungen des Löschvorgangs auf COGS zu verringern.
* Der- `predicate` Parameter des [. Purge](#purge-table-tablename-records-command) -Befehls wird verwendet, um anzugeben, welche Datensätze gelöscht werden sollen.
`Predicate`die Größe ist auf 63 KB beschränkt. Beim Erstellen von `predicate` :
    * Verwenden Sie den [Operator "in"](../query/inoperator.md), z `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')` . b.. 
    * Beachten Sie die Grenzwerte für den [Operator "in"](../query/inoperator.md) (die Liste kann bis zu `1,000,000` Werte enthalten).
    * Wenn die Abfrage Größe groß ist, verwenden Sie z. b. den- [ `externaldata` Operator](../query/externaldata-operator.md) `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` . In der Datei wird die Liste der zu löschenden IDs gespeichert.
    * Die Gesamtgröße der Abfrage nach dem Erweitern aller `externaldata` blobspeicher (Gesamtgröße aller blobspeicher) darf 64 MB nicht überschreiten. 

## <a name="purge-performance"></a>Leistung bereinigen

Nur eine Löschanforderung kann zu einem beliebigen Zeitpunkt auf dem Cluster ausgeführt werden. Alle anderen Anforderungen werden in die Warteschlange eingereiht `Scheduled` . Überwachen Sie die Warteschlangen Größe für Lösch Anforderungen, und behalten Sie die entsprechenden Grenzwerte bei, um die Anforderungen für Ihre Daten zu erfüllen.

So reduzieren Sie die Lösch Ausführungszeit:
* Befolgen Sie die [Richtlinien](#purge-guidelines) zum Bereinigen, um die Menge der gelöerten Daten zu verringern.
* Passen Sie die [Cachingrichtlinie](../management/cachepolicy.md) an, da das Löschen bei kaltdaten länger dauert
* Horizontales hochskalieren des Clusters

* Erhöhen Sie die Cluster Bereinigungs Kapazität nach sorgfältiger Überlegung, wie unter Erweitern der Bereinigungs [Kapazität von Blöcken](../management/capacitypolicy.md#extents-purge-rebuild-capacity)beschrieben. Zum Ändern dieses Parameters müssen Sie ein [Support Ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) öffnen.

## <a name="trigger-the-purge-process"></a>Löschen des Löschvorgangs

> [!Note]
> Die Lösch Ausführung wird aufgerufen, indem der Befehl zum Löschen der [ *TableName* Records-Tabelle](#purge-table-tablename-records-command) für den Datenverwaltung Endpunkt https://ingest- [yourclustername] ausgeführt wird. [ Region]. Kusto. Windows. net.

### <a name="purge-table-tablename-records-command"></a>Befehl zum Löschen der Tabelle TableName Records

Der Löschbefehl kann für unterschiedliche Verwendungs Szenarien auf zwei Arten aufgerufen werden:

* Programm gesteuerter Aufruf: ein einzelner Schritt, der von Anwendungen aufgerufen werden soll. Wenn Sie diesen Befehl direkt aufrufen, wird die Lösch Ausführungssequenz ausgelöst.

    **Syntax**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > Generieren Sie diesen Befehl mithilfe der cslcommandgenerator-API, die als Teil des nuget-Pakets der [Kusto-Client Bibliothek](../api/netfx/about-kusto-data.md) verfügbar ist.

* Menschlicher Aufruf: ein zweistufiger Prozess, für den eine explizite Bestätigung als separater Schritt erforderlich ist. Beim ersten Aufruf des Befehls wird ein Überprüfungs Token zurückgegeben, das bereitgestellt werden muss, um den eigentlichen Lösch Betrieb auszuführen. Diese Sequenz verringert das Risiko, dass falsche Daten versehentlich gelöscht werden. Die Verwendung dieser Option kann für große Tabellen mit erheblichen kaltcache-Daten eine lange Zeit in Anspruch nehmen.
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **Syntax**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    | Parameter  | BESCHREIBUNG  |
    |---------|---------|
    | `DatabaseName`   |   Name der Datenbank      |
    | `TableName`     |     Name der Tabelle    |
    | `Predicate`    |    Identifiziert die zu löschenden Datensätze. Weitere Informationen finden Sie weiter unten unter Bereinigungs Einschränkungen. | 
    | `noregrets`    |     Wenn festgelegt, wird eine einstufige Aktivierung ausgelöst.    |
    | `verificationtoken`     |  Im zweistufigen Aktivierungs Szenario ( `noregrets` ist nicht festgelegt) kann dieses Token verwendet werden, um den zweiten Schritt auszuführen und einen Commit für die Aktion auszuführen. Wenn `verificationtoken` nicht angegeben ist, wird der erste Schritt des Befehls auslöst. Informationen zum Löschvorgang werden mit einem Token zurückgegeben, das an den Befehl zurückgegeben werden soll, um Schritt #2 ausführen zu können.   |

    **Einschränkungen für Prädikate bereinigen**

    * Das Prädikat muss eine einfache Auswahl sein (z. b. *WHERE [columnName] = = ' x '*  /  *WHERE [columnName] in (' x ', ' Y ', ' Z ') und [othercolumn] = = ' a '*).
    * Mehrere Filter müssen mit einem "and" und nicht mit separaten Klauseln kombiniert werden `where` (z. b `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` . und nicht `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'` ).
    * Das Prädikat kann nicht auf Tabellen verweisen, die nicht die Tabelle sind, die gelöscht wird (*TableName*). Das Prädikat kann nur die Selection-Anweisung ( `where` ) enthalten. Es kann keine bestimmten Spalten aus der Tabelle projizieren (Ausgabe Schema beim Ausführen von "|").* `table` Prädikat*' muss dem Tabellen Schema entsprechen).
    * System Funktionen (z. b `ingestion_time()` .,, `extent_id()` ) werden nicht unterstützt.

#### <a name="example-two-step-purge"></a>Beispiel: Löschen mit zwei Schritten

Um das Löschen in einem zweistufigen Aktivierungs Szenario zu starten, führen Sie Schritt #1 des Befehls aus:

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **Output**

    | Numrecordstopurge | Estimatedpurgeexecutiontime| Verificationtoken
    |--|--|--
    | 1.596 | 00:00:02 | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    Then, validate the NumRecordsToPurge before running step #2. 

Verwenden Sie zum Ausführen eines Löschvorgangs in einem zweistufigen Aktivierungs Szenario das von Schritt #1 zurückgegebene Überprüfungs Token, um Schritt #2 auszuführen:

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **Output**

    | `OperationId` | `DatabaseName` | `TableName`|`ScheduledTime` | `Duration` | `LastUpdatedOn` |`EngineOperationId` | `State` | `StateDetails` |`EngineStartTime` | `EngineDuration` | `Retries` |`ClientRequestId` | `Principal`|
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--|
    | c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Geplant | | | |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...|

#### <a name="example-single-step-purge"></a>Beispiel: Löschen mit einem Schritt

Führen Sie den folgenden Befehl aus, um in einem Aktivierungs Szenario mit nur einem Schritt eine Löschung zu initiieren:

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
    ```

**Ausgabe**

| `OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
| c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Geplant | | | |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...|

### <a name="cancel-purge-operation-command"></a>Befehl "Löschvorgang Abbrechen"

Bei Bedarf können Sie ausstehende Lösch Anforderungen abbrechen.

> [!NOTE]
> Dieser Vorgang ist für Fehler Wiederherstellungs Szenarien vorgesehen. Es ist nicht garantiert, dass es erfolgreich ist und nicht Teil eines normalen Betriebs Flusses sein sollte. Sie kann nur auf Warteschlangen Anforderungen angewendet werden (noch nicht zur Ausführung an den Engine-Knoten weitergeleitet). Der Befehl wird auf dem Datenverwaltung-Endpunkt ausgeführt.

**Syntax**

```kusto
 .cancel purge <OperationId>
 ```

**Beispiel**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**Ausgabe**

Die Ausgabe dieses Befehls entspricht der Befehlsausgabe "Show löscht *operationId*" und zeigt den aktualisierten Status des abgebrochenen Löschvorgangs an. Wenn der Versuch erfolgreich ist, wird der Vorgangs Status auf aktualisiert `Abandoned` . Andernfalls wird der Vorgangs Zustand nicht geändert. 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...

## <a name="track-purge-operation-status"></a>Status des Löschvorgangs nachverfolgen 

> [!Note]
> Löschvorgänge können mit dem Befehl [Show](#show-purges-command) Löschvorgänge nachverfolgt werden, der für den Datenverwaltung Endpunkt https://ingest- [yourclustername] ausgeführt wird. [ Region]. Kusto. Windows. net.

Status = ' abgeschlossen ' gibt den erfolgreichen Abschluss der ersten Phase des Löschvorgangs an, d. h. Datensätze werden vorläufig gelöscht und sind nicht mehr für Abfragen verfügbar. Kunden sollten den Abschluss der zweiten Phase (fest Löschvorgang) **nicht** verfolgen und überprüfen. Diese Phase wird intern von Azure Daten-Explorer überwacht.

### <a name="show-purges-command"></a>Löschbefehl anzeigen

`Show purges`Befehl zeigt den Status des Löschvorgangs an, indem die Vorgangs-ID innerhalb des angeforderten Zeitraums angegeben wird. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

| Eigenschaften  |BESCHREIBUNG  |Obligatorisch/optional|
|---------|------------|-------|
|`OperationId `   |      Die Datenverwaltung Vorgangs-ID, die nach der Ausführung einer Phase oder einer zweiten Phase ausgegeben wird.   |Obligatorisch.
|`StartDate`    |   Unteres Zeit Limit für Filter Vorgänge. Wenn der Wert weggelassen wird, wird standardmäßig 24 Stunden vor der aktuellen Uhrzeit verwendet      |Optional
|`EndDate`    |  Ober Zeit Limit für Filter Vorgänge. Wenn der Wert weggelassen wird, wird standardmäßig die aktuelle Zeit       |Optional
|`DatabaseName`    |     Der Datenbankname zum Filtern von Ergebnissen.    |Optional

> [!NOTE]
> Der Status wird nur für Datenbanken bereitgestellt, die über [Datenbankadministrator](../management/access-control/role-based-authorization.md) Berechtigungen verfügen.

**Beispiele**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**Ausgabe** 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Abgeschlossen |Löschvorgang erfolgreich abgeschlossen (Speicher Artefakte ausstehend gelöscht) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...

* `OperationId`-die DM-Vorgangs-ID, die beim Ausführen von Lösch Vorgängen 
* `DatabaseName`* *-Datenbankname (Groß-/Kleinschreibung wird beachtet) 
* `TableName`-Tabellenname (Groß-/Kleinschreibung beachten). 
* `ScheduledTime`-Zeitpunkt des Ausführens des Lösch Befehls für den DM-Dienst. 
* `Duration`-Gesamtdauer des Löschvorgangs, einschließlich der Ausführungs Wartezeit der DM-Warteschlange. 
* `EngineOperationId`: die Vorgangs-ID des eigentlichen Löschvorgangs, der in der Engine ausgeführt wird. 
* `State`-Zustand löschen: kann einen der folgenden Werte aufweisen: 
    * `Scheduled`-der Löschvorgang ist für die Ausführung geplant. Wenn der Auftrag geplant bleibt, gibt es wahrscheinlich einen Rückstand an Lösch Vorgängen. Weitere Informationen zu diesem Rückstand finden Sie unter Löschen der [Leistung](#purge-performance) . Wenn bei einem Löschvorgang bei einem vorübergehenden Fehler ein Fehler auftritt, wird er von der DM wiederholt, und der Vorgang wird erneut geplant (daher wird möglicherweise ein Vorgangs Übergang von "geplant" zu "InProgress" und zurück zu "geplant" angezeigt).
    * `InProgress`-der Löschvorgang wird in der-Engine ausgeführt. 
    * `Completed`-Löschvorgang wurde erfolgreich abgeschlossen.
    * `BadInput`-Fehler beim Löschen bei fehlerhafter Eingabe, und es wird kein erneuter Versuch unternommen. Dieser Fehler kann auf verschiedene Probleme zurückzuführen sein, wie z. b. ein Syntax Fehler im Prädikat, ein ungültiges Prädikat für Lösch Befehle, eine Abfrage, die Grenzwerte überschreitet (z. b. über 1 Mio. Entitäten in einem `externaldata` Operator oder mehr als 64 MB Gesamtzahl der erweiterten Abfrage Größe) und 404-oder 403-Fehler für `externaldata` BLOB.
    * `Failed`-Fehler beim Löschen und wird nicht wiederholt. Dieser Fehler kann auftreten, wenn der Vorgang zu lange in der Warteschlange gewartet hat (über 14 Tage), aufgrund eines Backlogs anderer Löschvorgänge oder einer Anzahl von Fehlern, die das Wiederholungs Limit überschreiten. Letztere generiert eine interne Überwachungs Warnung und wird vom Azure Daten-Explorer-Team untersucht. 
* `StateDetails`-eine Beschreibung des Zustands.
* `EngineStartTime`-die Zeit, zu der der Befehl für die Engine ausgegeben wurde. Wenn es zwischen dieser Zeit und scheduledtime einen großen Unterschied gibt, gibt es in der Regel einen erheblichen Rückstand an Lösch Vorgängen, und der Cluster ist nicht in der Geschwindigkeit. 
* `EngineDuration`-Zeitpunkt der eigentlichen Lösch Ausführung in der Engine. Wenn das Löschen mehrmals wiederholt wurde, ist dies die Summe aller Ausführungs dauern. 
* `Retries`Gibt an, wie oft der DM-Dienst aufgrund eines vorübergehenden Fehlers wiederholt wurde.
* `ClientRequestId`-Client Aktivitäts-ID der DM-Bereinigungs Anforderung. 
* `Principal`: Identität des Ausstellers für den Löschbefehl.

## <a name="purging-an-entire-table"></a>Löschen einer gesamten Tabelle

Das Löschen einer Tabelle umfasst das Löschen der Tabelle und das Markieren als gelöscht, sodass der unter Lösch [Vorgang](#purge-process) beschriebene Prozess für die harte Löschung auf diesem gelöscht wird. Wenn eine Tabelle gelöscht wird, ohne Sie zu löschen, werden nicht alle zugehörigen Speicher Artefakte gelöscht. Diese Artefakte werden gemäß der Richtlinie für die feste Beibehaltung gelöscht, die anfänglich für die Tabelle festgelegt wurde. Der `purge table allrecords` Befehl ist schnell und effizient und ist dem Vorgang zum Löschen von Datensätzen vorzuziehen, wenn dies für Ihr Szenario zutrifft. 

> [!Note]
> Der Befehl wird aufgerufen, indem der Befehl zum Löschen der [Tabelle " *TableName* allrecords](#purge-table-tablename-allrecords-command) " für den Datenverwaltung Endpunkt https://ingest- [yourclustername] ausgeführt wird. [ Region]. Kusto. Windows. net.

### <a name="purge-table-tablename-allrecords-command"></a>Tabelle " *TableName* allrecords" löschen (Befehl)

Ähnlich wie bei dem Befehl "[. Purge Table Records ](#purge-table-tablename-records-command)" kann dieser Befehl in einem programmgesteuerten (einzelnen Schritt) oder in einem manuellen (zweistufigen) Modus aufgerufen werden.

1. Programm gesteuerter Aufruf (Einzelschritt):

     **Syntax**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

1. Menschlicher Aufruf (zwei Schritte):

     **Syntax**

     ```kusto
   
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)

     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    | Parameter  |BESCHREIBUNG  |
    |---------|---------|
    | `DatabaseName`   |   Der Name der Datenbank.      |
    | `TableName`    |     Der Name der Tabelle.    |
    | `noregrets`    |     Wenn festgelegt, wird eine einstufige Aktivierung ausgelöst.    |
    | `verificationtoken`     |  Im Szenario mit zweistufiger Aktivierung ( `noregrets` ist nicht festgelegt) kann dieses Token verwendet werden, um den zweiten Schritt auszuführen und einen Commit für die Aktion auszuführen. Wenn `verificationtoken` nicht angegeben ist, wird der erste Schritt des Befehls auslöst. In diesem Schritt wird ein Token zurückgegeben, das an den Befehl zurückgegeben wird, und Schritt #2.|

#### <a name="example-two-step-purge"></a>Beispiel: Löschen mit zwei Schritten

1. Um das Löschen in einem zweistufigen Aktivierungs Szenario zu starten, führen Sie Schritt #1 des Befehls aus: 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **Ausgabe**

    | `VerificationToken`|
    |--|
    | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b|

1.  Verwenden Sie zum Ausführen eines Löschvorgangs in einem zweistufigen Aktivierungs Szenario das von Schritt #1 zurückgegebene Überprüfungs Token, um Schritt #2 auszuführen:

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    Die Ausgabe ist identisch mit der Befehlsausgabe ". Show Tables" (wird ohne die gelöerte Tabelle zurückgegeben).

    **Ausgabe**

    |  TableName|DatabaseName|Ordner|DocString
    |---|---|---|---
    |  Othertable|MyDatabase|---|---


#### <a name="example-single-step-purge"></a>Beispiel: Löschen mit einem Schritt

Führen Sie den folgenden Befehl aus, um in einem Aktivierungs Szenario mit nur einem Schritt eine Löschung zu initiieren:

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

Die Ausgabe ist identisch mit der Befehlsausgabe ". Show Tables" (wird ohne die gelöerte Tabelle zurückgegeben).

**Ausgabe**

|TableName|DatabaseName|Ordner|DocString
|---|---|---|---
|Othertable|MyDatabase|---|---

