---
title: Splunk zu Kusto-Zuordnung für Azure Daten-Explorer und Azure Monitor
description: Konzept Zuordnung für Benutzer, die mit Splunk vertraut sind, um die Kusto-Abfragesprache zum Schreiben von Protokoll Abfragen zu erlernen.
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 8679b47a2c698c88c4d1773f9c50dee495532ae7
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783232"
---
# <a name="splunk-to-kusto-query-language-map"></a>Splunk zu Kusto-Abfrage sprach Zuordnung

Dieser Artikel soll Benutzern helfen, die mit Splunk vertraut sind und die Kusto-Abfragesprache erlernen, um Protokoll Abfragen mit Kusto zu schreiben. Direkt Vergleiche werden zwischen den beiden vorgenommen, um wichtige Unterschiede und Ähnlichkeiten hervorzuheben, damit Sie auf Ihr vorhandenes Wissen aufbauen können.

## <a name="structure-and-concepts"></a>Struktur und Konzepte

In der folgenden Tabelle werden die Konzepte und Datenstrukturen zwischen Splunk-und Kusto-Protokollen verglichen:

 | Konzept | Splunk | Kusto |  Comment |
 |:---|:---|:---|:---|
 | Bereitstellungs Einheit  | cluster |  cluster |  Kusto ermöglicht beliebige Cluster übergreifende Abfragen. Bei Splunk ist das nicht der Fall. |
 | Daten Caches |  buckets  |  Caching-und Aufbewahrungs Richtlinien |  Steuert den Zeitraum und die Cachingebene für die Daten. Diese Einstellung wirkt sich direkt auf die Leistung von Abfragen und die Kosten der Bereitstellung aus. |
 | logische Daten Partition  |  Index  |  database  |  Ermöglicht die logische Trennung der Daten. Beide Implementierungen ermöglichen partitionsübergreifende Unions und Verknüpfungen. |
 | strukturierte Ereignis Metadaten | – | table |  Splunk macht das Konzept der Ereignis Metadaten nicht für die Suchsprache verfügbar. Kusto-Protokolle haben das Konzept einer Tabelle mit Spalten. Jede Ereignisinstanz ist einer Zeile zugeordnet. |
 | Datensatz | Ereignis | row |  Nur Terminologieänderung. |
 | Daten Satz Attribut | Feld |  column |  In Kusto ist diese Einstellung als Teil der Tabellenstruktur vordefiniert. In Splunk hat jedes Ereignis einen eigenen Satz von Feldern. |
 | types | datatype |  datatype |  Kusto-Datentypen sind expliziter, da Sie für die Spalten festgelegt sind. Beide können dynamisch mit Datentypen arbeiten und ungefähr äquivalente Datentypen, einschließlich JSON-Unterstützung. |
 | Abfragen und suchen  | search | Abfrage |  Konzepte sind im Wesentlichen identisch zwischen Kusto und Splunk. |
 | Ereignis Erfassungs Zeit | Systemuhrzeit | `ingestion_time()` |  In Splunk erhält jedes Ereignis einen Systemzeitstempel der Zeit, zu der das Ereignis indiziert wurde. In Kusto können Sie eine Richtlinie mit dem Namen [ingestion_time](../management/ingestiontimepolicy.md) definieren, die eine System Spalte verfügbar macht, auf die über die [ingestion_time ()](ingestiontimefunction.md) -Funktion verwiesen werden kann. |

## <a name="functions"></a>Functions

In der folgenden Tabelle sind die Funktionen in Kusto angegeben, die den Splunk-Funktionen entsprechen.

