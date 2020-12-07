---
title: 'Gespeicherte Abfrageergebnisse (Vorschau): Azure-Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie gespeicherte Abfrageergebnisse in Azure Daten-Explorer erstellen und verwenden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mispecto
ms.service: data-explorer
ms.topic: reference
ms.date: 12/3/2020
ms.openlocfilehash: fb998499205be7645f011fe727f5e37495ff3697
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749303"
---
# <a name="stored-query-results-preview"></a>Gespeicherte Abfrageergebnisse (Vorschau)

Speichert die Ergebnisse einer schweren Abfrage und ruft Sie schnell ab.
Gespeicherte Abfrageergebnisse können in den folgenden Szenarien nützlich sein:
* Implementieren Sie die Ergebnis Paginierung. Ein gespeichertes Abfrageergebnis wird auf der Grundlage einer Abfrage erstellt, und auf der ersten Seite wird eine Vorschau angezeigt. Jede nachfolgende Seite zeigt den nächsten Teil des vorab berechneten Ergebnisses, ohne dass die anfängliche Abfrage erneut ausgeführt werden muss.
* Temporäres Speichern von Abfrage Ergebnissen beim Durchsuchen von Daten.

> [!NOTE]
> Die gespeicherten Abfrageergebnisse befinden sich in der Vorschau Phase und sollten nicht in Produktionsszenarien verwendet werden. 

