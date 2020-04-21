---
title: Anforderungseigenschaften, ClientRequestProperties - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Anforderungseigenschaften, ClientRequestProperties in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 0aa496e6011fce98db5a304b9748f27f07590b4f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524326"
---
# <a name="request-properties-clientrequestproperties"></a>Anforderungseigenschaften, ClientRequestProperties

Wenn Sie eine Anforderung von Kusto über das .NET SDK stellen Sie im Allgemeinen die folgenden Werte bereit:

1. Eine Verbindungszeichenfolge, die den Dienstendpunkt angibt, mit dem eine Verbindung hergestellt werden soll, Authentifizierungsparameter und ähnliche verbindungsbezogene Informationen. Programmgesteuert wird die Verbindungszeichenfolge durch `KustoConnectionStringBuilder` die Klasse dargestellt.

2. Der Name der Datenbank, die verwendet wird, um den "Bereich" der Anforderung zu beschreiben.

3. Der Text der Anforderung (Abfrage oder Befehl) selbst.

4. Zusätzliche Eigenschaften, die der Client bereitstellt und auf die Anforderung angewendet wird. Programmgesteuert werden diese Eigenschaften von einer `ClientRequestProperties`Klasse namens gehalten.

Die Clientanforderungseigenschaften haben viele Verwendungen. Einige von ihnen werden verwendet, um das Debuggen zu vereinfachen (z. B. durch bereitstellen von Korrelationszeichenfolgen, die zum Nachverfolgen von Client-/Dienstinteraktionen verwendet werden können), andere werden verwendet, um zu beeinflussen, welche Grenzwerte und Richtlinien auf die Anforderung angewendet werden, während eine dritte Kategorie solcher Eigenschaften [Abfrageparameter](../../query/queryparametersstatement.md)sind. Eine vollständige Liste der unterstützten Eigenschaften wird auf dieser Seite angezeigt.

Die `Kusto.Data.Common.ClientRequestProperties` Klasse enthält drei Arten von Daten:

* Benannte Eigenschaften.

* Optionen (Zuordnung eines Optionsnamens zu einem Optionswert).

* Parameter (Zuordnung eines Abfrageparameternamens zu einem Abfrageparameterwert).

> [!NOTE]
> Einige benannte Eigenschaften sind als "nicht verwenden" gekennzeichnet. Solche Eigenschaften sollten nicht von Clients angegeben werden und keine Auswirkungen auf den Dienst haben.

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>Die ClientRequestId (x-ms-client-request-id) benannte Eigenschaft

Diese benannte Eigenschaft enthält die vom Client angegebene Identität der Anforderung. Es wird dringend empfohlen, dass ein eindeutiger Wert pro Anforderung von Clients bei jeder anforderung angegeben wird, die sie senden, da dies das Debuggen von Fehlern vereinfacht (und in einigen Szenarien erforderlich ist, z. B. bei abfrageabbruch).

Der programmgesteuerte Name `ClientRequestId`dieser Eigenschaft ist , und `x-ms-client-request-id`es wird in den HTTP-Header übersetzt.

Diese Eigenschaft wird vom SDK auf einen (zufälligen) Wert festgelegt, wenn der Client keinen eigenen Wert angibt.

Der Inhalt dieser Eigenschaft kann eine beliebige druckbare eindeutige Zeichenfolge sein, z. B. eine GUID.
Es wird jedoch empfohlen, dass Clients die folgende Vorlage verwenden:

*ApplicationName* `.` *ActivityName* `;` *UniqueId*

Wenn *ApplicationName* die Clientanwendung identifiziert, die die Anforderung ausstellt, identifiziert *ActivityName* die Art der Aktivität, für die die Clientanwendung die Clientanforderung ausgibt, und *UniqueId* identifiziert die spezifische Anforderung.

## <a name="the-application-x-ms-app-named-property"></a>Die Application (x-ms-app) benannte Eigenschaft

Diese benannte Eigenschaft enthält den Namen der Clientanwendung, die die Anforderung zu Ablaufverfolgungszwecken stellt.