|Splunk | Kusto |Comment
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br />`tolong()`<br />`toint()` | (1) |
| `upper`<br />`lower` |toupper()<br />`tolower()`|(1) |
| `replace` | `replace()` | (1)<br /> Beachten Sie auch, dass zwar `replace()` in beiden Produkten drei Parameter benötigt, die Parameter jedoch unterschiedlich sind. |
| `substr` | `substring()` | (1)<br />Beachten Sie auch, dass Splunk einsbasierte Indizes verwendet. Kusto notiert null basierte Indizes. |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | In Splunk ist `regex` ein Operator. In Kusto ist es ein relationaler Operator. |
| `searchmatch` | == | In Splunk ermöglicht `searchmatch` eine Suche nach der exakten Zeichenfolge.
| `random` | rand()<br />rand(n) | Die Funktion von Splunk gibt eine Zahl zwischen 0 (null) und 2<sup>31</sup>-1 zurück. Kusto gibt eine Zahl zwischen 0,0 und 1,0 zurück, oder, wenn ein Parameter bereitgestellt wird (zwischen 0 und n-1).
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br />In Kusto ist die Entsprechung von Splunk `relative_time(datetimeVal, offsetVal)` `datetimeVal + totimespan(offsetVal)` .<br />Beispielsweise `search` wird &#124; `eval n=relative_time(now(), "-1d@d")` `...`  &#124; `extend myTime = now() - totimespan("1d")` .

(1) in Splunk wird die Funktion mit dem-Operator aufgerufen `eval` . In Kusto wird Sie als Teil von oder verwendet `extend` `project` .<br />(2) in Splunk wird die Funktion mit dem-Operator aufgerufen `eval` . In Kusto kann Sie mit dem-Operator verwendet werden `where` .


## <a name="operators"></a>Operatoren

In den folgenden Abschnitten wird erläutert, wie verschiedene Operatoren in Splunk und Kusto verwendet werden.

> [!NOTE]
> In den folgenden Beispielen wird das Splunk `rule` -Feld einer Tabelle in Kusto zugeordnet, und der Standardzeit Stempel von Splunk wird der Log Analytics- `ingestion_time()` Spalte zugeordnet.

### <a name="search"></a>Suchen,

In Splunk können Sie das Schlüsselwort `search` auslassen und eine Zeichenfolge ohne Anführungszeichen eingeben. In Kusto müssen Sie jede Abfrage mit starten `find` , eine Zeichenfolge ohne Anführungszeichen ist ein Spaltenname, und der Such Wert muss eine Zeichenfolge in Anführungszeichen sein. 

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `search` | `search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h` |
| Kusto | `find` | `find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)` |


### <a name="filter"></a>Filtern

Kusto-Protokoll Abfragen beginnen von einem tabellarischen Resultset, in dem `filter` angewendet wird. In Splunk ist Filtern der Standardvorgang für den aktuellen Index. Sie können auch den `where` Operator in Splunk verwenden, aber wir empfehlen ihn nicht.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `search` | `Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h` |
| Kusto | `where` | `Office_Hub_OHubBGTaskError`<br />&#124; `where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)` |

### <a name="get-n-events-or-rows-for-inspection"></a>*N* Ereignisse oder Zeilen für die Überprüfung

Kusto-Protokoll Abfragen unterstützen auch `take` als Alias für `limit` . Wenn die Ergebnisse in Splunk geordnet sind, werden `head` die ersten *n* Ergebnisse zurückgegeben. In Kusto ist `limit` nicht geordnet, aber es werden die ersten *n* Zeilen zurückgegeben, die gefunden werden.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `head` | `Event.Rule=330009.2`<br />&#124; `head 100` |
| Kusto | `limit` | `Office_Hub_OHubBGTaskError`<br />&#124; `limit 100` |

### <a name="get-the-first-n-events-or-rows-ordered-by-a-field-or-column"></a>Die ersten *n* Ereignisse oder Zeilen, geordnet nach einem Feld oder einer Spalte, erhalten.

Für die untersten Ergebnisse verwenden Sie in Splunk `tail` . In Kusto können Sie die Sortierrichtung mithilfe von angeben `asc` .

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `head` |  `Event.Rule="330009.2"`<br />&#124; `sort Event.Sequence`<br />&#124; `head 20` |
| Kusto | `top` | `Office_Hub_OHubBGTaskError`<br />&#124; `top 20 by Event_Sequence` |

### <a name="extend-the-result-set-with-new-fields-or-columns"></a>Erweitern des Resultsets mit neuen Feldern oder Spalten

Splunk verfügt über eine `eval` -Funktion, die jedoch nicht mit dem- `eval` Operator in Kusto vergleichbar ist. Sowohl der `eval` Operator in Splunk als auch der- `extend` Operator in Kusto unterstützen nur skalare Funktionen und arithmetische Operatoren.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `eval` |  `Event.Rule=330009.2`<br />&#124; `eval state= if(Data.Exception = "0", "success", "error")` |
| Kusto | `extend` | `Office_Hub_OHubBGTaskError`<br />&#124; `extend state = iif(Data_Exception == 0,"success" ,"error")` |

