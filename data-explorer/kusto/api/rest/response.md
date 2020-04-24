---
title: Abfrage-/Verwaltungs-HTTP-Antwort - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Abfrage-/Verwaltungs-HTTP-Antwort in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: 13937bbc9d17ed570c40926497ccf9b5c942fe72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503076"
---
# <a name="querymanagement-http-response"></a>HTTP-Antwort (Abfrage/Verwaltung)

## <a name="response-status"></a>Antwortstatus

Die HTTP-Antwortstatuszeile folgt den HTTP-Standardantwortcodes (z. B. 200 gibt Erfolg an). Die folgenden Statuscodes werden derzeit verwendet (beachten Sie jedoch, dass gültiger HTTP-Code zurückgegeben werden kann):

|Code|Untercode       |Beschreibung                                    |
|----|---------------|-----------------------------------------------|
|100 |Continue       |Der Client kann die Anforderung weiterhin senden.       |
|200 |OK             |Die Anforderung hat die Verarbeitung erfolgreich gestartet.       |
|400 |BadRequest     |Die Anforderung ist fehlerhaft und fehlgeschlagen (dauerhaft).|
|401 |Nicht autorisiert   |Der Client muss sich zuerst authentifizieren.            |
|403 |Verboten      |Die Clientanforderung wird abgelehnt.                      |
|404 |NotFound       |Anforderung verweist auf eine nicht vorhandene Entität.      |
|413 |NutzlastTooLarge|Anforderungsnutzlast überschritten Grenzen.               |
|429 |TooManyRequests|Die Anforderung wurde aufgrund der Drosselung abgelehnt.     |
|504 |Timeout        |Das Zeittimeout der Anforderung ist abgelaufen.                         |
|520 |ServiceError   |Beim Dienst ist ein Fehler bei der Verarbeitung der Anforderung aufgetreten.|

> [!NOTE]
> Es ist wichtig zu erkennen, dass der Statuscode 200 darstellt, dass die Anforderungsverarbeitung erfolgreich gestartet wurde, und nicht, dass sie erfolgreich abgeschlossen wurde. Fehler, die während der Anforderungsverarbeitung aufgetreten sind, aber nach der Rückgabe von 200 als "partielle Abfragefehler" bezeichnet wurden, und wenn sie aufgefunden werden, werden spezielle Indikatoren in den Antwortstream eingefügt, um den Client darüber zu informieren, dass sie aufgetreten sind.

## <a name="response-headers"></a>Antwortheader

Die folgenden benutzerdefinierten Header werden zurückgegeben.

|BenutzerdefinierteKopfzeile           |Beschreibung                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|Der eindeutige Anforderungsbezeichner, der im Anforderungsheader desselben Namens oder eines eindeutigen Bezeichners gesendet wird.     |
|`x-ms-activity-id`      |Ein global eindeutiger Korrelationsbezeichner für die Anforderung (erstellt vom Dienst).                        |

## <a name="response-body"></a>Antworttext

Wenn der Statuscode 200 ist, ist der Antworttext ein JSON-Dokument, das die Ergebnisse des Abfrage- oder Steuerbefehls als Sequecne rechteckiger Tabellen kodiert.
Weitere Informationen siehe unten.

> [!NOTE]
> Diese Reihenfolge der Tabellen wird vom SDK widergespiegelt. Wenn Sie beispielsweise die .NET Framework Kusto.Data-Bibliothek verwenden, wird `System.Data.IDataReader` die Reihenfolge der Tabellen dann zu den Ergebnissen in dem vom SDK zurückgegebenen Objekt.

