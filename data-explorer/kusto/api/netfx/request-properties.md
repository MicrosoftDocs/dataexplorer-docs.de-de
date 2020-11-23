---
title: Anforderungs Eigenschaften und clientrequestproperties-Azure Daten-Explorer
description: In diesem Artikel werden Anforderungs Eigenschaften und clientrequestproperties in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 7dfb0da5d6a2e0d9349f68ea97fb371641e0d506
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324703"
---
# <a name="request-properties-and-clientrequestproperties"></a>Anforderungs Eigenschaften und clientrequestproperties

Wenn eine Anforderung von Kusto über das .NET SDK durchgeführt wird, geben Sie Folgendes an:

* Eine Verbindungs Zeichenfolge, die den Dienst Endpunkt angibt, mit dem eine Verbindung hergestellt werden soll, sowie Authentifizierungs Parameter und ähnliche verbindungsbezogene Informationen. Programm gesteuert wird die Verbindungs Zeichenfolge über die- `KustoConnectionStringBuilder` Klasse dargestellt.

* Der Name der Datenbank, in der der "Bereich" der Anforderung beschrieben wird.

* Der Text der Anforderung (Abfrage oder Befehl) selbst.

* Zusätzliche Eigenschaften, die der Client für den Dienst bereitstellt und auf die Anforderung angewendet wird. Diese Eigenschaften werden Programm gesteuert von einer Klasse mit dem Namen gespeichert `ClientRequestProperties` .

##   <a name="clientrequestproperties"></a>Clientrequestproperties

