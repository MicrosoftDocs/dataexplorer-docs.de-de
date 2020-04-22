---
title: Datenbereinigung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Datenbereinigung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 460ad9cfca4f97e6735d30a4d47d6384581e7af7
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82029987"
---
# <a name="data-purge"></a>Datenbereinigung

>[!Note]
> Dieser Artikel enthält Schritte zum Löschen von persönlichen Daten vom Gerät oder aus dem Dienst und kann zur Unterstützung Ihrer Verpflichtungen im Rahmen der Datenschutz-Grundverordnung (DSGVO) verwendet werden. Wenn Sie nach allgemeinen Informationen zur DSGVO suchen, lesen Sie den [Abschnitt DSGVO im Service Trust-Portal](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).



Als Datenplattform unterstützt Azure Data Explorer (Kusto) die Möglichkeit, einzelne `.purge` Datensätze mithilfe von und verwandten Befehlen zu löschen. Sie können auch [eine ganze Tabelle löschen.](#purging-an-entire-table)  

> [!WARNING]
> Das Löschen `.purge` von Daten über den Befehl dient dem Schutz personenbezogener Daten und sollte nicht in anderen Szenarien verwendet werden. Es ist nicht für häufige Löschanforderungen oder das Löschen riesiger Datenmengen konzipiert und kann erhebliche Auswirkungen auf die Leistung des Dienstes haben.

## <a name="purge-guidelines"></a>Säuberungsrichtlinien

Es wird **dringend empfohlen,** dass Teams, die personenbezogene Daten in Azure Data Explorer speichern, dies nur nach sorgfältiger Entwurfsplanung des Datenschemas und Untersuchung relevanter Richtlinien tun.

1. Im besten Fall ist die Aufbewahrungsdauer für diese Daten ausreichend kurz und die Daten werden automatisch gelöscht.
2. Wenn die Verwendung des Aufbewahrungszeitraums nicht möglich ist, wird empfohlen, alle Datenschutzbestimmungen in einer kleinen Anzahl von Kusto-Tabellen (optimalerweise nur eine Tabelle) zu isolieren und von allen anderen Tabellen darauf zu verlinken. Auf diese Weise kann der [Datenbereinigungsprozess](#purge-process) für eine kleine Anzahl von Tabellen ausgeführt werden, die vertrauliche Daten enthalten, und alle anderen Tabellen vermeiden.
3. Der Aufrufer sollte jeden Versuch unternehmen, die Ausführung von `.purge` Befehlen auf 1-2 Befehle pro Tabelle pro Tag zu stapeln.
   Geben Sie nicht mehrere Befehle aus, von denen jeder ein eigenes Benutzeridentitätsprädikat enthält. Senden Sie stattdessen einen einzelnen Befehl, dessen Prädikat alle Benutzeridentitäten enthält, die gereinigt werden müssen.

## <a name="purge-process"></a>Säuberungsprozess

Der Prozess des selektiven Löschens von Daten aus Azure Data Explorer erfolgt in den folgenden Schritten:

1. **Phase 1:** Angesichts eines Kusto-Tabellennamens und eines Prädikats pro Datensatz, das angibt, welche Datensätze gelöscht werden sollen, scannt Kusto die Tabelle, um Datenshards zu identifizieren, die an der Datenbereinigung beteiligt wären (einen oder mehrere Datensätze haben, für die das Prädikat true zurückgibt).
2. **Phase 2: (Soft Delete)** Ersetzen Sie jeden Datenshard in der Tabelle (in Schritt (1) identifiziert) durch eine erneut eingeschriebene Version, die nicht über die Datensätze verfügt, für die das Prädikat true zurückgibt.
   Solange keine neuen Daten in die Tabelle aufgenommen werden, geben Abfragen am Ende dieser Phase keine Daten mehr zurück, für die das Prädikat true zurückgibt. 
   Die Dauer der Soft-Löschphase für die Bereinigung hängt von der Anzahl der Datensätze ab, die gelöscht werden müssen, von ihrer Verteilung über die Datenshards im Cluster, von der Anzahl der Knoten im Cluster, von der freien Kapazität für Bereinigungsvorgänge und von mehreren anderen Faktoren. Die Dauer von Phase 2 kann zwischen wenigen Sekunden und vielen Stunden variieren.
3. **Phase 3: (Hard Delete)** Arbeiten Sie alle Speicherartefakte zurück, die möglicherweise die "Gift"-Daten enthalten, und löschen Sie sie aus dem Speicher. Diese Phase wird mindestens 5 Tage *nach* Abschluss der vorherigen Phase, jedoch nicht länger als 30 Tage nach dem ersten Befehl durchgeführt, um die Datenschutzanforderungen zu erfüllen.

Die `.purge` Ausgabe eines Befehls löst diesen Prozess aus, der einige Tage in Anspruch nimmt. Beachten Sie, dass, wenn die "Dichte" der Datensätze, für die das Prädikat gilt, ausreichend groß ist, der Prozess effektiv alle Daten in der Tabelle wieder einbezieht, was sich somit erheblich auf die Leistung und COGS auswirkt.


## <a name="purge-limitations-and-considerations"></a>Einschränkungen und Überlegungen bereinigen

* **Der Spülprozess ist endgültig und irreversibel.** Es ist nicht möglich, diesen Prozess rückgängig zu machen oder gelöschte Daten wiederherzustellen. Daher können Befehle wie das Löschen von [Rückgängig-Tabellen](../management/undo-drop-table-command.md) keine gelöschten Daten wiederherstellen, und ein Rollback der Daten auf eine frühere Version kann nicht auf "vor" dem letzten Löschbefehl gehen.

* Um Fehler zu vermeiden, wird empfohlen, das Prädikat zu überprüfen, indem Sie eine Abfrage vor dem Bereinigen ausführen, um sicherzustellen, dass die Ergebnisse mit dem erwarteten Ergebnis übereinstimmen, oder den 2-stufigen Prozess verwenden, der die erwartete Anzahl von Datensätzen zurückgibt, die gelöscht werden. 

* Vorsorglich ist der Bereinigungsprozess standardmäßig für alle Cluster deaktiviert.
   Das Aktivieren des Bereinigungsvorgangs ist ein einmaliger Vorgang, bei dem ein [Supportticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)geöffnet werden muss. Geben Sie an, `EnabledForPurge` dass die Funktion aktiviert werden soll.

* Der `.purge` Befehl wird für den Data `https://ingest-[YourClusterName].kusto.windows.net`Management-Endpunkt ausgeführt: .
   Der Befehl erfordert [Datenbankadministratorberechtigungen](../management/access-control/role-based-authorization.md) für die relevanten Datenbanken. 
* Aufgrund der Auswirkungen auf die Auswirkungen auf die Bereinigung der Prozessleistung und um sicherzustellen, dass die [Bereinigungsrichtlinien](#purge-guidelines) eingehalten wurden, wird erwartet, dass der Aufrufer das Datenschema so ändert, dass minimale Tabellen relevante Daten und Batchbefehle pro Tabelle enthalten, um die erheblichen COGS-Auswirkungen des Bereinigungsprozesses zu reduzieren.
* Der `predicate` Parameter des [Befehls .purge](#purge-table-tablename-records-command) wird verwendet, um anzugeben, welche Datensätze bewilligt werden sollen.
`Predicate`Größe ist auf 63 KB begrenzt. Beim Bau `predicate`der:
    * Verwenden Sie den [Operator 'in'](../query/inoperator.md), z. B. `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')`. 
        * Beachten Sie die Grenzwerte des [Operators 'in'](../query/inoperator.md) (Liste kann bis zu `1,000,000` Werte enthalten).
    * Wenn die Abfragegröße groß ist, verwenden Sie den `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` [Operator 'externaldata',](../query/externaldata-operator.md)z. B. . Die Datei speichert die Liste der zu bereinigenden IDs.
        * Die Gesamtabfragegröße darf nach dem Erweitern aller externen Daten-Blobs (Gesamtgröße aller Blobs) 64 MB nicht überschreiten. 

## <a name="purge-performance"></a>Reinigungsleistung

Es kann jeweils nur eine Löschanforderung auf dem Cluster ausgeführt werden. Alle anderen Anforderungen werden im Status "Geplant" in die Warteschlange eingereiht. Die Größe der Löschanforderungswarteschlange muss überwacht und innerhalb angemessener Grenzen gehalten werden, um die für Ihre Daten geltenden Anforderungen zu erfüllen.

So reduzieren Sie die Ausführungszeit:
* Verringern Sie die Menge der gelöschten Daten, indem Sie die [Purge-Richtlinien](#purge-guidelines) befolgen
* Passen Sie die [Caching-Richtlinie](../management/cachepolicy.md) an, da die Bereinigung bei kalten Daten länger dauert.
* Skalieren des Clusters

* Erhöhen Sie die Clusterbereinigungskapazität nach sorgfältiger Überlegung, wie unter [Extents-Wiederherstellungskapazität](../management/capacitypolicy.md#extents-purge-rebuild-capacity)beschrieben. Das Ändern dieses Parameters erfordert das Öffnen eines [Supporttickets](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="trigger-the-purge-process"></a>Auslösen des Bereinigungsprozesses

> [!Note]
> Die Löschausführung wird durch Ausführen des Befehls [ *"Purge TableName* records"](#purge-table-tablename-records-command) auf dem Endpunkt Datenverwaltung (**https://ingest-[YourClusterName].kusto.windows.net**) aufgerufen.

### <a name="purge-table-tablename-records-command"></a>Befehl *"TabelleName-Datensätze"* löschen

Der Befehl "Bereinigen" kann für unterschiedliche Verwendungsszenarien auf zwei Arten aufgerufen werden:
1. Programmgesteuerter Aufruf: Ein einstufiger Aufruf, der von Anwendungen aufgerufen werden soll. Der Aufruf dieses Befehls löst direkt die Löschausführungssequenz aus.

    **Syntax**

     ```kusto
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > Generieren Sie diesen Befehl mithilfe der CslCommandGenerator-API, die als Teil des NuGet-Pakets [kusto Client Library](../api/netfx/about-kusto-data.md) verfügbar ist.

1. Menschliche Anrufung: Ein zweistufiger Prozess, der eine explizite Bestätigung als separater Schritt erfordert. Der erste Aufruf des Befehls gibt ein Überprüfungstoken zurück, das bereitgestellt werden sollte, um die eigentliche Bereinigung auszuführen. Diese Sequenz reduziert das Risiko, dass versehentlich falsche Daten gelöscht werden. Die Verwendung dieser Option kann für große Tabellen mit erheblichen Kalt-Cache-Daten sehr lange dauern.
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **Syntax**

     ```kusto
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    |Parameter  |BESCHREIBUNG  |
    |---------|---------|
    | DatabaseName   |   Name der Datenbank      |
    | TableName     |     Name der Tabelle    |
    | Predicate    |    Identifiziert die zu löschenden Datensätze. Siehe Einschränkungen des Prädikats bereinigen weiter unten. | 
    | noregrets    |     Wenn diese Einstellung, wird eine einstufige Aktivierung ausgelöst.    |
    | verificationtoken     |  Im zweistufigen**Aktivierungsszenario (noregrets** ist nicht festgelegt) kann dieses Token verwendet werden, um den zweiten Schritt auszuführen und die Aktion zu übertragen. Wenn **verificationtoken** nicht angegeben ist, löst es den ersten Schritt des Befehls aus, in dem Informationen über die Bereinigung zurückgegeben werden und ein Token, das an den Befehl zurückgesendet werden soll, um Schritt #2 auszuführen.   |

    **Einschränkungen des Prädikats bereinigen**
    * Das Prädikat muss eine einfache Auswahl sein (z. B. *wobei [ColumnName] == 'X'* / *wobei [ColumnName] in ('X', 'Y', 'Z') und [OtherColumn] == 'A'*) sind.
    * Mehrere Filter müssen mit einem "und" `where` kombiniert werden, anstatt `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` separate `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'`Klauseln (z. B. und nicht ).
    * Das Prädikat kann nicht auf andere Tabellen als die gelöschte Tabelle verweisen (*TableName*). Das Prädikat kann nur die`where`Auswahlanweisung ( ) enthalten. Es kann keine spezifischen Spalten aus der Tabelle projizieren (Ausgabeschema beim Ausführen von '* `table` | Prädikat*' muss dem Tabellenschema entsprechen).
    * Systemfunktionen (z. `ingestion_time()` `extent_id()`B. , ) werden als Teil des Prädikats nicht unterstützt.

#### <a name="example-two-step-purge"></a>Beispiel: Zweistufige Bereinigung

1. Um die Bereinigung in einem zweistufigen Aktivierungsszenario zu initiieren, führen Sie Schritt #1 des Befehls aus:

    ```kusto
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **Ausgabe**

    |NumRecordsToPurge |EstimatedPurgeExecutionTime| VerificationToken
    |--|--|--
    |1,596 |00:00:02 |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    Überprüfen Sie numRecordsToPurge vor der Ausführung von Schritt #2. 
2. Um eine Bereinigung in einem zweistufigen Aktivierungsszenario abzuschließen, verwenden Sie das von Schritt #1 zurückgegebene Überprüfungstoken, um Schritt #2 auszuführen:

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **Ausgabe**

    |OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime |EngineDuration |Wiederholungsversuche |ClientRequestId |Prinzipal
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Geplant | | | |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD-App-ID=...

#### <a name="example-single-step-purge"></a>Beispiel: Einstufige Bereinigung

Führen Sie den folgenden Befehl aus, um eine Bereinigung in einem einstufigen Aktivierungsszenario auszulösen:

```kusto
.purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
```

**Ausgabe**

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime |EngineDuration |Wiederholungsversuche |ClientRequestId |Prinzipal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Geplant | | | |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD-App-ID=...

### <a name="cancel-purge-operation-command"></a>Befehl zum Abbrechen des Bereinigungsvorgangs abbrechen

Bei Bedarf können Sie ausstehende Löschanforderungen abbrechen.

> [!NOTE]
> Dieser Vorgang ist für Fehlerwiederherstellungsszenarien vorgesehen. Es ist nicht garantiert, erfolgreich zu sein, und sollte nicht Teil eines normalen Betriebsablaufs sein. Sie kann nur auf Anforderungen in der Warteschlange angewendet werden (die zur Ausführung noch nicht an den Modulknoten gesendet werden). Der Befehl wird auf dem Datenverwaltungsendpunkt ausgeführt.

**Syntax**

```kusto
 .cancel purge <OperationId>
 ```

**Beispiel**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**Ausgabe**

Die Ausgabe dieses Befehls ist die gleiche wie die Befehlsausgabe "Show purges *OperationId",* die den aktualisierten Status des abgebrochenen Bereinigungsvorgangs anzeigt. Wenn der Versuch erfolgreich ist, wird der Vorgangsstatus auf 'Abandoned' aktualisiert, andernfalls wird der Vorgangsstatus nicht geändert. 

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime |EngineDuration |Wiederholungsversuche |ClientRequestId |Prinzipal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD-App-ID=...

## <a name="track-purge-operation-status"></a>Status des Spurbereinigungsvorgangs 

> [!Note]
> Löschvorgänge können mit dem Befehl ["Vorlupungen anzeigen"](#show-purges-command) nachverfolgt werden, der für den Endpunkt Datenverwaltung (**https://ingest-[YourClusterName].kusto.windows.net**) ausgeführt wird.

Status = 'Abgeschlossen' gibt den erfolgreichen Abschluss der ersten Phase des Bereinigungsvorgangs an, d. h. Datensätze werden weich gelöscht und sind nicht mehr für Abfragen verfügbar. Von Kunden wird **nicht** erwartet, dass sie die zweite Phase (Hard-Delete) nachverfolgen und überprüfen. Diese Phase wird intern von Kusto überwacht.

### <a name="show-purges-command"></a>Show-Säuberungen-Befehl

`Show purges`Befehl zeigt den Status des Bereinigungsvorgangs an, indem die Vorgangs-ID innerhalb des angeforderten Zeitraums angegeben wird. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|Eigenschaften  |BESCHREIBUNG  |Obligatorisch/Optional
|---------|---------|
|OperationId    |      Die Datenverwaltungs-ID wird nach der Ausführung einer einzelnen oder zweiten Phase ausgegeben.   |Obligatorisch.
|StartDate    |   Geringeres Zeitlimit für Filtervorgänge. Wenn nicht angegeben, wird standardmäßig 24 Stunden vor der aktuellen Zeit angezeigt.      |Optional
|EndDate    |  Obere Zeitbegrenzung für Filtervorgänge. Wenn nicht angegeben, wird standardmäßig die aktuelle Zeit angezeigt.       |Optional
|DatabaseName    |     Datenbankname zum Filtern von Ergebnissen.    |Optional

> [!NOTE]
> Der Status wird nur für Datenbanken bereitgestellt, für die der Client über [Datenbankadministratorberechtigungen](../management/access-control/role-based-authorization.md) verfügt.

**Beispiele**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**Ausgabe** 

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime |EngineDuration |Wiederholungsversuche |ClientRequestId |Prinzipal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Abgeschlossen |Erfolgreich abgeschlossene Bereinigung (Speicherartefakte bis zum Löschen) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD-App-ID=...

* **OperationId** - die DM-Vorgangs-ID, die beim Ausführen der Bereinigung zurückgegeben wird. 
* **DatabaseName** - Datenbankname (Groß-/Kleinschreibung). 
* **TableName** - Tabellenname (Groß-/Kleinschreibung). 
* **ScheduledTime** - Zeitpunkt der Ausführung des Löschbefehls für den DM-Dienst. 
* **Dauer** - Gesamtdauer des Bereinigungsvorgangs, einschließlich der Wartezeit in der Ausführungs-DM-Warteschlange. 
* **EngineOperationId** - die Betriebs-ID der tatsächlichen Bereinigung, die im Modul ausgeführt wird. 
* **Zustand** - Bereinigungszustand, kann einer der folgenden sein: 
    * Geplant - Der Löschvorgang ist für die Ausführung geplant. Wenn der Auftrag **geplant**bleibt, gibt es wahrscheinlich einen Rückstand bei den Bereinigungsvorgängen. Siehe [Löschleistung,](#purge-performance) um diesen Rückstand zu beseitigen. Wenn ein Bereinigungsvorgang bei einem vorübergehenden Fehler fehlschlägt, wird er von der DM wiederholt und erneut auf **Geplant** festgelegt (so dass möglicherweise ein Vorgangsübergang von **Geplant** zu **InProgress** und zurück zu **Geplant**angezeigt wird).
    * InProgress - die Bereinigungsoperaration ist im Motor in Arbeit. 
    * Abgeschlossen - Bereinigung erfolgreich abgeschlossen.
    * BadInput - Die Bereinigung ist bei fehlerhafter Eingabe fehlgeschlagen und wird nicht wiederholt. Dies kann auf verschiedene Probleme zurückzuführen sein, z. B. auf einen Syntaxfehler im Prädikat, ein ungültiges Prädikat für Löschbefehle, eine Abfrage, die Grenzwerte überschreitet (z. B. über 1M-Entitäten in einem externen Datenoperator oder über 64 MB der gesamten erweiterten Abfragegröße) und 404 oder 403 Fehler für externe Daten-Blobs.
    * Fehler - Die Bereinigung ist fehlgeschlagen und wird nicht wiederholt. Dies kann passieren, wenn der Vorgang zu lange in der Warteschlange gewartet hat (über 14 Tage), aufgrund eines Rückstands bei anderen Bereinigungsvorgängen oder einer Reihe von Fehlern, die das Wiederholungslimit überschreiten. Letzteres wird eine interne Überwachungswarnung auslösen und vom Azure Data Explorer-Team untersucht. 
* StateDetails - eine Beschreibung des Staates.
* EngineStartTime - die Zeit, zu der der Befehl an das Modul ausgegeben wurde. Wenn es einen großen Unterschied zwischen dieser Zeit und ScheduledTime gibt, gibt es in der Regel einen großen Rückstand an Löschvorgängen und der Cluster hält nicht mit dem Tempo Schritt. 
* EngineDuration - Zeitpunkt der tatsächlichen Bereinigungsausführung im Motor. Wenn die Bereinigung mehrmals wiederholt wurde, ist dies die Summe aller Ausführungsdauern. 
* Wiederholungen - Anzahl der Wiederholungen, mit denen der Vorgang vom DM-Dienst aufgrund eines vorübergehenden Fehlers wiederholt wurde.
* ClientRequestId - Clientaktivitäts-ID der DM-Bereinigungsanforderung. 
* Principal - Identität des Emittenten des Bereinigungsbefehls.

## <a name="purging-an-entire-table"></a>Bereinigen eines gesamten Tisches
Zum Löschen einer Tabelle gehört das Löschen der Tabelle und das Markieren als gelöscht, sodass der im [Vorgang "Löschen"](#purge-process) beschriebene Hartlöschprozess auf ihr ausgeführt wird. Wenn Sie eine Tabelle löschen, ohne sie zu löschen, werden nicht alle Speicherartefakte gelöscht (gemäß der ursprünglich für die Tabelle festgelegten Richtlinie für die harte Aufbewahrung gelöscht). Der `purge table allrecords` Befehl ist schnell und effizient und ist dem Prozess der Bereinigung von Datensätzen, falls für Ihr Szenario zutreffend, sehr viel vorzuziehen. 

> [!Note]
> Der Befehl wird aufgerufen, indem der Befehl ["Purge table *TableName* allrecords"](#purge-table-tablename-allrecords-command) auf dem Endpunkt Datenverwaltung (**https://ingest-[YourClusterName].kusto.windows.net**) ausgeführt wird.

### <a name="purge-table-tablename-allrecords-command"></a>Löschtabelle *TableName* allrecords -Befehl

Ähnlich wie der Befehl['.purge table records ](#purge-table-tablename-records-command)' kann dieser Befehl in einem programmatischen (einstufigen) oder im manuellen (zweistufigen) Modus aufgerufen werden.
1. Programmgesteuerter Aufruf (einstufiger Schritt):

     **Syntax**

     ```kusto
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. Menschliche Anrufung (zwei Schritte):

     **Syntax**

     ```kusto
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |Parameter  |BESCHREIBUNG  |
    |---------|---------|
    |**DatabaseName**   |   Der Name der Datenbank.      |
    |**TableName**     |     Der Name der Tabelle.    |
    |**noregrets**    |     Wenn diese Einstellung, wird eine einstufige Aktivierung ausgelöst.    |
    |**verificationtoken**     |  Im zweistufigen**Aktivierungsszenario (noregrets** ist nicht festgelegt) kann dieses Token verwendet werden, um den zweiten Schritt auszuführen und die Aktion zu übertragen. Wenn **verificationtoken** nicht angegeben ist, löst der erste Schritt des Befehls, in dem ein Token zurückgegeben wird, den Befehl zurück an den Befehl zurückundwerden und den Schritt des Befehls #2 ausführen.|

#### <a name="example-two-step-purge"></a>Beispiel: Zweistufige Bereinigung

1. Um die Bereinigung in einem zweistufigen Aktivierungsszenario zu initiieren, führen Sie Schritt #1 des Befehls aus: 

    ```kusto
    .purge table MyTable in database MyDatabase allrecords
    ```

    **Ausgabe**

    | VerificationToken
    |--
    |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

1.  Um eine Bereinigung in einem zweistufigen Aktivierungsszenario abzuschließen, verwenden Sie das von Schritt #1 zurückgegebene Überprüfungstoken, um Schritt #2 auszuführen:

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    Die Ausgabe ist die gleiche wie die Befehlsausgabe '.show tables' (zurückgegeben ohne die gelöschte Tabelle).

    **Ausgabe**

    |TableName|DatabaseName|Ordner|DocString
    |---|---|---|---
    |SonstigeTabelle|Mydatabase|---|---


#### <a name="example-single-step-purge"></a>Beispiel: Einstufige Bereinigung

Führen Sie den folgenden Befehl aus, um eine Bereinigung in einem einstufigen Aktivierungsszenario auszulösen:

```kusto
.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

Die Ausgabe ist die gleiche wie die Befehlsausgabe '.show tables' (zurückgegeben ohne die gelöschte Tabelle).

**Ausgabe**

|TableName|DatabaseName|Ordner|DocString
|---|---|---|---
|SonstigeTabelle|Mydatabase|---|---


