---
title: 'Erstellen einer materialisierten Ansicht: Azure-Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie materialisierte Sichten in Azure Daten-Explorer erstellt werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 383d1ab5d948a5fbcfb3ab2aad0ff8e5ed675075
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349442"
---
# <a name="create-materialized-view"></a>.create materialized-view

Eine [materialisierte Sicht](materialized-view-overview.md) ist eine Aggregations Abfrage über eine Quell Tabelle, die eine einzelne Zusammenfassungs Anweisung darstellt.

Es gibt zwei Möglichkeiten, eine materialisierte Sicht zu erstellen, die von der Option *Abgleich* im Befehl notiert wird:

 * **Basierend auf den vorhandenen Datensätzen in der Quell Tabelle erstellen:** 
      * Die Erstellung kann in Abhängigkeit von der Anzahl der Datensätze in der Quell Tabelle einige Zeit in Anspruch nehmen. Die Ansicht ist bis zum Abschluss nicht für Abfragen verfügbar.
      * Wenn Sie diese Option verwenden, muss der CREATE-Befehl sein, `async` und die Ausführung kann mit dem Befehl [. Show Operations](../operations.md#show-operations) überwacht werden.

    * Das Abbrechen des Abgleich-Prozesses ist mit dem Befehl " [. Cancel Operation](#cancel-materialized-view-creation) " möglich.

      > [!IMPORTANT]
      > * Die Verwendung der Option "Abgleich" wird für Daten im kaltcache nicht unterstützt. Erhöhen Sie ggf. den Zeitraum für den aktiven Cache für die Erstellung der Sicht. Hierfür ist möglicherweise eine horizontale Skalierung erforderlich.    
      > * Die Verwendung der Abgleich-Option kann für große Quell Tabellen eine lange Zeit in Anspruch nehmen. Wenn dieser Prozess während der Ausführung vorübergehend fehlschlägt, wird er nicht automatisch wiederholt, und eine erneute Ausführung des create-Befehls ist erforderlich.
    
* **Erstellen Sie ab jetzt die materialisierte Ansicht:** 
    * Die materialisierte Sicht wird leer erstellt und enthält nur Datensätze, die nach der Erstellung der Sicht erfasst werden. Die Erstellung dieser Art wird sofort zurückgegeben, erfordert nicht `async` , und die Ansicht ist sofort für Abfragen verfügbar.

Für den Create-Vorgang sind [Datenbankadministrator](../access-control/role-based-authorization.md) Berechtigungen erforderlich. Der Ersteller der materialisierten Sicht wird zum Administrator des IT-Administrators.

## <a name="syntax"></a>Syntax

`.create` [`async`] `materialized-view` <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <br>
*ViewName* `on table` *SourceTableName* <br>
`{`<br>&nbsp;&nbsp;&nbsp;&nbsp;*Such*<br>`}`

## <a name="arguments"></a>Argumente

|Argument|type|Beschreibung
|----------------|-------|---|
|Ansichtsname|String|Der materialisierte Sichtname. Der Sichtname kann nicht mit Tabellen-oder Funktionsnamen in derselben Datenbank in Konflikt stehen und muss den [bezeichnerbenennungs Regeln](../../query/schema-entities/entity-names.md#identifier-naming-rules)entsprechen. |
|SourceTableName|String|Der Name der Quell Tabelle, in der die Sicht definiert ist.|
|Abfrage|String|Die materialisierte Ansichts Abfrage. Weitere Informationen finden Sie unter [Query](#query-argument).|

### <a name="query-argument"></a>Abfrage Argument

Die im materialisierte View-Argument verwendete Abfrage ist durch die folgenden Regeln beschränkt:

* Das Abfrage Argument sollte auf eine einzelne Fakten Tabelle verweisen, die die Quelle der materialisierten Sicht ist, einen einzelnen Zusammenfassungs Operator einschließen und eine oder mehrere Aggregations Funktionen, die von einer oder mehreren Gruppen durch Ausdrücke aggregiert werden. Der Zusammenfassungs Operator muss immer der letzte Operator in der Abfrage sein.

* Eine Sicht ist entweder eine `arg_max` / `arg_min` / `any` Ansicht (diese Funktionen können in derselben Ansicht verwendet werden) oder eine der anderen unterstützten Funktionen, aber nicht beide in derselben materialisierten Sicht. 
    `SourceTable | summarize arg_max(Timestamp, *), count() by Id` wird beispielsweise nicht unterstützt. 

* Die Abfrage sollte keine Operatoren enthalten, die von oder abhängig sind `now()` `ingestion_time()` . Die Abfrage sollte z. b. nicht enthalten `where Timestamp > ago(5d)` . Eine materialisierte Sicht mit einer `arg_max` / `arg_min` / `any` Aggregation kann keine der anderen unterstützten Aggregations Funktionen enthalten. Schränken Sie den von der Sicht abgedeckten Zeitraum mithilfe der Beibehaltungs Richtlinie für die materialisierte Sicht ein.

* Zusammengesetzte Aggregationen werden in der materialisierten Sicht Definition nicht unterstützt. Definieren Sie beispielsweise anstelle der folgenden Sicht `SourceTable | summarize Result=sum(Column1)/sum(Column2) by Id` die materialisierte Sicht wie folgt: `SourceTable | summarize a=sum(Column1), b=sum(Column2) by Id` . Führen Sie zum Anzeigen der Abfragezeit-aus `ViewName | project Id, Result=a/b` . Die erforderliche Ausgabe der Sicht, einschließlich der berechneten Spalte ( `a/b` ), kann in einer [gespeicherten Funktion](../../query/functions/user-defined-functions.md)gekapselt werden. Greifen Sie auf die gespeicherte Funktion zu, anstatt direkt auf die materialisierte Ansicht zuzugreifen.

* Cluster übergreifende/datenbankübergreifende Abfragen werden nicht unterstützt.

* Verweise auf [external_table ()](../../query/externaltablefunction.md) und [externaldata](../../query/externaldata-operator.md) werden nicht unterstützt.

* Zusätzlich zur Quell Tabelle der Sicht kann Sie auch auf eine oder mehrere verweisen [`dimension tables`](../../concepts/fact-and-dimension-tables.md) . Dimensions Tabellen müssen explizit in den Ansichts Eigenschaften aufgerufen werden. Es ist wichtig, das Verhalten beim Verbinden mit Dimensions Tabellen zu verstehen:

    * Datensätze in der Quell Tabelle der Sicht (die Fakten Tabelle) werden nur einmal materialisiert. Eine andere Erfassungs Latenz zwischen der Fakten Tabelle und der Dimensions Tabelle kann sich auf die Sicht Ergebnisse auswirken.

    * **Beispiel** : eine Sicht Definition enthält eine innere Verknüpfung mit einer Dimensions Tabelle. Zum Zeitpunkt der Materialisierung wurde der Dimensions Daten Satz nicht vollständig erfasst, wurde aber bereits in der Fakten Tabelle erfasst. Dieser Datensatz wird aus der Sicht gelöscht und nie neu verarbeitet. 

        Wenn der Join ein äußerer Join ist, wird der Datensatz aus der Fakten Tabelle ebenso verarbeitet und der Ansicht mit einem NULL-Wert für die Spalten der Dimensions Tabelle hinzugefügt. Datensätze, die bereits (mit NULL-Werten) der Sicht hinzugefügt wurden, werden nicht erneut verarbeitet. Ihre Werte in Spalten aus der Dimensions Tabelle bleiben NULL.

## <a name="properties"></a>Eigenschaften

Die folgenden Elemente werden in der-Klausel unterstützt `with(propertyName=propertyValue)` . Alle Eigenschaften sind optional.

|Eigenschaft|type|Beschreibung |
|----------------|-------|---|
|Abgleich|bool|Gibt an, ob die Ansicht auf der Grundlage aller Datensätze erstellt werden soll, die sich derzeit in *SourceTable* ( `true` ) befinden, oder ob Sie "From-Now-on" () erstellt wird `false` Der Standardwert ist `false`.| 
|effectivedatetime|datetime| Wenn die Angabe zusammen mit festgelegt `backfill=true` ist, wird die Erstellung nur mit Datensätzen Rück gefüllt, die nach DateTime erfasst wurden. "Backfill" muss ebenfalls auf "true" festgelegt werden. Erwartet ein DateTime-Literalzeichen, z. b. `effectiveDateTime=datetime(2019-05-01)`|
|dimensiontables|Array|Eine durch Trennzeichen getrennte Liste von Dimensions Tabellen in der Sicht. Siehe [Abfrage Argument](#query-argument)
|autoupdateschema|bool|Gibt an, ob die Sicht in den Quell Tabellen Änderungen automatisch aktualisiert werden soll. Der Standardwert ist `false`. Diese Option ist nur für Sichten vom Typ gültig `arg_max(Timestamp, *)`  /  `arg_min(Timestamp, *)`  /  `any(*)` (nur, wenn das Columns-Argument ist `*` ). Wenn diese Option auf true festgelegt ist, werden Änderungen an der Quell Tabelle automatisch in der materialisierten Sicht reflektiert.
|folder|Zeichenfolge|Der Ordner der materialisierten Ansicht.|
|docString|Zeichenfolge|Eine Zeichenfolge, die die materialisierte Sicht dokumentiert.|

> [!WARNING]
> * `autoUpdateSchema`Die Verwendung von kann zu nicht rückgängig zu Datenverlusten führen, wenn Spalten in der Quell Tabelle gelöscht werden. 
> * Wenn eine Änderung an der Quell Tabelle vorgenommen wird, was zu einer Schema Änderung der materialisierten Sicht führt, und `autoUpdateSchema` ist false, wird die Sicht automatisch deaktiviert. 
>    * Dieser Fehler kommt häufig vor, wenn ein verwendet `arg_max(Timestamp, *)` und der Quell Tabelle Spalten hinzugefügt werden. 
>    * Vermeiden Sie diesen Fehler, indem Sie die Ansichts Abfrage als `arg_max(Timestamp, Column1, Column2, ...)` oder mithilfe der `autoUpdateSchema` Option definieren.
> * Wenn die Ansicht aus diesen Gründen deaktiviert ist, können Sie Sie erneut aktivieren, nachdem Sie das Problem mit dem Befehl " [materialisierte Sicht aktivieren](materialized-view-enable-disable.md) " behoben haben.
>

## <a name="examples"></a>Beispiele

1. Erstellen Sie eine leere ARG_MAX Sicht, die nur Datensätze materialisiert, die von nun an erfasst wurden:

    <!-- csl -->
    ```
    .create materialized-view ArgMax on table T
    {
        T | summarize arg_max(Timestamp, *) by User
    }
    ```
    
1. Erstellen Sie eine materialisierte Sicht für tägliche Aggregate mit Abgleich-Option mit `async` :

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, docString="Customer telemetry") CustomerUsage on table T
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day 
    } 
    ```
    
1. Erstellen Sie eine materialisierte Sicht mit Abgleich und `effectiveDateTime` . Die Sicht wird nur auf der Grundlage von Datensätzen aus DateTime erstellt:

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, effectiveDateTime=datetime(2019-01-01)) CustomerUsage on table T 
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day
    } 
    ```
1. Eine materialisierte Sicht, die die Quell Tabelle basierend auf der EventId-Spalte dedupliziert:

    <!-- csl -->
    ```
    .create materialized-view DedupedT on table T
    {
        T
        | summarize any(*) by EventId
    }
    ```

1. Die Definition kann zusätzliche Operatoren vor der- `summarize` Anweisung enthalten, solange die-Anweisung `summarize` die letzte ist:

    <!-- csl -->
    ```
    .create materialized-view CustomerUsage on table T 
    {
        T 
        | where Customer in ("Customer1", "Customer2", "CustomerN")
        | parse Url with "https://contoso.com/" Api "/" *
        | extend Month = startofmonth(Timestamp)
        | summarize count(), dcount(User), max(Duration) by Customer, Api, Month
    }
    ```

1. Materialisierte Sichten, die mit einer Dimensions Tabelle verknüpft sind:

    <!-- csl -->
    ```
    .create materialized-view EnrichedArgMax on table T with (dimensionTables = ['DimUsers'])
    {
        T
        | lookup DimUsers on User  
        | summarize arg_max(Timestamp, *) by User 
    }
    
    .create materialized-view EnrichedArgMax on table T with (dimensionTables = ['DimUsers'])
    {
        DimUsers | project User, Age, Address
        | join kind=rightouter hint.strategy=broadcast T on User
        | summarize arg_max(Timestamp, *) by User 
    }
    ```
    

## <a name="supported-aggregation-functions"></a>Unterstützte Aggregations Funktionen

Die folgenden Aggregations Funktionen werden unterstützt:

* [`count`](../../query/count-aggfunction.md)
* [`countif`](../../query/countif-aggfunction.md)
* [`dcount`](../../query/dcount-aggfunction.md)
* [`dcountif`](../../query/dcountif-aggfunction.md)
* [`min`](../../query/min-aggfunction.md)
* [`max`](../../query/max-aggfunction.md)
* [`avg`](../../query/avg-aggfunction.md)
* [`avgif`](../../query/avgif-aggfunction.md)
* [`sum`](../../query/sum-aggfunction.md)
* [`arg_max`](../../query/arg-max-aggfunction.md)
* [`arg_min`](../../query/arg-min-aggfunction.md)
* [`any`](../../query/any-aggfunction.md)
* [`hll`](../../query/hll-aggfunction.md)
* [`make_set`](../../query/makeset-aggfunction.md)
* [`make_list`](../../query/makelist-aggfunction.md)
* [`percentile`, `percentiles`](../../query/percentiles-aggfunction.md)

## <a name="performance-tips"></a>Leistungstipps

* Materialisierte Sicht Abfrage Filter werden optimiert, wenn Sie nach einer der materialisierten Sicht Dimensionen (Aggregation by-Klausel) gefiltert werden. Wenn Sie wissen, dass das Abfrage Muster häufig nach einer Spalte filtert, bei der es sich um eine Dimension in der materialisierten Sicht handeln kann, fügen Sie Sie in die Sicht ein. Beispiel: für eine materialisierte Sicht, die eine `arg_max` durch verfügbar macht, die `ResourceId` häufig von gefiltert wird `SubscriptionId` , lautet die Empfehlung wie folgt:

    **Do** Ausführen:
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by SubscriptionId, ResouceId 
    }
    ``` 
    
    Nicht **tun** :
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by ResouceId 
    }
    ```

* Schließen Sie keine Transformationen, Normalisierungen und anderen umfangreichen Berechnungen ein, die als Teil der materialisierten Sicht Definition in eine [Aktualisierungs Richtlinie](../updatepolicy.md) verschoben werden können. Führen Sie stattdessen alle diese Prozesse in einer Update Richtlinie aus, und führen Sie die Aggregation nur in der materialisierten Sicht aus. Verwenden Sie diesen Prozess für die Suche in Dimensions Tabellen, falls zutreffend.

    **Do** Ausführen:
    
    * Update Richtlinie:
    
    ```kusto
    .alter-merge table Target policy update 
    @'[{"IsEnabled": true, 
        "Source": "SourceTable", 
        "Query": 
            "SourceTable 
            | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)", 
        "IsTransactional": false}]'  
    ```
        
    * Materialisierte Ansicht:
    
    ```kusto
    .create materialized-view Usage on table Events
    {
        Target 
        | summarize count() by ResourceId 
    }
    ```
    
    Nicht **tun** :
    
    ```kusto
    .create materialized-view Usage on table SourceTable
    {
        SourceTable 
        | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)
        | summarize count() by ResourceId
    }
    ```

> [!NOTE]
> Wenn Sie die beste Abfragezeit Leistung benötigen, aber einige Daten der Aktualität der Daten Opfern können, verwenden Sie die [Funktion materialized_view ()](../../query/materialized-view-function.md).

## <a name="limitations-on-creating-materialized-views"></a>Einschränkungen beim Erstellen materialisierter Sichten

* Eine materialisierte Sicht kann nicht erstellt werden:
    * Zusätzlich zu einer anderen materialisierten Sicht.
    * Auf [Follower-Datenbanken](../../../follower.md). Follower-Datenbanken sind schreibgeschützt, und materialisierte Sichten erfordern Schreibvorgänge.  Materialisierte Sichten, die auf Daten Bank Datenbanken definiert sind, können wie jede andere Tabelle in der Führungskraft von ihren Followern abgefragt werden. 
* Die Quell Tabelle einer materialisierten Sicht:
    * Muss eine Tabelle sein, die direkt erfasst wird, entweder mithilfe einer der Erfassungs [Methoden](../../../ingest-data-overview.md#ingestion-methods-and-tools), mithilfe einer [Update Richtlinie](../updatepolicy.md)oder [durch Erfassung von Abfrage Befehlen](../data-ingestion/ingest-from-query.md).
        * Insbesondere wird die Verwendung von [Move-Blöcken](../move-extents.md) aus anderen Tabellen in die Quell Tabelle der materialisierten Sicht nicht unterstützt. Das Verschieben von Blöcken kann mit folgendem Fehler fehlschlagen: `Cannot drop/move extents from/to table 'TableName' since Materialized View 'ViewName' is currently processing some of these extents` . 
    * Die [ingestiontime-Richtlinie](../ingestiontimepolicy.md) muss aktiviert sein (der Standardwert ist aktiviert).
    * Kann nicht für die streamingansung aktiviert werden.
    * Kann keine eingeschränkte Tabelle oder Tabelle mit aktivierter Sicherheit auf Zeilenebene sein.
* [Cursor Funktionen](../databasecursor.md#cursor-functions) können nicht über materialisierte Sichten verwendet werden.
* Der fortlaufende Export aus einer materialisierten Sicht wird nicht unterstützt.

## <a name="cancel-materialized-view-creation"></a>Materialisierte Ansichts Erstellung Abbrechen

Brechen Sie den Prozess der materialisierten Ansichts Erstellung ab, wenn Sie die `backfill` Option verwenden. Diese Aktion ist nützlich, wenn die Erstellung zu lange dauert und Sie Sie während der Ausführung abbrechen möchten.  

> [!WARNING]
> Die materialisierte Sicht kann nicht wieder hergestellt werden, nachdem dieser Befehl ausgeführt wurde.

Der Erstellungs Prozess kann nicht sofort abgebrochen werden. Der Cancel-Befehl signalisiert, dass die Materialisierung angehalten wird, und die Erstellung prüft regelmäßig, ob ein Abbruch angefordert wurde. Der Cancel-Befehl wartet auf einen maximalen Zeitraum von 10 Minuten, bis der Erstellungs Vorgang für materialisierte Sichten abgebrochen wurde, und meldet zurück, wenn der Abbruch erfolgreich war. Auch wenn der Abbruch innerhalb von 10 Minuten nicht erfolgreich war und der Abbruch Befehl einen Fehler meldet, wird die materialisierte Sicht wahrscheinlich später im Erstellungs Prozess abgebrochen. Der Befehl [. Show Operations](../operations.md#show-operations) gibt an, ob der Vorgang abgebrochen wurde. Der `cancel operation` Befehl wird nur für die Erzwingung von materialisierten Sichten und nicht für das Abbrechen anderer Vorgänge unterstützt.

### <a name="syntax"></a>Syntax

`.cancel` `operation` *operationId*

### <a name="properties"></a>Eigenschaften

|Eigenschaft|type|Beschreibung
|----------------|-------|---|
|operationId|Guid|Die Vorgangs-ID, die vom CREATE MATERIALIZED-VIEW-Befehl zurückgegeben wird.|

### <a name="output"></a>Output

|Ausgabeparameter |type |Beschreibung
|---|---|---
|OperationId|Guid|Die Vorgangs-ID des Befehls Create materialisierte View.
|Vorgang|String|Art des Vorgangs.
|StartedOn|datetime|Die Startzeit des Erstell Vorgangs.
|Cancellationstate|Zeichenfolge|Eines von: `Cancelled successfully` (Erstellung wurde abgebrochen), `Cancellation failed` (warten auf Abbruchzeit Überschreitung), `Unknown` (die Anzeige Erstellung wird nicht mehr ausgeführt, aber von diesem Vorgang nicht abgebrochen).
|ReasonPhrase|Zeichenfolge|Grund, warum der Abbruch nicht erfolgreich war.

### <a name="example"></a>Beispiel

<!-- csl -->
```
.cancel operation c4b29441-4873-4e36-8310-c631c35c916e
```

|OperationId|Vorgang|StartedOn|Cancellationstate|ReasonPhrase|
|---|---|---|---|---|
|c4b29441-4873-4e36-8310-c631c35c916e|Materializedviewkreateoralter|2020-05-08 19:45:03.9184142|Erfolgreich abgebrochen||

Wenn der Abbruch nicht innerhalb von 10 Minuten abgeschlossen wurde, weist auf einen `CancellationState` Fehler hin. Die Erstellung kann dann abgebrochen werden.