Die Eigenschaften der Client Anforderung haben viele Verwendungsmöglichkeiten. 
* Vereinfacht das Debuggen. Beispielsweise können die Eigenschaften Korrelations Zeichenfolgen bereitstellen, die zum Nachverfolgen von Client-/Dienstinteraktionen verwendet werden. 
* Wirkt sich darauf aus, welche Beschränkungen und Richtlinien auf die Anforderung angewendet werden. 
* mithilfe von [Abfrage Parametern](../../query/queryparametersstatement.md) können Client Anwendungen Kusto-Abfragen auf der Grundlage von Benutzereingaben parametrisieren.
[Liste der unterstützten Eigenschaften](#list-of-clientrequestproperties).

Die- `Kusto.Data.Common.ClientRequestProperties` Klasse enthält drei Arten von Daten.

* Benannte Eigenschaften.
* Optionen: eine Zuordnung eines Options namens zu einem Optionswert.
* Parameters: eine Zuordnung eines Abfrage Parameter namens zu einem Abfrage Parameterwert.

> [!NOTE]
> Einige benannte Eigenschaften sind als "nicht verwenden" gekennzeichnet. Solche Eigenschaften sollten nicht von Clients angegeben werden und haben keine Auswirkung auf den Dienst.

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>Die benannte Eigenschaft "clientrequestid (x-MS-Client-Request-ID)"

Diese benannte Eigenschaft verfügt über die vom Client angegebene Identität der Anforderung. Clients sollten bei jeder gesendeten Anforderung einen eindeutigen pro-Anforderungs-Wert angeben. Durch diesen Wert werden debuggingfehler vereinfacht, und in einigen Szenarien, z. b. bei Abfrage Abbruch, ist dies erforderlich.

Der programmgesteuerte Name der-Eigenschaft ist `ClientRequestId` , und Sie wird in den-HTTP-Header übersetzt `x-ms-client-request-id` .

Diese Eigenschaft wird vom SDK auf einen (zufälligen) Wert festgelegt, wenn der Client keinen Wert angibt.

Der Inhalt dieser Eigenschaft kann eine beliebige Druck Bare eindeutige Zeichenfolge sein, z. b. eine GUID.
Es wird jedoch empfohlen, dass von den Clients Folgendes verwendet wird: *ApplicationName* `.` *ActivityName* `;` *UniqueId*

* *ApplicationName* identifiziert die Client Anwendung, die die Anforderung durchführt.
* *ActivityName* identifiziert die Art der Aktivität, für die die Client Anwendung die Client Anforderung ausgibt.
* *UniqueId* identifiziert die jeweilige Anforderung.

## <a name="the-application-x-ms-app-named-property"></a>Die benannte Eigenschaft der Anwendung (x-ms-APP)

Die benannte Eigenschaft der Anwendung (x-ms-APP) enthält den Namen der Client Anwendung, von der die Anforderung gestellt wird, und wird für die Ablauf Verfolgung verwendet.

Der programmgesteuerte Name dieser Eigenschaft ist `Application` , und Sie wird in den-HTTP-Header übersetzt `x-ms-app` . Sie kann in der Kusto-Verbindungs Zeichenfolge als angegeben werden `Application Name for Tracing` .

Diese Eigenschaft wird auf den Namen des Prozesses festgelegt, der das SDK verwendet, wenn der Client keinen eigenen Wert angibt.

## <a name="the-user-x-ms-user-named-property"></a>Die benannte Eigenschaft des Benutzers (x-MS-User)

Die benannte Eigenschaft des Benutzers (x-MS-User) verfügt über die Identität des Benutzers, der die Anforderung ausführt, und wird für die Ablauf Verfolgung verwendet.

Der programmgesteuerte Name dieser Eigenschaft ist `User` , und Sie wird in den-HTTP-Header übersetzt `x-ms-user` . Sie kann in der Kusto-Verbindungs Zeichenfolge als angegeben werden `User Name for Tracing` .

## <a name="controlling-request-properties-using-the-rest-api"></a>Steuern von Anforderungs Eigenschaften mit der Rest-API

Wenn Sie eine HTTP-Anforderung an den Kusto-Dienst ausgeben, verwenden `properties` Sie den Slot im JSON-Dokument, das der Post-Anforderungs Text ist, um Anforderungs Eigenschaften bereitzustellen. 

> [!NOTE]
> Einige der Eigenschaften (z. b. "Clientanforderungs-ID", die die Korrelations-ID ist, die der Client für den Dienst zur Identifizierung der Anforderung bereitstellt) können im-HTTP-Header bereitgestellt werden und können auch festgelegt werden, wenn "http Get" verwendet wird.
Weitere Informationen finden Sie [im Kusto-Rest-API-Anforderungs Objekt](../rest/request.md).

## <a name="providing-values-for-query-parameterization-as-request-properties"></a>Angeben von Werten für die Abfrage Parametrisierung als Anforderungs Eigenschaften

Kusto-Abfragen können auf Abfrage Parameter verweisen, indem eine spezialisierte [Declare Query-Parameters-](../../query/queryparametersstatement.md) Anweisung im Abfragetext verwendet wird. Mit dieser Anweisung können Client Anwendungen Kusto-Abfragen auf sichere Weise auf der Grundlage von Benutzereingaben und ohne Angst vor Injection-Angriffen parametrisieren.

Programm gesteuert können Sie Eigenschaftswerte mithilfe der `ClearParameter` `SetParameter` Methoden, und festlegen `HasParameter` .

In der Rest-API werden Abfrage Parameter in derselben JSON-codierten Zeichenfolge wie die anderen Anforderungs Eigenschaften angezeigt.

## <a name="sample-client-code-for-using-request-properties"></a>Beispiel Client Code für die Verwendung von Anforderungs Eigenschaften

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
{
    var queryParameters = new Dictionary<String, String>()
    {
        { "xIntValue", "111" },
        { "xStrValue", "abc" },
        { "xDoubleValue", "11.1" }
    };

    // Query parameters (and many other properties) are provided
    // by a ClientRequestProperties object handed alongside
    // the query:
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="list-of-clientrequestproperties"></a>Liste der clientrequestproperties

<!-- The following text can be re-produced by running: Kusto.Cli.exe -focus "#crp -doc" -->

* `deferpartialqueryfailures` (*Optiondeferpartialqueryfailure*): Wenn der Wert true ist, werden die Fehler bei partiellen Abfragen von Teil Fehlern als Teil des Resultsets deaktiviert. Booleschen
* `materialized_view_shuffle` (*Optionmaterializedviewshufflequery*): ein Hinweis zur Verwendung der shuffle-Strategie für materialisierte Sichten, auf die in der Abfrage verwiesen wird.
Die-Eigenschaft ist ein Array von materialisierten Sichten und den zu verwendenden shuffle-Schlüsseln.
Beispiele: "Dynamic ([{" Name ":" V1 "," Keys ": [" K1 "," K2 "]}])" (Shuffle View v1 by K1, K2) oder "Dynamic ([{" Name ":" V1 "}])" (Shuffle View v1 by all Keys) [Dynamic]
* `max_memory_consumption_per_query_per_node` (*Optionmaxmemoryconsumptionperquerypernode*): überschreibt die maximale Standardgröße des Arbeitsspeichers, die eine gesamte Abfrage pro Knoten zuordnen kann. UInt64
* `maxmemoryconsumptionperiterator` (*Optionmaxmemoryconsumptionperiterator*): überschreibt die standardmäßige maximale Speichermenge, die ein Abfrage Operator zuordnen kann. UInt64
* `maxoutputcolumns` (*Optionmaxoutputcolumns*): überschreibt die standardmäßige maximale Anzahl von Spalten, die von einer Abfrage erzeugt werden dürfen. Lange
* `norequesttimeout` (*Optionnorequesttimeout*): ermöglicht das Festlegen des Anforderungs Timeouts auf den maximalen Wert. Booleschen
* `notruncation` (*Optionnotrunation*): ermöglicht das Abschneiden der Abrufergebnisse der Abfrageergebnisse, die an den Aufrufer zurückgegeben werden. Booleschen
* `push_selection_through_aggregation` (*Optionpushselectiondurchsatz Aggregation*): bei "true" wird eine einfache Auswahl über die Aggregation durchlaufen [Boolean]
* `query_bin_auto_at` (*Querybinautoat*): der zu verwendende Startwert beim Auswerten der bin_auto ()-Funktion. [Literalexpression]
* `query_bin_auto_size` (*Querybinautosize*): bei der Auswertung der bin_auto ()-Funktion wird der Wert für die bin-Größe verwendet. [Literalexpression]
* `query_cursor_after_default` (*Optionquerycurrsorafterdefault*): der Standardparameter Wert der cursor_after ()-Funktion, wenn Sie ohne Parameter aufgerufen wird. [string]
* `query_cursor_before_or_at_default` (*Optionquerycurrsorbeforeoratdefault*): der Standardparameter Wert der cursor_before_or_at ()-Funktion, wenn Sie ohne Parameter aufgerufen wird. [string]
* `query_cursor_current` (*Optionquerycursorcurrent*): überschreibt den Cursor Wert, der von den Funktionen "cursor_current ()" oder "current_cursor ()" zurückgegeben wird. [string]
* `query_cursor_disabled` (*Optionquerycursordeaktiviert*): deaktiviert die Verwendung von Cursor Funktionen im Kontext der Abfrage. booleschen
* `query_cursor_scoped_tables` (*Optionquerycurrsorscopedtables*): Liste der Tabellennamen, die auf cursor_after_default festgelegt werden sollen. cursor_before_or_at_default (obere Grenze ist optional). schem
* `query_datascope` (*Optionquerydatascope*): steuert das Datascope der Abfrage, unabhängig davon, ob die Abfrage für alle Daten oder nur einen Teil davon gilt. [' default ', ' all ' oder ' hotcache ']
* `query_datetimescope_column` (*Optionquerydatetimescopecolren*): steuert den Spaltennamen für den DateTime-Gültigkeitsbereich der Abfrage (query_datetimescope_to/query_datetimescope_from). [String]
* `query_datetimescope_from` (*Optionquerydatetimescopefrom*): steuert den DateTime-Gültigkeitsbereich der Abfrage (frühestens), der als automatisch angewendete Filter für query_datetimescope_column verwendet wird (sofern definiert). [DateTime]
* `query_datetimescope_to` (*Optionquerydatetimescopeto*): steuert den DateTime-Gültigkeitsbereich der Abfrage (Latest), der als automatisch angewendete Filter auf query_datetimescope_column (sofern definiert) verwendet wird. [DateTime]
* `query_distribution_nodes_span` (*Optionquerydistributionnodesspansize*): steuert die Art und Weise, wie sich die Unterabfrage Zusammenführung verhält: der ausgeführte Knoten führt in der Abfrage Hierarchie für jede Untergruppe von Knoten eine zusätzliche Ebene ein. die Größe der Untergruppe wird mit dieser Option festgelegt. Wartenden
* `query_fanout_nodes_percent` (*Optionqueryfanoutnodespercent*): der Prozentsatz der Knoten, an denen die Ausführung verzweigen werden soll. Wartenden
* `query_fanout_threads_percent` (*Optionqueryfanoutthreadspercent*): der Prozentsatz der Threads, die ausgeführt werden sollen. Wartenden
* `query_force_row_level_security` (*Optionqueryforcerowlevelsecurity*): erzwingt bei Angabe Sicherheit auf Zeilenebene Regeln, auch wenn row_level_security Richtlinie deaktiviert ist [Boolean]
* `query_language` (*Optionquerylanguage*): steuert, wie der Abfragetext interpretiert werden soll. [' CSL ', ' kql ' oder ' SQL ']
* `query_max_entities_in_union` (*Optionmaxentitiestounion*): überschreibt die standardmäßige maximale Anzahl von Spalten, die von einer Abfrage erzeugt werden dürfen. Lange
* `query_now` (*Optionquerynow*): überschreibt den DateTime-Wert, der von der Now (0s)-Funktion zurückgegeben wird. [DateTime]
* `query_python_debug` (*Optiondebugpython*): Wenn festgelegt, generieren Sie eine python-debugabfrage für den aufgezählten python-Knoten (Standard zuerst). [Boolean oder int]
* `query_results_apply_getschema` (*Optionqueryresultapplygetschema*): Wenn dieser Wert festgelegt ist, wird das Schema aller tabellarischen Daten in den Ergebnissen der Abfrage anstelle der eigentlichen Daten abgerufen. Booleschen
* `query_results_cache_max_age` (*Optionqueryresultscachemaxage*): Wenn dies positiv ist, steuert das maximale Alter der zwischengespeicherten Abfrageergebnisse, die Kusto zurückgeben darf [TimeSpan]
* `query_results_progressive_row_count` (*Optionprogressivequeryminrowzähltperupdate*): ein Hinweis für Kusto, wie viele Datensätze in jedem Update gesendet werden sollen (wird nur wirksam, wenn optionresultsprogressiveaktivierte festgelegt ist).
* `query_results_progressive_update_period` (*Optionprogressiveprogressreportperiod*): Hinweis für Kusto, wie oft Status Rahmen gesendet werden sollen (wird nur wirksam, wenn optionresultsprogressiveaktivierte festgelegt ist)
* `query_take_max_records` (*Optiontakemaxrecords*): ermöglicht das Einschränken von Abfrage Ergebnissen auf diese Anzahl von Datensätzen. Lange
* `queryconsistency` (*Optionquerykonsistenz*): steuert die Abfrage Konsistenz. [' strongkonsistenz ' oder ' normalkonsistenz ' oder ' weakkonsistenz ']
* `request_block_row_level_security` (*Optionrequestblockrowlevelsecurity*): blockiert den Zugriff auf Tabellen, für die row_level_security Richtlinie aktiviert ist [Boolean], falls angegeben.
* `request_callout_disabled` (*Optionrequestcalloutdeaktiviert*): Wenn angegeben, gibt an, dass die Anforderung einen vom Benutzer bereitgestellten Dienst nicht aufrufen kann. Booleschen
* `request_description` (*Optionrequestdescription*): beliebiger Text, den der Autor der Anforderung als Anforderungsbeschreibung einschließen möchte. [String]
* `request_external_table_disabled` (*Optionrequestexternaltabledeaktiviert*): gibt an, dass die Anforderung keinen Code in der externaltable aufrufen kann, wenn Sie angegeben ist. Booleschen
* `request_impersonation_disabled` (*Optiondonotimpersonate*): gibt ggf. an, dass der Dienst die Identität des Aufrufers nicht annehmen soll. Booleschen
* `request_readonly` (*Optionrequestread only*): Wenn angegeben, gibt an, dass die Anforderung nichts schreiben darf. Booleschen
* `request_remote_entities_disabled` (*Optionrequestremoteentitiesdeaktiviert*): Wenn angegeben, gibt an, dass die Anforderung nicht auf Remote Datenbanken und Cluster zugreifen kann. Booleschen
* `request_sandboxed_execution_disabled` (*Optionrequestsandboxedexecutiondeaktiviert*): gibt an, dass die Anforderung keinen Code in der Sandbox aufrufen kann, wenn Sie angegeben ist. Booleschen
* `results_progressive_enabled` (*Optionresultsprogressiveaktiviert*): Wenn festgelegt, wird der Progressive Abfrage Datenstrom aktiviert.
* `servertimeout` (*Optionservertimeout*): überschreibt das standardmäßige Anforderungs Timeout. TimeSpan
* `truncationmaxrecords` (*Optiontruncationmaxrecords*): überschreibt die standardmäßige maximale Anzahl von Datensätzen, die eine Abfrage an den Aufrufer zurückgeben darf (Abschneiden). Lange
* `truncationmaxsize` (*Optiontruncationmaxsize*): überschreibt die standardmäßige maximale Datengröße, die eine Abfrage an den Aufrufer zurückgeben darf (Abschneiden). Lange
* `validate_permissions` (*Optionvalidateberechtigungen*): überprüft die Berechtigungen des Benutzers, um die Abfrage auszuführen, und führt die Abfrage nicht selbst aus. Booleschen
