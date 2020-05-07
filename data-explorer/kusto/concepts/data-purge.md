---
title: 'Daten Löschung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird das Löschen von Daten in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: e24edd1f47318d1ee12bfead83d2e09f67a6cc7a
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907170"
---
# <a name="data-purge"></a>Datenbereinigung

>[!Note]
> Dieser Artikel enthält Schrittanleitungen zum Löschen von personenbezogenen Daten aus einem Gerät oder Dienst und kann Sie dabei unterstützen, Ihren Pflichten gemäß DSGVO nachzukommen. Allgemeine Informationen zur dsgvo finden Sie im Abschnitt zur dsgvo [des Dienst Vertrauensstellungs Portals](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).



Als Datenplattform unterstützt Azure Daten-Explorer (Kusto) die Möglichkeit, einzelne Datensätze zu löschen, indem `.purge` und zugehörige Befehle verwendet werden. Sie können auch [eine gesamte Tabelle](#purging-an-entire-table)löschen.  

> [!WARNING]
> Das Löschen von Daten `.purge` über den-Befehl dient zum Schutz persönlicher Daten und sollte nicht in anderen Szenarien verwendet werden. Er ist nicht für die Unterstützung von häufigen Lösch Anforderungen oder das Löschen großer Datenmengen konzipiert und hat möglicherweise eine erhebliche Auswirkung auf die Leistung des Dienstanbieter.

## <a name="purge-guidelines"></a>Richtlinien zum Löschen

Es wird **dringend empfohlen** , dass Teams, die personenbezogene Daten in Azure speichern Daten-Explorer dies nur nach einem sorgfältigen Entwurf des Daten Schemas und der Untersuchung relevanter Richtlinien durchführen.

1. In einem Best-Case-Szenario ist die Beibehaltungs Dauer für diese Daten ausreichend kurz, und die Daten werden automatisch gelöscht.
2. Wenn die Verwendung von Beibehaltungs Dauer nicht möglich ist, wird empfohlen, alle Daten mit Datenschutzregeln in einer kleinen Anzahl von Kusto-Tabellen (optimal, nur eine Tabelle) zu isolieren und aus allen anderen Tabellen zu verknüpfen. Dadurch kann der Daten Lösch [Vorgang](#purge-process) für eine kleine Anzahl von Tabellen ausgeführt werden, die sensible Daten enthält, und alle anderen Tabellen werden vermieden.
3. Der Aufrufer sollte jeden Versuch durchführen, die `.purge` Ausführung von Befehlen in 1-2-Befehle pro Tabelle pro Tag zu verpacken.
   Geben Sie nicht mehrere Befehle aus, die jeweils über ein eigenes Benutzer Identitäts Prädikat verfügen. Senden Sie stattdessen einen einzelnen Befehl, dessen Prädikat alle Benutzer Identitäten enthält, die gelöscht werden müssen.

## <a name="purge-process"></a>Prozess bereinigen

Der Prozess der selektiven Löschung von Daten aus Azure Daten-Explorer erfolgt in den folgenden Schritten:

1. **Phase 1:** Bei Verwendung eines Kusto-Tabellennamens und eines Daten Satz Prädikats, das angibt, welche Datensätze gelöscht werden sollen, durchsucht Kusto die Tabelle, um Daten-Shards zu identifizieren, die am Daten Löschvorgang beteiligt sind (einen oder mehrere Datensätze aufweisen, für die das Prädikat true zurückgibt).
2. **Phase 2: (vorläufiges löschen)** Ersetzen Sie jeden datenshard in der Tabelle (in Schritt (1) identifiziert) durch eine erneut erfasste Version, die keine Datensätze enthält, für die das Prädikat true zurückgibt.
   Solange keine neuen Daten in der Tabelle erfasst werden, werden vom Ende dieser Phase keine Daten mehr zurückgegeben, für die das Prädikat "true" zurückgibt. 
   Die Dauer der leeren Lösch Phase von Lösch Vorgängen hängt von der Anzahl der Datensätze ab, die bereinigt werden müssen, der Verteilung auf die Daten-Shards im Cluster, der Anzahl der Knoten im Cluster, der für Löschvorgänge verwendeten freien Kapazität und verschiedener anderer Faktoren. Die Dauer von Phase 2 kann zwischen einigen Sekunden und vielen Stunden variieren.
3. **Phase 3: (hartes löschen)** Arbeiten Sie alle Speicher Artefakte zurück, die möglicherweise die "nicht verarbeitbaren" Daten enthalten, und löschen Sie Sie aus dem Speicher. Diese Phase wird mindestens 5 Tage *nach* dem Abschluss der vorherigen Phase ausgeführt, aber nicht mehr als 30 Tage nach dem ersten Befehl, um die Datenschutzanforderungen einzuhalten.

Durch das `.purge` ausgeben eines Befehls wird dieser Prozess ausgelöst, der einige Tage in Anspruch nimmt. Beachten Sie Folgendes: Wenn die "Dichte" der Datensätze, für die das Prädikat gilt, ausreichend groß ist, werden alle Daten in der Tabelle durch den Prozess erneut erfasst, wodurch sich die Leistung und die COGS erheblich beeinträchtigen.


## <a name="purge-limitations-and-considerations"></a>Entfernen von Einschränkungen und Überlegungen

* **Der Löschvorgang ist endgültig und**nicht rückgängig. Es ist nicht möglich, diesen Prozess "Rückgängig" zu machen oder Daten wiederherzustellen, die gelöscht wurden. Daher können Befehle wie z. b. eine [Rückgängig-Tabelle](../management/undo-drop-table-command.md) nicht gelöschte Daten wiederherstellen, und ein Rollback der Daten auf eine frühere Version kann nicht vor dem letzten Löschbefehl durchgeführt werden.

* Um Fehler zu vermeiden, wird empfohlen, das Prädikat zu überprüfen, indem Sie vor dem Löschen eine Abfrage ausführen, um sicherzustellen, dass die Ergebnisse dem erwarteten Ergebnis entsprechen, oder Sie verwenden den zweistufigen Prozess, der die erwartete Anzahl von Datensätzen zurückgibt, die bereinigt werden. 

* Als Vorsichtsmaßnahme wird der Löschvorgang standardmäßig auf allen Clustern deaktiviert.
   Das Aktivieren des Löschvorgangs ist ein einmal Vorgang, der das Öffnen eines [Support Tickets](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)erfordert. Geben Sie an, dass die `EnabledForPurge` Funktion aktiviert werden soll.

* Der `.purge` Befehl wird für den Datenverwaltung Endpunkt ausgeführt: `https://ingest-[YourClusterName].kusto.windows.net`.
   Der-Befehl erfordert [Datenbankadministrator](../management/access-control/role-based-authorization.md) -Berechtigungen für die entsprechenden Datenbanken. 
* Aufgrund der Auswirkungen auf den Löschvorgang auf die Leistung und um sicherzustellen, dass die Lösch [Richtlinien](#purge-guidelines) befolgt wurden, sollte der Aufrufer das Datenschema so ändern, dass die minimalen Tabellen relevante Daten und Batch Befehle pro Tabelle enthalten, um die erheblichen Auswirkungen des Löschvorgangs auf COGS zu verringern.
* Der `predicate` -Parameter des [. Purge](#purge-table-tablename-records-command) -Befehls wird verwendet, um anzugeben, welche Datensätze gelöscht werden sollen.
`Predicate`die Größe ist auf 63 KB beschränkt. Beim Erstellen von `predicate`:
    * Verwenden Sie den [Operator "in"](../query/inoperator.md), z `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')`. b.. 
        * Beachten Sie die Grenzwerte für den [Operator "in"](../query/inoperator.md) (die Liste kann `1,000,000` bis zu Werte enthalten).
    * Wenn die Abfrage Größe groß ist, verwenden Sie den ["externaldata"](../query/externaldata-operator.md)-Operator `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])`, z. b.. In der Datei wird die Liste der zu löschenden IDs gespeichert.
        * Die Gesamtgröße der Abfrage nach dem Erweitern aller externaldata-blobspeicher (Gesamtgröße aller blobdaten) darf 64 MB nicht überschreiten. 

## <a name="purge-performance"></a>Leistung bereinigen

Nur eine Löschanforderung kann zu einem beliebigen Zeitpunkt auf dem Cluster ausgeführt werden. Alle anderen Anforderungen werden in eine Warteschlange eingereiht. Die Warteschlangen Größe für Lösch Anforderungen muss überwacht werden, und Sie muss innerhalb angemessener Grenzwerte aufbewahrt werden, um den für Ihre Daten geltenden Anforderungen gerecht zu werden.

So reduzieren Sie die Lösch Ausführungszeit:
* Verringern Sie die Menge der gelöerten Daten durch die folgenden Lösch [Richtlinien](#purge-guidelines) .
* Passen Sie die [Cachingrichtlinie](../management/cachepolicy.md) an, da das Löschen bei kaltdaten länger dauert
* Horizontales hochskalieren des Clusters

* Erhöhen Sie die Cluster Bereinigungs Kapazität nach sorgfältiger Überlegung, wie unter Erweitern der Bereinigungs [Kapazität von Blöcken](../management/capacitypolicy.md#extents-purge-rebuild-capacity)beschrieben. Zum Ändern dieses Parameters müssen Sie ein [Support Ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) öffnen.

## <a name="trigger-the-purge-process"></a>Löschen des Löschvorgangs

> [!Note]
> Die Lösch Ausführung wird aufgerufen, indem der Befehl zum Löschen der [ *TableName* Records-Tabelle](#purge-table-tablename-records-command) für den Datenverwaltung Endpunkt ausgeführt wird (**https://ingest-[yourclustername]. [ Region]. Kusto. Windows. net**).

### <a name="purge-table-tablename-records-command"></a>Befehl zum Löschen der Tabelle *TableName* Records

Der Löschbefehl kann für unterschiedliche Verwendungs Szenarien auf zwei Arten aufgerufen werden:
1. Programm gesteuerter Aufruf: ein einzelner Schritt, der von Anwendungen aufgerufen werden soll. Wenn Sie diesen Befehl direkt aufrufen, wird die Lösch Ausführungssequenz ausgelöst.

    **Syntax**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > Generieren Sie diesen Befehl mithilfe der cslcommandgenerator-API, die als Teil des nuget-Pakets der [Kusto-Client Bibliothek](../api/netfx/about-kusto-data.md) verfügbar ist.

1. Menschlicher Aufruf: ein zweistufiger Prozess, für den eine explizite Bestätigung als separater Schritt erforderlich ist. Beim ersten Aufruf des Befehls wird ein Überprüfungs Token zurückgegeben, das bereitgestellt werden muss, um den eigentlichen Lösch Betrieb auszuführen. Diese Sequenz verringert das Risiko, dass falsche Daten versehentlich gelöscht werden. Die Verwendung dieser Option kann für große Tabellen mit erheblichen kaltcache-Daten eine lange Zeit in Anspruch nehmen.
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
    
    |Parameter  |BESCHREIBUNG  |
    |---------|---------|
    | DatabaseName   |   Name der Datenbank      |
    | TableName     |     Name der Tabelle    |
    | Predicate    |    Identifiziert die zu löschenden Datensätze. Weitere Informationen finden Sie weiter unten unter Bereinigungs Einschränkungen. | 
    | noregrets    |     Wenn festgelegt, wird eine einstufige Aktivierung ausgelöst.    |
    | verificationtoken     |  Im Szenario mit zweistufiger Aktivierung (**noregrets** ist nicht festgelegt) kann dieses Token verwendet werden, um den zweiten Schritt auszuführen und einen Commit für die Aktion auszuführen. Wenn **verificationtoken** nicht angegeben wird, wird der erste Schritt des Befehls, in dem Informationen zum Löschen zurückgegeben werden, und ein Token, das an den Befehl zurückgegeben werden soll, zurückgegeben, um Schritt #2 auszuführen.   |

    **Einschränkungen für Prädikate bereinigen**
    * Das Prädikat muss eine einfache Auswahl sein (z. b. *WHERE [columnName] = = ' x '* / *WHERE [columnName] in ("x", "Y", "Z") und [othercolumn] = = "a"*).
    * Mehrere Filter müssen mit einem "and" und nicht mit separaten `where` Klauseln kombiniert werden (z. `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` b. `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'`und nicht).
    * Das Prädikat kann nicht auf Tabellen verweisen, die nicht die Tabelle sind, die gelöscht wird (*TableName*). Das Prädikat kann nur die Selection-Anweisung (`where`) enthalten. Es kann keine bestimmten Spalten aus der Tabelle projizieren (Ausgabe Schema beim Ausführen von "* `table` |"). Prädikat*' muss dem Tabellen Schema entsprechen).
    * System Funktionen (z. b `ingestion_time()`. `extent_id()`,,) werden nicht als Teil des Prädikats unterstützt.

#### <a name="example-two-step-purge"></a>Beispiel: Löschen mit zwei Schritten

1. Führen Sie zum Initiieren des Lösch Verfahrens in einem zweistufigen Aktivierungs Szenario Schritt #1 des Befehls aus:

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **Ausgabe**

    |Numrecordstopurge |Estimatedpurgeexecutiontime| Verificationtoken
    |--|--|--
    |1.596 |00:00:02 |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    Validieren Sie den numrecordstopurge vor dem Ausführen des Schritts #2. 
2. Verwenden Sie zum Ausführen eines Löschvorgangs in einem zweistufigen Aktivierungs Szenario das von Schritt #1 zurückgegebene Überprüfungs Token, um Schritt #2 auszuführen:

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **Ausgabe**

    |OperationId |DatabaseName |TableName |ScheduledTime |Duration |Lastupdatedon |Engineoperationid |Zustand |Statedetails |Enginestarttime |Engineduration |Wiederholungsversuche |ClientRequestId |Prinzipal
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Geplant | | | |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...

#### <a name="example-single-step-purge"></a>Beispiel: Löschen mit einem Schritt

Führen Sie den folgenden Befehl aus, um in einem Aktivierungs Szenario mit nur einem Schritt eine Löschung zu initiieren:

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
    ```

**Ausgabe**

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |Lastupdatedon |Engineoperationid |Zustand |Statedetails |Enginestarttime |Engineduration |Wiederholungsversuche |ClientRequestId |Prinzipal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Geplant | | | |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...

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

Die Ausgabe dieses Befehls entspricht der Befehlsausgabe "Show löscht *operationId*" und zeigt den aktualisierten Status des abgebrochenen Löschvorgangs an. Wenn der Versuch erfolgreich ist, wird der Vorgangs Status auf "abgebrochen" aktualisiert; andernfalls wird der Vorgangs Zustand nicht geändert. 

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |Lastupdatedon |Engineoperationid |Zustand |Statedetails |Enginestarttime |Engineduration |Wiederholungsversuche |ClientRequestId |Prinzipal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...

## <a name="track-purge-operation-status"></a>Status des Löschvorgangs nachverfolgen 

> [!Note]
> Löschvorgänge können mit dem Befehl zum [anzeigen](#show-purges-command) von Lösch Vorgängen nachverfolgt werden, der für den Datenverwaltung Endpunkt (**https://ingest-[yourclustername]) ausgeführt wird. [ Region]. Kusto. Windows. net**).

Status = ' abgeschlossen ' gibt den erfolgreichen Abschluss der ersten Phase des Löschvorgangs an, d. h. Datensätze werden vorläufig gelöscht und sind nicht mehr für Abfragen verfügbar. Es ist **nicht** zu erwarten, dass Kunden den Abschluss der zweiten Phase (Hard-Delete) nachverfolgen und überprüfen. Diese Phase wird intern von Kusto überwacht.

### <a name="show-purges-command"></a>Löschbefehl anzeigen

`Show purges`Befehl zeigt den Status des Löschvorgangs an, indem die Vorgangs-ID innerhalb des angeforderten Zeitraums angegeben wird. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|Eigenschaften  |BESCHREIBUNG  |Obligatorisch/optional
|---------|---------|
|OperationId    |      Die Datenverwaltung Vorgangs-ID, die nach der Ausführung einer Phase oder einer zweiten Phase ausgegeben wird.   |Obligatorisch.
|StartDate    |   Unteres Zeit Limit für Filter Vorgänge. Wenn der Wert weggelassen wird, wird standardmäßig 24 Stunden vor der aktuellen Uhrzeit verwendet      |Optional
|EndDate    |  Ober Zeit Limit für Filter Vorgänge. Wenn der Wert weggelassen wird, wird standardmäßig die aktuelle Zeit       |Optional
|DatabaseName    |     Der Datenbankname zum Filtern von Ergebnissen.    |Optional

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

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |Lastupdatedon |Engineoperationid |Zustand |Statedetails |Enginestarttime |Engineduration |Wiederholungsversuche |ClientRequestId |Prinzipal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Abgeschlossen |Löschvorgang erfolgreich abgeschlossen (Speicher Artefakte ausstehend gelöscht) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |KE. RunCommand; 1d0ad28b-f 791-4f-a-a60b-s32318367b7 |Aad-APP-ID =...

* **OperationId** : die ID des DM-Vorgangs, die beim Ausführen der Löschung zurückgegeben wird 
* **DatabaseName** -Datenbankname (Groß-/Kleinschreibung wird beachtet) 
* **TableName** -Tabellenname (Groß-/Kleinschreibung wird beachtet). 
* **Scheduledtime** : Zeitpunkt der Ausführung des Lösch Befehls für den DM-Dienst. 
* **Dauer** : die gesamte Dauer des Löschvorgangs, einschließlich der Ausführungs Wartezeit der DM-Warteschlange. 
* **Engineoperationid** : die Vorgangs-ID des eigentlichen Löschvorgangs, der in der Engine ausgeführt wird. 
* **Status:** löschen kann eine der folgenden sein: 
    * Der Vorgang für das geplante Löschen ist für die Ausführung geplant. Wenn der Auftrag **geplant**bleibt, gibt es wahrscheinlich einen Rückstand an Lösch Vorgängen. Weitere Informationen zu diesem Rückstand finden Sie unter Löschen der [Leistung](#purge-performance) . Wenn bei einem Löschvorgang bei einem vorübergehenden Fehler ein Fehler auftritt, wird er von der DM wiederholt, und der Vorgang wird erneut **geplant** (daher wird möglicherweise ein Vorgangs Übergang von " **geplant** " zu " **InProgress** " und zurück zu " **geplant**" angezeigt).
    * InProgress-die Löschoperation wird in der-Engine ausgeführt. 
    * Abgeschlossen: der Löschvorgang wurde erfolgreich abgeschlossen.
    * Badinput-Fehler beim Löschen bei fehlerhafter Eingabe, und es wird kein erneuter Versuch unternommen. Dies kann auf verschiedene Probleme zurückzuführen sein, wie z. b. ein Syntax Fehler im Prädikat, ein ungültiges Prädikat für Lösch Befehle, eine Abfrage, die Grenzwerte überschreitet (z. b. über 1 Mio. Entitäten in einem externaldata-Operator oder mehr als 64 MB der Gesamtzahl der erweiterten Abfragen) und 404-oder 403-Fehler für externaldata
    * Fehler: Fehler beim Löschen, und es wird kein erneuter Versuch unternommen. Dies kann vorkommen, wenn der Vorgang zu lange in der Warteschlange gewartet hat (über 14 Tage), aufgrund eines Backlogs von anderen Lösch Vorgängen oder einer Anzahl von Fehlern, die das Wiederholungs Limit überschreiten. Letztere generiert eine interne Überwachungs Warnung und wird vom Azure Daten-Explorer-Team untersucht. 
* Statedetails-eine Beschreibung des Zustands.
* Enginestarttime: die Zeit, zu der der Befehl für die Engine ausgegeben wurde. Wenn es zwischen dieser Zeit und scheduledtime einen großen Unterschied gibt, gibt es in der Regel einen großen Rückstand an Lösch Vorgängen, und der Cluster ist nicht in der Geschwindigkeit. 
* Engineduration: Zeitpunkt der eigentlichen Lösch Ausführung in der Engine. Wenn das Löschen mehrmals wiederholt wurde, ist dies die Summe aller Ausführungs dauern. 
* Wiederholungen: Häufigkeit, mit der der Vorgang durch den DM-Dienst aufgrund eines vorübergehenden Fehlers wiederholt wurde.
* Clientrequestid: Client Aktivitäts-ID der DM-Löschanforderung. 
* Prinzipal-ID des Ausstellers für den Löschbefehl.

## <a name="purging-an-entire-table"></a>Löschen einer gesamten Tabelle
Das Löschen einer Tabelle umfasst das Löschen der Tabelle und das Markieren als gelöscht, sodass der unter Lösch [Vorgang](#purge-process) beschriebene Prozess für die harte Löschung auf diesem gelöscht wird. Wenn Sie eine Tabelle löschen, ohne Sie zu löschen, werden nicht alle zugehörigen Speicher Artefakte gelöscht (gelöscht gemäß der Anfangs Richtlinie, die für die Tabelle festgelegt wurde). Der `purge table allrecords` Befehl ist schnell und effizient und ist dem Vorgang zum Löschen von Datensätzen, sofern dies für Ihr Szenario zutrifft, sehr vorzuziehen. 

> [!Note]
> Der Befehl wird aufgerufen, indem der Befehl "Lösch [Tabelle *TableName* allrecords](#purge-table-tablename-allrecords-command) " für den Datenverwaltung Endpunkt (**https://ingest-[yourclustername]) ausgeführt wird. [ Region]. Kusto. Windows. net**).

### <a name="purge-table-tablename-allrecords-command"></a>Tabelle " *TableName* allrecords" löschen (Befehl)

Ähnlich wie bei dem Befehl "[. Purge Table Records ](#purge-table-tablename-records-command)" kann dieser Befehl in einem programmgesteuerten (einzelnen Schritt) oder in einem manuellen (zweistufigen) Modus aufgerufen werden.
1. Programm gesteuerter Aufruf (Einzelschritt):

     **Syntax**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. Menschlicher Aufruf (zwei Schritte):

     **Syntax**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |Parameter  |BESCHREIBUNG  |
    |---------|---------|
    |**DatabaseName**   |   Der Name der Datenbank.      |
    |**TableName**     |     Der Name der Tabelle.    |
    |**noregrets**    |     Wenn festgelegt, wird eine einstufige Aktivierung ausgelöst.    |
    |**verificationtoken**     |  Im Szenario mit zweistufiger Aktivierung (**noregrets** ist nicht festgelegt) kann dieses Token verwendet werden, um den zweiten Schritt auszuführen und einen Commit für die Aktion auszuführen. Wenn **verificationtoken** nicht angegeben wird, wird der erste Schritt des Befehls, in dem ein Token zurückgegeben wird, zurückgegeben, der an den Befehl zurückgegeben wird, und der Schritt #2 des Befehls ausgeführt.|

#### <a name="example-two-step-purge"></a>Beispiel: Löschen mit zwei Schritten

1. Führen Sie zum Initiieren des Lösch Verfahrens in einem zweistufigen Aktivierungs Szenario Schritt #1 des Befehls aus: 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **Ausgabe**

    | Verificationtoken
    |--
    |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

1.  Verwenden Sie zum Ausführen eines Löschvorgangs in einem zweistufigen Aktivierungs Szenario das von Schritt #1 zurückgegebene Überprüfungs Token, um Schritt #2 auszuführen:

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    Die Ausgabe ist identisch mit der Befehlsausgabe ". Show Tables" (wird ohne die gelöerte Tabelle zurückgegeben).

    **Ausgabe**

    |TableName|DatabaseName|Ordner|DocString
    |---|---|---|---
    |Othertable|MyDatabase|---|---


#### <a name="example-single-step-purge"></a>Beispiel: Löschen mit einem Schritt

Führen Sie den folgenden Befehl aus, um in einem Aktivierungs Szenario mit nur einem Schritt eine Löschung zu initiieren:

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

Die Ausgabe ist identisch mit der Befehlsausgabe ". Show Tables" (wird ohne die gelöerte Tabelle zurückgegeben).

**Ausgabe**

|TableName|DatabaseName|Ordner|DocString
|---|---|---|---
|Othertable|MyDatabase|---|---