### <a name="rename"></a>Umbenennen

Kusto verwendet den- `project-rename` Operator zum Umbenennen eines Felds. Im- `project-rename` Operator kann eine Abfrage alle Indizes nutzen, die für ein Feld vorerstellt wurden. Splunk verfügt über einen `rename` Operator, der das gleiche tut.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `rename` |  `Event.Rule=330009.2`<br />&#124; `rename Date.Exception as execption` |
| Kusto | `project-rename` | `Office_Hub_OHubBGTaskError`<br />&#124; `project-rename exception = Date_Exception` |

### <a name="format-results-and-projection"></a>Format Ergebnisse und Projektion

Splunk weist anscheinend keinen Operator auf, der mit vergleichbar ist `project-away` . Sie können die Benutzeroberfläche verwenden, um Felder herauszufiltern.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `table` |  `Event.Rule=330009.2`<br />&#124; `table rule, state` |
| Kusto | `project`<br />`project-away` | `Office_Hub_OHubBGTaskError`<br />&#124; `project exception, state` |

### <a name="aggregation"></a>Aggregation

Weitere Informationen finden Sie [in der Liste der verfügbaren Aggregationen Funktionen](summarizeoperator.md#list-of-aggregation-functions) .

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `stats` |  `search (Rule=120502.*)`<br />&#124; `stats count by OSEnv, Audience` |
| Kusto | `summarize` | `Office_Hub_OHubBGTaskError`<br />&#124; `summarize count() by App_Platform, Release_Audience` |


### <a name="join"></a>Join

`join` in Splunk hat erhebliche Einschränkungen. Die Unterabfrage hat eine Beschränkung von 10.000 Ergebnissen (festgelegt in der Bereitstellungs Konfigurationsdatei), und eine begrenzte Anzahl von joinvarianten ist verfügbar.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `join` |  `Event.Rule=120103* &#124; stats by Client.Id, Data.Alias` <br />&#124; `join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]` |
| Kusto | `join` | `cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions`<br />&#124; `where  Data_Hresult== -2147221040`<br />&#124; `join kind = inner (Office_System_SystemHealthMetadata`<br />&#124; `summarize by Client_Id, Data_Alias)on Client_Id`   |

### <a name="sort"></a>Sortieren

Wenn Sie in Splunk in aufsteigender Reihenfolge sortieren möchten, müssen Sie den- `reverse` Operator verwenden. Kusto unterstützt auch das definieren, wo Nullen platziert werden sollen, entweder am Anfang oder am Ende.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `sort` |  `Event.Rule=120103`<br />&#124; `sort Data.Hresult` <br />&#124; `reverse` |
| Kusto | `order by` | `Office_Hub_OHubBGTaskError`<br />&#124; `order by Data_Hresult,  desc` |

### <a name="multivalue-expand"></a>Erweiterung für mehrere Werte

Der mehrwertige Expand-Operator ist in Splunk und Kusto ähnlich.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand solutions` |
| Kusto | `mv-expand` | `mv-expand solutions` |

### <a name="result-facets-interesting-fields"></a>Ergebnis Facetten, interessante Felder

In Log Analytics im Azure-Portal wird nur die erste Spalte verfügbar gemacht. Alle Spalten stehen über die API zur Verfügung.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `fields` |  `Event.Rule=330009.2`<br />&#124; `fields App.Version, App.Platform` |
| Kusto | `facets` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `facet by App_Branch, App_Version` |

### <a name="deduplicate"></a>Deduplizieren

In Kusto können Sie verwenden, `summarize arg_min()` um die Reihenfolge umzukehren, in der der Datensatz ausgewählt wird.

| Produkt | Operator | Beispiel |
|:---|:---|:---|
| Splunk | `dedup` |  `Event.Rule=330009.2`<br />&#124; `dedup device_id sortby -batterylife` |
| Kusto | `summarize arg_max()` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `summarize arg_max(batterylife, *) by device_id` |

## <a name="next-steps"></a>Nächste Schritte

- Durchlaufen Sie ein Tutorial zur [Kusto-Abfragesprache](tutorial.md?pivots=azuremonitor).
