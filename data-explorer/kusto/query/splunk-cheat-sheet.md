---
title: Splunk zu Kusto Log Query Language in Azure Monitor
description: Hilfe für Benutzer, die mit Splunk beim Erlernen von Kusto-Protokoll Abfragen vertraut sind.
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: f574493f2029d08fd71b44c858592a6fd8467c82
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94499102"
---
# <a name="splunk-to-kusto-query-language"></a>Splunk zu Kusto-Abfragesprache

Dieser Artikel soll Benutzern helfen, die mit Splunk vertraut sind, um die Kusto-Abfragesprache zum Schreiben von Protokoll Abfragen in Kusto zu erlernen. Die beiden Sprachen werden direkt miteinander verglichen, um wichtige Unterschiede sowie Ähnlichkeiten, die Ihnen eine Nutzung Ihrer vorhandenen Kenntnisse ermöglichen, zu veranschaulichen.

## <a name="structure-and-concepts"></a>Struktur und Konzepte

In der folgenden Tabelle werden die Konzepte und Datenstrukturen zwischen Splunk-und Kusto-Protokollen verglichen.

 | Konzept | Splunk | Kusto |  Comment |
 |:---|:---|:---|:---|
 | Bereitstellungseinheit  | cluster |  cluster |  Kusto ermöglicht beliebige Cluster übergreifende Abfragen. Bei Splunk ist das nicht der Fall. |
 | Datencaches |  buckets  |  Caching und Aufbewahrungsrichtlinien |  Steuert den Zeitraum und die Cachingebene für die Daten. Diese Einstellung wirkt sich direkt auf die Leistung von Abfragen und die Kosten der Bereitstellung aus. |
 | Logische Partition von Daten  |  Index  |  database  |  Ermöglicht die logische Trennung der Daten. Beide Implementierungen ermöglichen partitionsübergreifende Unions und Verknüpfungen. |
 | Strukturierte Ereignismetadaten | – | table |  Bei Splunk wird das Konzept der Suchsprache für Ereignismetadaten nicht verfügbar gemacht. Kusto-Protokolle haben das Konzept einer Tabelle mit Spalten. Jede Ereignisinstanz ist einer Zeile zugeordnet. |
 | Datensatz | Ereignis | row |  Nur Terminologieänderung. |
 | Datensatzattribut | Feld |  column |  In Kusto ist dies als Teil der Tabellenstruktur vordefiniert. In Splunk hat jedes Ereignis einen eigenen Satz von Feldern. |
 | Typen | datatype |  datatype |  Kusto-Datentypen sind expliziter, da Sie für die Spalten festgelegt werden. Beide verfügen über die Möglichkeit, dynamisch mit Datentypen und einem sich weitgehend entsprechenden Satz von Datentypen, einschließlich JSON-Unterstützung, zu arbeiten. |
 | Abfrage und Suche  | search | Abfrage |  Konzepte sind im Wesentlichen identisch zwischen Kusto und Splunk. |
 | Ereigniserfassungszeit | Systemzeit | ingestion_time() |  In Splunk erhält jedes Ereignis einen Systemzeitstempel des Zeitpunkts, zu dem das Ereignis indiziert wurde. In Kusto können Sie eine Richtlinie mit dem Namen ingestion_time definieren, die eine System Spalte verfügbar macht, auf die über die ingestion_time ()-Funktion verwiesen werden kann. |

## <a name="functions"></a>Functions

In der folgenden Tabelle sind die Funktionen in Kusto angegeben, die den Splunk-Funktionen entsprechen.

|Splunk | Kusto |Kommentar
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br>`tolong()`<br>`toint()` | (1) |
| `upper`<br>`lower` |toupper()<br>`tolower()`|(1) |
| `replace` | `replace()` | (1)<br> Beachten Sie auch, dass `replace()` zwar bei beiden Produkten drei Parameter verwendet, die Parameter aber unterschiedlich sind. |
| `substr` | `substring()` | (1)<br>Beachten Sie auch, dass Splunk einsbasierte Indizes verwendet. Kusto notiert null basierte Indizes. |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | In Splunk ist `regex` ein Operator. In Kusto ist es ein relationaler Operator. |
| `searchmatch` | == | In Splunk ermöglicht `searchmatch` eine Suche nach der exakten Zeichenfolge.
| `random` | rand()<br>rand(n) | Die Splunk-Funktion gibt eine Zahl zwischen 0 (null) und 2<sup>31</sup>-1 zurück. Kusto ' gibt eine Zahl zwischen 0,0 und 1,0 oder, wenn ein Parameter bereitgestellt wird, zwischen 0 und n-1 zurück.
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br>In Kusto ist das Splunk-Äquivalent von relative_time (datetimeval, offsetval) datetimeval +-TimeSpan (offsetval).<br><code>search &#124; eval n=relative_time(now(), "-1d@d")</code> wird beispielsweise zu <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) In Splunk wird die Funktion mit dem `eval`-Operator aufgerufen. In Kusto wird Sie als Teil von oder verwendet `extend` `project` .<br>(2) In Splunk wird die Funktion mit dem `eval`-Operator aufgerufen. In Kusto kann Sie mit dem-Operator verwendet werden `where` .


