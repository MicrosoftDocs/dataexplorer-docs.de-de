---
title: 'HTTP-Antwort für die Abfrage/Verwaltung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die HTTP-Antwort für die Abfrage/Verwaltung in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: 2642ffc6b87afab785dc5f7ba962e1f659232cc2
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942266"
---
# <a name="querymanagement-http-response"></a>HTTP-Antwort (Abfrage/Verwaltung)

## <a name="response-status"></a>Antwortstatus

Die HTTP-Antwortstatus Zeile folgt den HTTP-Standardantwort Codes.
Code 200 gibt z. b. Erfolg an. 

Die folgenden Statuscodes werden derzeit verwendet, es kann jedoch ein beliebiger gültiger HTTP-Code zurückgegeben werden.

|Code|Subcode        |BESCHREIBUNG                                    |
|----|---------------|-----------------------------------------------|
|100 |Weiter       |Der Client kann die Anforderung weiterhin senden.       |
|200 |OK             |Die Anforderung wurde erfolgreich verarbeitet.       |
|400 |BadRequest     |Die Anforderung ist falsch formatiert und ist fehlgeschlagen (permanent).|
|401 |Nicht autorisiert   |Der Client muss sich zuerst authentifizieren.            |
|403 |Verboten      |Die Client Anforderung wurde verweigert.                      |
|404 |NotFound       |Die Anforderung verweist auf eine nicht vorhandene Entität.      |
|413 |Payloadtoolarge|Die Anforderungs Nutzlast hat Limits überschritten.               |
|429 |TooManyRequests|Die Anforderung wurde aufgrund einer Drosselung verweigert. |
|504 |Timeout        |Timeout bei der Anforderung.                         |
|520 |ServiceError   |Der Dienst hat beim Verarbeiten der Anforderung einen Fehler festgestellt.|

> [!NOTE]
> Der 200-Statuscode zeigt, dass die Anforderungs Verarbeitung erfolgreich gestartet wurde, und nicht, dass Sie erfolgreich abgeschlossen wurde.
> Fehler, die während der Anforderungs Verarbeitung auftreten, nachdem der 200-Statuscode zurückgegeben wurde, werden als "partielle Abfrage Fehler" bezeichnet. Wenn Sie gefunden werden, werden spezielle Indikatoren in den Antwortstream eingefügt, um den Client darauf aufmerksam zu machen, dass diese

## <a name="response-headers"></a>Antwortheader

Die folgenden benutzerdefinierten Header werden zurückgegeben.

|Benutzerdefinierter Header           |BESCHREIBUNG                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|Der eindeutige Anforderungs Bezeichner, der im Anforderungs Header desselben Namens gesendet wird, oder ein eindeutiger Bezeichner.     |
|`x-ms-activity-id`      |Eine global eindeutige Korrelations-ID für die Anforderung. Sie wird vom-Dienst erstellt.                    |

## <a name="response-body"></a>Antworttext

Wenn der Statuscode 200 lautet, ist der Antworttext ein JSON-Dokument, das die Ergebnisse der Abfrage oder des Steuerelement Befehls als Sequenz von rechteckigen Tabellen codiert.
Weitere Informationen siehe unten.

> [!NOTE]
> Die Sequenz der Tabellen wird vom SDK reflektiert. Wenn Sie z. b. die .NET Framework Kusto. Data-Bibliothek verwenden, wird die Sequenz der Tabellen zu den Ergebnissen in dem `System.Data.IDataReader` vom SDK zurückgegebenen Objekt.