Wenn der Statuscode einen 4xx- oder einen 5xx-Fehler (außer 401) angibt, ist der Antworttext ein JSON-Dokument, das die Fehlerdetails kodiert und den [Microsoft REST-API-Richtlinien](https://github.com/microsoft/api-guidelines)entspricht.

> [!NOTE]
> Wenn `Accept` der Header nicht in der Anforderung enthalten ist, ist der Antworttext eines Fehlers nicht unbedingt ein JSON-Dokument.

## <a name="json-encoding-of-a-sequence-of-tables"></a>JSON-Codierung einer Tabellenfolge

Die JSON-Codierung einer Tabellenfolge ist ein einzelner JSON-Eigenschaftsbeutel mit den folgenden Namen/Wert-Paaren:

|name  |Wert                              |
|------|-----------------------------------|
|Tabellen|Ein Array des Table-Eigenschaftsbeutels.|

Der Tabellen-Eigenschaftsbeutel hat die folgenden Name/Wert-Paare:

|name     |Wert                               |
|---------|------------------------------------|
|TableName|Eine Zeichenfolge, die die Tabelle identifiziert. |
|Spalten  |Ein Array des Column-Eigenschaftsbeutels.|
|Zeilen     |Ein Array des Row-Arrays.          |

Der Column-Eigenschaftsbeutel hat die folgenden Name/Wert-Paare:

|name      |Wert                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|Eine Zeichenfolge, die die Spalte identifiziert.                           |
|DataType  |Eine Zeichenfolge, die den ungefähren .NET-Typ der Spalte bereitstellt.|
|ColumnType|Eine Zeichenfolge, die den [skalaren Datentyp](../../query/scalar-data-types/index.md) der Spalte bereitstellt.|

Das Row-Array hat die gleiche Reihenfolge wie das entsprechende Columns-Array und hat ein Element mit dem Wert der Zeile für die entsprechende Spalte.
Skalare Datentypen, die in JSON nicht `datetime
and `dargestellt werden können (z. B. Zeitspanne), werden als JSON-Zeichenfolgen dargestellt.

Das folgende Beispiel zeigt ein mögliches solches Objekt, wenn es eine einzelne Tabelle mit dem Namen `Table_0` enthält, die eine einzelne Spalte `Text` des Typs `string` und eine einzelne Zeile enthält.

```json
{
    "Tables": [{
        "TableName": "Table_0",
        "Columns": [{
            "ColumnName": "Text",
            "DataType": "String",
            "ColumnType": "string"
        }],
        "Rows": [["Hello, World!"]]
}
```

Ein weiterer Exmaple:

![JSON-Antwortdarstellung](../images/rest-json-representation.png "rest-json-repräsentation")

## <a name="the-meaning-of-tables-in-the-response"></a>Die Bedeutung von Tabellen in der Antwort

In den meisten Fällen geben Steuerbefehle ein Ergebnis mit einer einzelnen Tabelle zurück, in der die vom Befehl "Steuerelement" generierten Informationen enthalten sind. Der `.show databases` Befehl gibt z. B. eine einzelne Tabelle mit den Details aller zugriffsbaren Datenbanken im Cluster zurück.

Abfragen hingegen geben in der Regel mehrere Tabellen zurück. Für jede [tabellenförmige Ausdrucksanweisung](../../query/tabularexpressionstatements.md)werden eine oder mehrere Tabellen in der Reihenfolge emittiert, die die von der Anweisung erzeugten Ergebnisse darstellen (aufgrund von [Batches](../../query/batches.md) und [Gabeloperatoren](../../query/forkoperator.md)können mehrere solcher Tabellen vorhanden sein).

Darüber hinaus werden in der Regel drei Tische hergestellt:

* Eine @ExtendedProperties Tabelle, die zusätzliche Werte wie Clientvisualisierungsanweisungen (z. B. zur Wiedergabe der Informationen im [Renderoperator)](../../query/renderoperator.md)und [Datenbankcursorinformationen](../../management/databasecursor.md) enthält.
* Eine QueryStatus-Tabelle, die zusätzliche Informationen zur Ausführung der Abfrage selbst bereitstellt, z. B. ob sie erfolgreich abgeschlossen wurde oder nicht, und welche Ressourcen von der Abfrage verbraucht wurden.
* Eine Tabelle TableOfContents, die zuletzt angezeigt wird und die anderen Tabellen in den Ergebnissen auflistet.