Der programmgesteuerte Name `Application`dieser Eigenschaft ist , und `x-ms-app`es wird in den HTTP-Header übersetzt. Sie kann in der Kusto-Verbindungszeichenfolge als `Application Name for Tracing`angegeben werden.

Diese Eigenschaft wird auf den Namen des Prozesses festgelegt, der das SDK hostet, wenn der Client keinen eigenen Wert angibt.

## <a name="the-user-x-ms-user-named-property"></a>Die Nameeigenschaft "Benutzer" (x-ms-user)

Diese benannte Eigenschaft enthält die Identität des Benutzers, der die Anforderung zu Ablaufverfolgungszwecken stellt.

Der programmgesteuerte Name `User`dieser Eigenschaft ist , und `x-ms-user`es wird in den HTTP-Header übersetzt. Sie kann in der Kusto-Verbindungszeichenfolge als `User Name for Tracing`angegeben werden.

## <a name="controlling-request-properties-using-the-rest-api"></a>Steuern von Anforderungseigenschaften mithilfe der REST-API

Verwenden Sie beim Ausstellen einer HTTP-Anforderung an den Kusto-Dienst den `properties` Steckplatz im JSON-Dokument, das der POST-Anforderungstext ist, um Anforderungseigenschaften bereitzustellen. Beachten Sie, dass einige der Eigenschaften (z. B. die "Clientanforderungs-ID", d. h. die Korrelations-ID, die der Client dem Dienst zum Identifizieren der Anforderung bereitstellt) im HTTP-Header bereitgestellt werden können und daher auch festgelegt werden können, wenn HTTP GET verwendet wird.
Weitere Informationen finden Sie [im Kusto REST-API-Anforderungsobjekt.](../rest/request.md)

## <a name="providing-values-for-query-parametrization-as-request-properties"></a>Bereitstellen von Werten für die Abfrageparametrisierung als Anforderungseigenschaften

Kusto-Abfragen können auf Abfrageparameter verweisen, indem eine spezielle [Declare-Query-Parameters-Anweisung](../../query/queryparametersstatement.md) im Abfragetext verwendet wird. Auf diese Weise können Clientanwendungen Kusto-Abfragen auf sichere Weise auf der Grundlage von Benutzereingaben parametrisieren (ohne Angst vor Injektionsangriffen).

Programmgesteuert können Eigenschaftenwerte mithilfe der `ClearParameter`, `SetParameter`und `HasParameter` Methoden festgelegt werden.

In der REST-API werden Abfrageparameter in derselben JSON-codierten Zeichenfolge wie die anderen Anforderungseigenschaften angezeigt.

## <a name="sample-code-for-using-request-properties"></a>Beispielcode für die Verwendung von Anforderungseigenschaften

Hier ist ein Beispiel für Clientcode:

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
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
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="list-of-clientrequestproperties"></a>Liste der ClientRequestProperties