## <a name="operators"></a>Operatoren

In den folgenden Abschnitten sind Beispiele für die Verwendung verschiedener Operatoren zwischen Splunk und Kusto aufgeführt.

> [!NOTE]
> Im folgenden Beispiel wird die Splunk- _Feldregel_ einer Tabelle in Kusto zugeordnet, und der Standardzeit Stempel von Splunk wird der Spalte Logs Analytics _ingestion_time ()_ zugeordnet.

### <a name="search"></a>Suchen,
In Splunk können Sie das Schlüsselwort `search` auslassen und eine Zeichenfolge ohne Anführungszeichen eingeben. In Kusto müssen Sie jede Abfrage mit starten `find` , eine Zeichenfolge ohne Anführungszeichen ist ein Spaltenname, und der Such Wert muss eine Zeichenfolge in Anführungszeichen sein. 

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `search` | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Kusto | `find` | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filter
Kusto-Protokoll Abfragen beginnen von einem tabellarischen Resultset mit dem Filter. In Splunk ist Filtern der Standardvorgang für den aktuellen Index. Sie können auch den `where`-Operator in Splunk verwenden, doch wird dies nicht empfohlen.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `search` | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Kusto | `where` | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Abrufen von n Ereignissen/Zeilen zur Überprüfung 
Kusto-Protokoll Abfragen unterstützen auch `take` als Alias für `limit` . Wenn in Splunk die Ergebnisse sortiert sind, gibt `head` die ersten n Ergebnisse zurück. In Kusto ist Limit nicht geordnet, sondern gibt die ersten n Zeilen zurück, die gefunden werden.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `head` | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Kusto | `limit` | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Abrufen der ersten n Ereignisse/Zeilen nach Feld/Spalte sortiert
Für niedrigste Ergebnisse in Splunk verwenden Sie `tail`. In Kusto können Sie die Reihenfolge der Sortierreihenfolge mit angeben `asc` .

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `head` |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Kusto | `top` | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Erweitern des Resultset mit neuen Feldern/Spalten
Splunk verfügt auch über eine `eval`-Funktion, die nicht mit dem `eval`-Operator vergleichbar ist. Sowohl der `eval` Operator in Splunk als auch der `extend` Operator in Kusto unterstützen nur skalare Funktionen und arithmetische Operatoren.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `eval` |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Kusto | `extend` | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Umbenennen 
Kusto verwendet den- `project-rename` Operator zum Umbenennen eines Felds. `project-rename` ermöglicht der Abfrage, alle für ein Feld vorab erstellten Indizes zu nutzen. Splunk verfügt für den gleichen Zweck über einen `rename`-Operator.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `rename` |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Kusto | `project-rename` | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Formatieren von Ergebnissen/Projektion
Splunk scheint keinen Operator aufzuweisen, der `project-away` ähnelt. Sie können die Benutzeroberfläche verwenden, um Felder auszufiltern.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `table` |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Kusto | `project`<br>`project-away` | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Aggregation
Weitere Informationen finden Sie [in der Liste der Aggregationen Funktionen](summarizeoperator.md#list-of-aggregation-functions) für die verschiedenen Aggregationen Funktionen.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `stats` |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Kusto | `summarize` | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
Das Verknüpfen in Splunk weist wesentliche Einschränkungen auf. Für die Unterabfrage gilt ein Grenzwert von 10000 Ergebnissen (in der Bereitstellungskonfigurationsdatei festgelegt) sowie eine begrenzte Anzahl von Verknüpfungskonfigurationen.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `join` |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Kusto | `join` | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Sortieren
In Splunk müssen Sie zum Sortieren in aufsteigender Reihenfolge den `reverse`-Operator verwenden. Kusto unterstützt auch das definieren, wo Nullen am Anfang oder am Ende abgelegt werden.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `sort` |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Kusto | `order by` | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Erweiterung für mehrere Werte
Dies ist ein ähnlicher Operator in Splunk und Kusto.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand foo` |
| Kusto | `mvexpand` | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Ergebnisfacets, interessante Felder
In Log Analytics im Azure-Portal wird nur die erste Spalte verfügbar gemacht. Alle Spalten stehen über die API zur Verfügung.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `fields` |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Kusto | `facets` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>Deduplizieren
Sie können stattdessen `summarize arg_min()` verwenden, um die Reihenfolge umzukehren, in der ein Datensatz ausgewählt wird.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `dedup` |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Kusto | `summarize arg_max()` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Nächste Schritte

- Führen Sie ein Tutorial zur [Kusto-Abfragesprache](tutorial.md?pivots=azuremonitor)durch.