Wenn der Statuscode einen 4xx-oder einen 5xx-Fehler angibt (außer 401), ist der Antworttext ein JSON-Dokument, das die Details des Fehlers codiert.
Weitere Informationen finden Sie unter [Microsoft-Rest-API-Richtlinien](https://github.com/microsoft/api-guidelines).

> [!NOTE]
> Wenn der- `Accept` Header nicht in der Anforderung enthalten ist, ist der Antworttext eines Fehlers nicht notwendigerweise ein JSON-Dokument.

## <a name="json-encoding-of-a-sequence-of-tables"></a>JSON-Codierung einer Sequenz von Tabellen

Die JSON-Codierung einer Sequenz von Tabellen ist ein einzelner JSON-Eigenschaften Behälter mit den folgenden Name/Wert-Paaren.

|Name  |Wert                              |
|------|-----------------------------------|
|Tabellen|Ein Array des Tabellen Eigenschaften Behälters.|

Der Tabellen Eigenschaften Behälter weist die folgenden Name/Wert-Paare auf.

|Name     |Wert                               |
|---------|------------------------------------|
|TableName|Eine Zeichenfolge, die die Tabelle bezeichnet. |
|Spalten  |Ein Array des Spalten Eigenschaften Behälters.|
|Zeilen     |Ein Array des Zeilen Arrays.          |

Der Spalten Eigenschaften Behälter weist die folgenden Name/Wert-Paare auf.

|Name      |Wert                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|Eine Zeichenfolge, die die Spalte identifiziert.                           |
|DataType  |Eine Zeichenfolge, die den ungefähren .NET-Typ der Spalte bereitstellt.|
|ColumnType|Eine Zeichenfolge, die den [skalaren Datentyp](../../query/scalar-data-types/index.md) der Spalte bereitstellt.|

Das Zeilen Array hat dieselbe Reihenfolge wie das entsprechende Spalten Array.
Das Zeilen Array verfügt auch über ein Element, das mit dem Wert der Zeile für die relevante Spalte übereinstimmt.
Skalare Datentypen, die nicht in JSON-Code dargestellt werden können, z `datetime` `timespan` . b. und, werden als JSON-Zeichen folgen dargestellt.

Das folgende Beispiel zeigt ein mögliches solches Objekt, wenn es eine einzelne Tabelle mit dem Namen enthält, `Table_0` die eine einzelne Spalte `Text` vom Typ `string` und eine einzelne Zeile enthält.

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

Ein weiteres Beispiel: 

:::image type="content" source="../images/rest-json-representation.png" alt-text="Screenshot der Strukturansicht einer JSON-Datei, die ein Array von Tabellen Objekten enthält.":::

## <a name="the-meaning-of-tables-in-the-response"></a>Die Bedeutung von Tabellen in der Antwort.

In den meisten Fällen geben Steuerungsbefehle ein Ergebnis mit einer einzelnen Tabelle zurück, die die vom Steuerelement Befehl generierten Informationen enthält. Der `.show databases` Befehl gibt z. b. eine einzelne Tabelle mit den Details aller zugänglichen Datenbanken im Cluster zurück.

Abfragen geben in der Regel mehrere Tabellen zurück.
Für jede [tabellarische Ausdrucks Anweisung](../../query/tabularexpressionstatements.md)werden eine oder mehrere Tabellen in der Reihenfolge generiert, die die von der Anweisung erzeugten Ergebnisse darstellt.

> [!NOTE]
> Es können mehrere solcher Tabellen vorhanden sein, weil [Batches](../../query/batches.md) und [Fork-Operatoren](../../query/forkoperator.md)vorhanden sind.

Es werden häufig drei Tabellen erstellt:
* Eine @ExtendedProperties Tabelle, die zusätzliche Werte bereitstellt, z. b. Anweisungen zur Client Visualisierung. Diese Werte werden beispielsweise generiert, um die Informationen im Rendering- [Operator](../../query/renderoperator.md)widerzuspiegeln) und den [Daten Bank Cursor](../../management/databasecursor.md).
  
  Diese Tabelle verfügt über eine einzelne Spalte vom Typ `string` , die JSON-ähnliche Werte enthält:

  |Wert|
  |-----|
  |{"Visualisierung": "PieChart",...}|
  |{"Cursor": "637239957206013576"}|

* Eine QueryStatus-Tabelle, die zusätzliche Informationen über die Ausführung der Abfrage selbst bereitstellt, z. b., wenn Sie erfolgreich abgeschlossen wurde oder nicht, und die Ressourcen, die von der Abfrage verbraucht wurden.

  Diese Tabelle weist die folgende Struktur auf:

  |Timestamp                  |severity|Schweregrad Name|StatusCode|StatusDescription            |Anzahl|RequestId|ActivityId|Subactivityid|Clientactivityid|
  |---------------------------|--------|------------|----------|-----------------------------|-----|---------|----------|-------------|----------------|
  |2020-05-02 06:09:12.7052077|4       |Info        | 0        | Abfrage wurde erfolgreich abgeschlossen.|1    |...      |...       |...          |...             |

  Schweregrade von 2 oder kleiner weisen auf einen Fehler hin.

* Eine Tabelle "TableOfContents", die zuletzt erstellt wird, und listet die anderen Tabellen in den Ergebnissen auf. 

  Ein Beispiel für diese Tabelle ist:

  |Ordinal|Typ            |Name               |Id                                  |Prettyname|
  |-------|----------------|-------------------|------------------------------------|----------|
  |0      | QueryResult    |Primaryresult      |db9520f9-0455-4cb5-b257-53068497605a||
  |1      | QueryProperties|@ExtendedProperties|908901l6-5319-4809-ae9e-009068c267c7||
  |2      | QueryStatus    |QueryStatus        |00000000-0000-0000-0000-000000000000||