<!-- The following is auto-generated by running the following command: -->
<!-- Kusto.Cli.exe -execute:"#crp -doc"                                -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `debug_query_externaldata_projection_fusion_disabled`(*OptionDebugQueryDisableExternalDataProjectionFusion*): Wenn festgelegt, sichern Sie die Projektion nicht in den ExternalData-Operator. [Boolesche]
* `debug_query_fanout_threads_percent_external_data`(*OptionDebugQueryFanoutThreadsPercentExternalData*): Der Prozentsatz der Threads, an die die Ausführung für externe Datenknoten fanout. [Int]
* `deferpartialqueryfailures`(*OptionDeferPartialQueryFailures*): Wenn true, deaktiviert das Melden von teilgestellten Abfragefehlern als Teil des Resultsets. [Boolesche]
* `max_memory_consumption_per_query_per_node`(*OptionMaxMemoryConsumptionPerQueryPerNode*): Überschreibt die standardmäßige maximale Speichermenge, die eine ganze Abfrage pro Knoten zuweisen kann. [UInt64]
* `maxmemoryconsumptionperiterator`(*OptionMaxMemoryConsumptionPerIterator*): Überschreibt die standardmäßige maximale Speichermenge, die ein Abfrageoperator zuweisen kann. [UInt64]
* `maxoutputcolumns`(*OptionMaxOutputColumns*): Überschreibt die standardmäßige maximale Anzahl von Spalten, die eine Abfrage erzeugen darf. [Lang]
* `norequesttimeout`(*OptionNoRequestTimeout*): Aktiviert das Festlegen des Anforderungstimeouts auf den maximalen Wert. [Boolesche]
* `notruncation`(*OptionNoTruncation*): Aktiviert das Unterdrücken des Abschneidens der Abfrageergebnisse, die an den Aufrufer zurückgegeben werden. [Boolesche]
* `push_selection_through_aggregation`(*OptionPushSelectionThroughAggregation*): Wenn true, drücken Sie einfache Auswahl durch Aggregation [Boolean]
* `query_admin_super_slacker_mode`(*OptionAdminSuperSlackerMode*): Wenn true, delegieren Sie die Ausführung der Abfrage an einen anderen Knoten [Boolean]
* `query_bin_auto_at`(*QueryBinAutoAt*): Beim Auswerten der bin_auto() Funktion wird der zu verwendende Startwert verwendet. [LiteralExpression]
* `query_bin_auto_size`(*QueryBinAutoSize*): Beim Auswerten der bin_auto()-Funktion wird der zu verwendende Wert für die Lagerplatzgröße verwendet. [LiteralExpression]
* `query_cursor_after_default`(*OptionQueryCursorAfterDefault*): Der Standardparameterwert der cursor_after()-Funktion, wenn er ohne Parameter aufgerufen wird. [string]
* `query_cursor_before_or_at_default`(*OptionQueryCursorBeforeOrAtDefault*): Der Standardparameterwert der cursor_before_or_at()-Funktion, wenn er ohne Parameter aufgerufen wird. [string]
* `query_cursor_current`(*OptionQueryCursorCurrent*): Überschreibt den Cursorwert, der von den Funktionen cursor_current() oder current_cursor() zurückgegeben wird. [string]
* `query_cursor_scoped_tables`(*OptionQueryCursorScopedTables*): Liste der Tabellennamen, die auf cursor_after_default . cursor_before_or_at_default (obere Grenze ist optional). [dynamisch]
* `query_datascope`(*OptionQueryDataScope*): Steuert das Datascope der Abfrage - unabhängig davon, ob die Abfrage auf alle Daten oder nur einen Teil davon zutrifft. ['default', 'all' oder 'hotcache']
* `query_datetimescope_column`(*OptionQueryDateTimeScopeColumn*): Steuert den Spaltennamen für den Datumszeitbereich der Abfrage (query_datetimescope_to / query_datetimescope_from). [String]
* `query_datetimescope_from`(*OptionQueryDateTimeScopeFrom*): Steuert den Datumszeitbereich der Abfrage (frühestens) -- wird als automatisch angewendeter Filter nur für query_datetimescope_column (falls definiert) verwendet. [DateTime]
* `query_datetimescope_to`(*OptionQueryDateTimeScopeTo*): Steuert den Datumszeitbereich der Abfrage (zuletzt) -- wird als automatisch angewendeter Filter nur für query_datetimescope_column verwendet (falls definiert). [DateTime]
* `query_distribution_nodes_span`(*OptionQueryDistributionNodesSpanSize*): Wenn festgelegt, steuert die Funktionsweise der Unterabfragezusammenführung: Der ausführende Knoten führt eine zusätzliche Ebene in der Abfragehierarchie für jede Untergruppe von Knoten ein. Die Größe der Untergruppe wird durch diese Option festgelegt. [Int]
* `query_fanout_nodes_percent`(*OptionQueryFanoutNodesPercent*): Der Prozentsatz der Knoten, an die die Ausflüftung sanatausgeführt werden soll. [Int]
* `query_fanout_threads_percent`(*OptionQueryFanoutThreadsPercent*): Der Prozentsatz der Threads, auf die die Ausführung fanouten soll. [Int]
* `query_language`(*OptionQueryLanguage*): Steuert, wie der Abfragetext interpretiert werden soll. ['csl','kql' oder 'sql']
* `query_max_entities_in_union`(*OptionMaxEntitiesToUnion*): Überschreibt die standardmäßige maximale Anzahl von Spalten, die eine Abfrage erzeugen darf. [Lang]
* `query_now`(*OptionQueryNow*): Überschreibt den datetime-Wert, der von der now(0s)-Funktion zurückgegeben wird. [DateTime]
* `query_python_debug`(*OptionDebugPython*): Wenn festgelegt, generieren Sie python debug query für den aufgezählten Python-Knoten (Standard zuerst). [Booleleoder oder Int]
* `query_results_apply_getschema`(*OptionQueryResultsApplyGetSchema*): Wenn festgelegt, ruft das Schema der einzelnen Tabellendaten in den Ergebnissen der Abfrage anstelle der Daten selbst ab. [Boolesche]
* `query_results_cache_max_age`(*OptionQueryResultsCacheMaxAge*): Wenn positiv, steuert das maximale Alter der zwischengespeicherten Abfrageergebnisse, die Kusto zurückgeben darf [TimeSpan]
* `query_results_progressive_row_count`(*OptionProgressiveQueryMinRowCountPerUpdate*): Hinweis für Kusto, wie viele Datensätze in jeder Aktualisierung gesendet werden sollen (wird nur wirksam, wenn OptionResultsProgressiveEnabled gesetzt ist)
* `query_results_progressive_update_period`(*OptionProgressiveProgressReportPeriod*): Hinweis für Kusto, wie oft Fortschrittsframes gesendet werden (wird nur wirksam, wenn OptionResultsProgressiveEnabled gesetzt ist)
* `query_shuffle_broadcast_join`(*ShuffleBroadcastJoin*): Aktiviert das Shuffling über Broadcast-Join.
* `query_take_max_records`(*OptionTakeMaxRecords*): Ermöglicht das Beschränken von Abfrageergebnissen auf diese Anzahl von Datensätzen. [Lang]
* `queryconsistency`(*OptionQueryConsistency*): Steuert die Abfragekonsistenz. ['strongconsistency' oder 'normalconsistency' oder 'weakconsistency']
* `request_callout_disabled`(*OptionRequestCalloutDisabled*): Wenn angegeben, gibt die Anforderung an, dass sie nicht an einen vom Benutzer bereitgestellten Dienst aufrufen kann. [Boolesche]
* `request_description`(*OptionRequestDescription*): Beliebiger Text, den der Autor der Anforderung als Anforderungsbeschreibung einfügen möchte. [String]
* `request_external_table_disabled`(*OptionRequestExternalTableDisabled*): Wenn angegeben, gibt die Anforderung an, dass der Code in der ExternalTable nicht aufgerufen werden kann. [Boolesche]
* `request_readonly`(*OptionRequestReadOnly*): Wenn angegeben, gibt an, dass die Anforderung nichts schreiben kann. [Boolesche]
* `request_remote_entities_disabled`(*OptionRequestRemoteEntitiesDisabled*): Wenn angegeben, gibt die Anforderung an, dass die Anforderung nicht auf entfernte Datenbanken und Cluster zugreifen kann. [Boolesche]
* `request_sandboxed_execution_disabled`(*OptionRequestSandboxedExecutionDisabled*): Wenn angegeben, gibt die Anforderung an, dass die Anforderung keinen Code in der Sandbox aufrufen kann. [Boolesche]
* `results_progressive_enabled`(*OptionResultsProgressiveEnabled*): Wenn festgelegt, aktiviert den progressiven Abfragestream
* `servertimeout`(*OptionServerTimeout*): Überschreibt das Standardanforderungstimeout. [TimeSpan]
* `truncationmaxrecords`(*OptionTruncationMaxRecords*): Überschreibt die standardmäßige maximale Anzahl von Datensätzen, die eine Abfrage an den Aufrufer zurückgeben darf (Abschneiden). [Lang]
* `truncationmaxsize`(*OptionTruncationMaxSize*): Überschreibt die maximale Datengröße von dfefault, die eine Abfrage an den Aufrufer zurückgeben darf (Abschneiden). [Lang]
* `validate_permissions`(*OptionValidatePermissions*): Überprüft die Berechtigungen des Benutzers zum Ausführen der Abfrage und führt die Abfrage nicht selbst aus. [Boolesche]

