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
ms.openlocfilehash: 807ca8a9bfd8d3f356fd849b6adc2102201513cf
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057161"
---
# <a name="create-materialized-view"></a>. CREATE MATERIALIZED-VIEW

Eine [materialisierte Sicht](materialized-view-overview.md) ist eine Aggregations Abfrage über eine Quell Tabelle, die eine einzelne Zusammenfassungs Anweisung darstellt.
Allgemeine Informationen und Richtlinien zum Erstellen einer materialisierten Sicht finden Sie unter [Erstellen einer materialisierten Sicht](materialized-view-overview.md#create-a-materialized-view).

Erfordert [Datenbankadministrator](../access-control/role-based-authorization.md) Berechtigungen.

Die materialisierte Sicht basiert immer auf einem einzelnen `fact table` und kann auch auf ein oder mehrere verweisen [`dimension tables`](../../concepts/fact-and-dimension-tables.md) . Informationen zu Einschränkungen beim beitreten zu Dimensions Tabellen in materialisierten Sichten finden Sie unter [Eigenschaften](#properties). Allgemeine Einschränkungen finden Sie unter [Einschränkungen bei der Erstellung materialisierter Sichten](materialized-view-overview.md#limitations-on-creating-materialized-views). 

* Verfolgen Sie den Erstellungs Prozess mit dem Befehl [. Show Operations](../operations.md#show-operations) .
* Brechen Sie den Erstellungs Vorgang mit dem Befehl [. Cancel Operation](#cancel-materialized-view-creation) ab.

## <a name="syntax"></a>Syntax

`.create` [`async`] `materialized-view` <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <br>
*ViewName* `on table` *SourceTableName* <br>
`{`<br>&nbsp;&nbsp;&nbsp;&nbsp;*Such*<br>`}`

## <a name="arguments"></a>Argumente

|Argument|type|BESCHREIBUNG
|----------------|-------|---|
|Ansichtsname|Zeichenfolge|Der materialisierte Sichtname. Der Sichtname kann nicht mit Tabellen-oder Funktionsnamen in derselben Datenbank in Konflikt stehen und muss den [bezeichnerbenennungs Regeln](../../query/schema-entities/entity-names.md#identifier-naming-rules)entsprechen. |
|SourceTableName|Zeichenfolge|Der Name der Quell Tabelle, in der die Sicht definiert ist.|
|Abfrage|Zeichenfolge|Die materialisierte Ansichts Abfrage. Weitere Informationen finden Sie unter [Query](#query-argument).|

### <a name="query-argument"></a>Abfrage Argument

Die im materialisierte View-Argument verwendete Abfrage ist durch die folgenden Regeln beschränkt:

* Das Abfrage Argument sollte auf eine einzelne Fakten Tabelle verweisen, die die Quelle der materialisierten Sicht ist, einen einzelnen Zusammenfassungs Operator einschließen und eine oder mehrere Aggregations Funktionen, die von einer oder mehreren Gruppen durch Ausdrücke aggregiert werden. Der Zusammenfassungs Operator muss immer der letzte Operator in der Abfrage sein.

* Die Abfrage sollte keine Operatoren enthalten, die von oder abhängig sind `now()` `ingestion_time()` . Die Abfrage sollte z. b. nicht enthalten `where Timestamp > ago(5d)` . Eine materialisierte Sicht mit einer `arg_max` / `arg_min` / `any` Aggregation kann keine der anderen unterstützten Aggregations Funktionen enthalten. Schränken Sie den von der Sicht abgedeckten Zeitraum mithilfe der Beibehaltungs Richtlinie für die materialisierte Sicht ein.

* Eine Sicht ist entweder eine `arg_max` / `arg_min` / `any` Ansicht (diese Funktionen können in derselben Ansicht verwendet werden) oder eine der anderen unterstützten Funktionen, aber nicht beide in derselben materialisierten Sicht. 
    `SourceTable | summarize arg_max(Timestamp, *), count() by Id` wird beispielsweise nicht unterstützt. 

* Zusammengesetzte Aggregationen werden in der materialisierten Sicht Definition nicht unterstützt. Definieren Sie beispielsweise anstelle der folgenden Sicht `SourceTable | summarize Result=sum(Column1)/sum(Column2) by Id` die materialisierte Sicht wie folgt: `SourceTable | summarize a=sum(Column1), b=sum(Column2) by Id` . Führen Sie zum Anzeigen der Abfragezeit-aus `ViewName | project Id, Result=a/b` . Die erforderliche Ausgabe der Sicht, einschließlich der berechneten Spalte ( `a/b` ), kann in einer [gespeicherten Funktion](../../query/functions/user-defined-functions.md)gekapselt werden. Greifen Sie auf die gespeicherte Funktion zu, anstatt direkt auf die materialisierte Ansicht zuzugreifen.

* Cluster übergreifende/datenbankübergreifende Abfragen werden nicht unterstützt.

* Verweise auf [external_table ()](../../query/externaltablefunction.md) und [externaldata](../../query/externaldata-operator.md) werden nicht unterstützt.

## <a name="properties"></a>Eigenschaften

Die folgenden Elemente werden in der-Klausel unterstützt `with(propertyName=propertyValue)` . Alle Eigenschaften sind optional.

|Eigenschaft|type|BESCHREIBUNG | Hinweise |
|----------------|-------|---|---|
|Abgleich|bool|Gibt an, ob die Ansicht auf der Grundlage aller Datensätze erstellt werden soll, die sich derzeit in *SourceTable* ( `true` ) befinden, oder ob Sie "From-Now-on" () erstellt wird `false` Der Standardwert ist `false`.| Der Befehl muss lauten `async` , und die Ansicht ist erst dann für Abfragen verfügbar, wenn die Erstellung abgeschlossen ist. Abhängig von der Menge der aufzufüllenden Daten kann die Erstellung mit Abgleich einige Zeit in Anspruch nehmen. Es ist absichtlich "langsam", um sicherzustellen, dass nicht zu viele Ressourcen des Clusters verbraucht werden. Siehe [Rück Füll Erklärung](materialized-view-overview.md#create-a-materialized-view) |
|effectivedatetime|datetime| Wenn die Angabe zusammen mit festgelegt `backfill=true` ist, wird die Erstellung nur mit Datensätzen Rück gefüllt, die nach DateTime erfasst wurden. "Backfill" muss ebenfalls auf "true" festgelegt werden. Erwartet ein DateTime-Literalzeichen, z. b. `effectiveDateTime=datetime(2019-05-01)`|
|dimensiontables|Array|Eine durch Trennzeichen getrennte Liste von Dimensions Tabellen in der Sicht.|  Dimensions Tabellen müssen explizit in den Ansichts Eigenschaften aufgerufen werden. Joins/Lookups mit Dimensions Tabellen sollten [bewährte Methoden](../../query/best-practices.md)für die Abfrage verwenden. Weitere Informationen finden Sie [unter Join with Dimension Table](#join-with-dimension-table).
|autoupdateschema|bool|Gibt an, ob die Sicht in den Quell Tabellen Änderungen automatisch aktualisiert werden soll. Der Standardwert ist `false`.| Die `autoUpdateSchema` Option ist nur für Sichten vom Typ gültig `arg_max(Timestamp, *)`  /  `arg_min(Timestamp, *)`  /  `any(*)` (nur, wenn das Columns-Argument ist `*` ). Wenn diese Option auf true festgelegt ist, werden Änderungen an der Quell Tabelle automatisch in der materialisierten Sicht reflektiert. Wenn diese Option verwendet wird, werden nicht alle Änderungen an der Quell Tabelle unterstützt. Weitere Informationen finden Sie unter [. ALTER MATERIALIZED-VIEW](materialized-view-alter.md). |
|folder|Zeichenfolge|Der Ordner der materialisierten Ansicht.|
|docString|Zeichenfolge|Eine Zeichenfolge, die die materialisierte Sicht dokumentiert.|

> [!WARNING]
> `autoUpdateSchema`Die Verwendung von kann zu nicht rückgängig zu Datenverlusten führen, wenn Spalten in der Quell Tabelle gelöscht werden. Die Sicht wird deaktiviert, wenn Sie nicht auf festgelegt ist `autoUpdateSchema` , und es wird eine Änderung an der Quell Tabelle vorgenommen, die zu einer Schema Änderung in der materialisierten Sicht führt. Wenn das Problem behoben ist, aktivieren Sie die materialisierte Sicht mit dem Befehl " [materialisierte Sicht aktivieren](materialized-view-enable-disable.md) " erneut. 
>
>Dieser Prozess wird häufig verwendet, wenn ein verwendet `arg_max(Timestamp, *)` und der Quell Tabelle Spalten hinzugefügt werden. Vermeiden Sie den Fehler, indem Sie die Ansichts Abfrage als `arg_max(Timestamp, Column1, Column2, ...)` oder mithilfe der `autoUpdateSchema` Option definieren. 

### <a name="join-with-dimension-table"></a>Verknüpfen mit Dimensions Tabelle

Datensätze in der Quell Tabelle (Fakten Tabelle) der Sicht werden nur einmal materialisiert. Eine andere Erfassungs Latenz zwischen der Fakten Tabelle und der Dimensions Tabelle kann sich auf die Sicht Ergebnisse auswirken.

**Beispiel**: eine Sicht Definition enthält eine innere Verknüpfung mit einer Dimensions Tabelle. Zum Zeitpunkt der Materialisierung wurde der Dimensions Daten Satz nicht vollständig erfasst, wurde aber bereits in der Fakten Tabelle erfasst. Dieser Datensatz wird aus der Sicht gelöscht und nie neu verarbeitet. 

Angenommen, der Join ist ein äußerer Join. Der Datensatz aus der Fakten Tabelle wird verarbeitet und der Ansicht mit einem NULL-Wert für die Spalten der Dimensions Tabelle hinzugefügt. Datensätze, die bereits (mit NULL-Werten) der Sicht hinzugefügt wurden, werden nicht erneut verarbeitet. Ihre Werte in Spalten aus der Dimensions Tabelle bleiben NULL.


## <a name="examples"></a>Beispiele

1. Erstellen Sie eine leere Ansicht, die nur Datensätze materialisiert, die von nun an erfasst wurden: 

    <!-- csl -->
    ```
    .create materialized-view ArgMax on table T
    {
        T | summarize arg_max(Timestamp, *) by User
    }
    ```
    
1. Erstellen Sie eine materialisierte Sicht mit der Option Abgleich, indem Sie Folgendes verwenden `async` :

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
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
    {
        T
        | lookup DimUsers on User  
        | summarize arg_max(Timestamp, *) by User 
    }
    
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
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

    **Do**Ausführen:
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by SubscriptionId, ResouceId 
    }
    ``` 
    
    Nicht **tun**:
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by ResouceId 
    }
    ```

* Schließen Sie keine Transformationen, Normalisierungen und anderen umfangreichen Berechnungen ein, die als Teil der materialisierten Sicht Definition in eine [Aktualisierungs Richtlinie](../updatepolicy.md) verschoben werden können. Führen Sie stattdessen alle diese Prozesse in einer Update Richtlinie aus, und führen Sie die Aggregation nur in der materialisierten Sicht aus. Verwenden Sie diesen Prozess für die Suche in Dimensions Tabellen, falls zutreffend.

    **Do**Ausführen:
    
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
    &nbsp;     Target 
    &nbsp;     | summarize count() by ResourceId 
    }
    ```
    
    Nicht **tun**:
    
    ```kusto
    .create materialized-view Usage on table SourceTable
    {
    &nbsp;     SourceTable 
    &nbsp;     | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)
    &nbsp;     | summarize count() by ResourceId
    }
    ```

> [!NOTE]
> Wenn Sie die beste Abfragezeit Leistung benötigen, aber einige Daten der Aktualität der Daten Opfern können, verwenden Sie die [Funktion materialized_view ()](../../query/materialized-view-function.md).

## <a name="cancel-materialized-view-creation"></a>Materialisierte Ansichts Erstellung Abbrechen

Brechen Sie den Prozess der materialisierten Ansichts Erstellung ab, wenn Sie die `backfill` Option verwenden. Diese Aktion ist nützlich, wenn die Erstellung zu lange dauert und Sie Sie während der Ausführung abbrechen möchten.  

> [!WARNING]
> Die materialisierte Sicht kann nicht wieder hergestellt werden, nachdem dieser Befehl ausgeführt wurde.

Der Erstellungs Prozess kann nicht sofort abgebrochen werden. Der Cancel-Befehl signalisiert, dass die Materialisierung angehalten wird, und die Erstellung prüft regelmäßig, ob ein Abbruch angefordert wurde. Der Cancel-Befehl wartet auf einen maximalen Zeitraum von 10 Minuten, bis der Erstellungs Vorgang für materialisierte Sichten abgebrochen wurde, und meldet zurück, wenn der Abbruch erfolgreich war. Auch wenn der Abbruch innerhalb von 10 Minuten nicht erfolgreich war und der Abbruch Befehl einen Fehler meldet, wird die materialisierte Sicht wahrscheinlich später im Erstellungs Prozess abgebrochen. Der Befehl [. Show Operations](../operations.md#show-operations) gibt an, ob der Vorgang abgebrochen wurde. Der `cancel operation` Befehl wird nur für die Erzwingung von materialisierten Sichten und nicht für das Abbrechen anderer Vorgänge unterstützt.

### <a name="syntax"></a>Syntax

`.cancel` `operation` *operationId*

### <a name="properties"></a>Eigenschaften

|Eigenschaft|type|BESCHREIBUNG
|----------------|-------|---|
|operationId|Guid|Die Vorgangs-ID, die vom CREATE MATERIALIZED-VIEW-Befehl zurückgegeben wird.|

### <a name="output"></a>Output

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|---
|OperationId|Guid|Die Vorgangs-ID des Befehls Create materialisierte View.
|Vorgang|Zeichenfolge|Art des Vorgangs.
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