Der Zugriff auf gespeicherte Abfrageergebnisse kann bis zu 24 Stunden nach dem Zeitpunkt der Erstellung erfolgen. Updates an Sicherheitsrichtlinien (z. b. Datenbankzugriff, Sicherheit auf Zeilenebene usw.) werden nicht an gespeicherte Abfrageergebnisse weitergegeben. Verwenden Sie [. Drop-stored_query_results](#drop-stored_query_results) im Falle der Sperrung von Benutzerberechtigungen. Auf ein gespeichertes Abfrageergebnis kann nur über dieselbe Prinzipal Identität zugegriffen werden, die es erstellt hat. 

Gespeicherte Abfrageergebnisse Verhalten sich wie Tabellen, da die Reihenfolge der Datensätze nicht beibehalten wird. Zum paginieren der Ergebnisse wird empfohlen, dass die Abfrage eindeutige ID-Spalten enthält. Weitere Informationen finden Sie unter [Beispiele](#examples). Wenn von einer Abfrage mehrere Resultsets zurückgegeben werden, wird nur das erste Resultset gespeichert. 

Die Verwendung gespeicherter Abfrageergebnisse erfordert `Database User` oder eine höhere Zugriffs Rolle.

## <a name="store-the-results-of-a-query"></a>Speichern der Ergebnisse einer Abfrage

**Syntax**

`.set``stored_query_result` *Storedqueryresultname* [ `with` `(` *propertyName* `=` *PropertyValue* `,` ... `)` ] <| *Abfrage*

**Argumente**

* *Storedqueryresultname*: Name der gespeicherten Abfrageergebnisse, die den Regeln für [Entitäts Namen](../query/schema-entities/entity-names.md) entspricht.
* *Query*: eine potenziell schwerwiegende kql-Abfrage, deren Ergebnisse gespeichert werden.
* *PropertyName*: (alle Eigenschaften sind optional)
    
    | Eigenschaft       | type       | Beschreibung       |
    |----------------|------------|-------------------------------------------------------------------------------------|
    | `expiresAfter` | `timespan` | Ein TimeSpan-Literale, das angibt, wann das Ergebnis der gespeicherten Abfrage abläuft (maximal 24 Stunden). |
    | `previewCount` | `int`      | Die Anzahl der Zeilen, die in einer Vorschau zurückgegeben werden sollen. Wenn diese Eigenschaft auf `0` (Standard) festgelegt wird, gibt der Befehl alle Abfrageergebnis Zeilen zurück. |
    | `distributed`  | `bool`     | Gibt an, dass der Befehl Abfrageergebnisse von allen Knoten speichert, die die Abfrage parallel ausführen. Der Standardwert ist *true*. `distributed`Das Festlegen des Flags auf *false* ist hilfreich, wenn die von einer Abfrage erzeugte Datenmenge gering ist oder eine Reihe von Cluster Knoten groß ist, um das Erstellen vieler kleiner datenshards zu verhindern. |

## <a name="retrieve-a-stored-query-result"></a>Abrufen eines Ergebnisses einer gespeicherten Abfrage

Um ein gespeichertes Abfrageergebnis abzurufen, verwenden `stored_query_result()` Sie die Funktion in der Abfrage:

`stored_query_result``(`"Storedqueryresultname" `)` `|` ...

## <a name="examples"></a>Beispiele

### <a name="simple-query"></a>Einfache Abfrage

Speichern eines einfachen Abfrage Ergebnisses:

<!-- csl -->
```kusto
.set stored_query_result Numbers <| range X from 1 to 1000000 step 1
```

**Ausgabe:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

Ergebnis der gespeicherten Abfrage abrufen:

<!-- csl -->
```kusto
stored_query_result("Numbers")
```

**Ausgabe:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

### <a name="pagination"></a>Paginierung

Abrufen von Klicks in den letzten sieben Tagen nach Ad-Netzwerk und Tag:

<!-- csl -->
```kusto
.set stored_query_result DailyClicksByAdNetwork7Days with (previewCount = 100) <|
Events
| where Timestamp > ago(7d)
| where EventType == 'click'
| summarize Count=count() by Day=bin(Timestamp, 1d), AdNetwork
| order by Count desc
| project Num=row_number(), Day, AdNetwork, Count
```

**Ausgabe:**

| Nummer | Tag | Adnetwork | Anzahl |
|-----|-----|-----------|-------|
| 1 | 2020-01-01 00:00:00.0000000 | Neoads | 1002 |
| 2 | 2020-01-01 00:00:00.0000000 | Hoch Horizonte | 543 |
| 3 | 2020-01-01 00:00:00.0000000 | Pieads | 379 |
| ... | ... | ... | ... |

Abrufen der nächsten Seite:

<!-- csl -->
```kusto
stored_query_result("DailyClicksByAdNetwork7Days")
| where Num between(100 .. 200)
```

**Ausgabe:**

| Nummer | Tag | Adnetwork | Anzahl |
|-----|-----|-----------|-------|
| 100 | 2020-01-01 00:00:00.0000000 | Coolads | 301 |
| 101 | 2020-01-01 00:00:00.0000000 | DreamAds | 254 |
| 102 | 2020-01-02 00:00:00.0000000 | Superads | 123 |
| ... | ... | ... | ... |

## <a name="control-commands"></a>Steuerungsbefehle

### <a name="show-stored_query_results"></a>. anzeigen stored_query_results

Zeigt Informationen zu aktiven gespeicherten Abfrage Ergebnissen an.

>[!NOTE]
> * Benutzer mit `DatabaseAdmin` - `DatabaseMonitor` Berechtigung oder-Berechtigung können das vorhanden sein aktiver gespeicherter Abfrageergebnisse im Kontext der Datenbank überprüfen.
> * Benutzer mit- `DatabaseUser` oder- `DatabaseViewer` Berechtigungen können das vorhanden sein aktiver gespeicherter Abfrageergebnisse überprüfen, die von Ihrem Prinzipal erstellt wurden

#### <a name="syntax"></a>Syntax

`.show` `stored_query_results`

#### <a name="returns"></a>Rückgabe

| Storedqueryresultid | Name | DatabaseName | Principalidentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | Ereignisse | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_result"></a>. Drop stored_query_result

Löscht ein aktives Ergebnis der gespeicherten Abfrage, das in der aktuellen Datenbank vom aktuellen Prinzipal erstellt wurde.

#### <a name="syntax"></a>Syntax

`.drop``stored_query_result` *Storedqueryresultname*

`Database User` zum Aufrufen dieses Befehls ist eine Berechtigung erforderlich.

#### <a name="returns"></a>Rückgabe

Gibt Informationen zu gelöschten gespeicherten Abfrage Ergebnissen zurück, z. b.:

| Storedqueryresultid | Name | DatabaseName | Principalidentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | Ereignisse | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_results"></a>. Drop stored_query_results

Löscht aktive gespeicherte Abfrageergebnisse, die in der aktuellen Datenbank vom angegebenen Prinzipal erstellt wurden.

`Database Admin` zum Aufrufen dieses Befehls ist eine Berechtigung erforderlich.

#### <a name="syntax"></a>Syntax

`.drop``stored_query_results` `created-by` *PrincipalName*

#### <a name="returns"></a>Rückgabe

Gibt Informationen zu gelöschten gespeicherten Abfrage Ergebnissen zurück.

Beispiel:

<!-- csl -->
```kusto
.drop stored_query_results by user 'aadapp=c28e9b80-2808-bed525fc0fbb'
```

| Storedqueryresultid | Name | DatabaseName | Principalidentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | Ereignisse | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |
| 571b339-ba7caac19b46 (in englischer Sprache) | Traces | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 5212 | 100.000 | 2020-10-07 14:31:01.8271231| 2020-10-08 14:31:01.8271231 |
