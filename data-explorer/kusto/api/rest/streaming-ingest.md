---
title: HTTP-Anforderung für die Streamingerfassung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die HTTP-Anforderung für die Streamingerfassung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d14987806bbc62dbc79112700bd5b88aaa6676c3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503008"
---
# <a name="streaming-ingestion-http-request"></a>Streaming-Aufnahme HTTP-Anforderung

## <a name="request-verb-and-resource"></a>Anforderungsverb und Ressource

|Aktion    |HTTP-Verb|HTTP-Ressource                                               |
|----------|---------|------------------------------------------------------------|
|Erfassen    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>Anforderungsparameter

| Parameter    |  Beschreibung                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
| `{database}` | **Erforderlich** Name der Zieldatenbank für die Aufnahmeanforderung                                          |
| `{table}`    | **Erforderlich** Name der Zieltabelle für die Aufnahmeanforderung                                             |

## <a name="additional-parameters"></a>Zusätzliche Parameter
Zusätzliche Paramter werden als URL-Abfrage formatiert: `{name}` = `{value}` Paare, die nach & Zeichen getrennt werden


| Parameter    |  Beschreibung                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
|`streamFormat`| **Erforderlich** Gibt das Format der Daten im Anforderungstext an. Der Wert sollte einer `Csv`der`Tsv``Scsv`folgenden`SOHsv``Psv`Werte`Json``SingleJson`sein: , , , , , , ,`MultiJson`, , .`Avro` Weitere Informationen finden Sie unter [Unterstützte Datenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).|
|`mappingName` | **Erforderlich,** `streamFormat` wenn `Json`es `SingleJson` `MultiJson` sich `Avro`um eine von , oder , **optional** auf andere Weise handelt. Der Wert sollte der Name der vorerstellten Aufnahmezuordnung sein, die in der Tabelle definiert ist. Weitere Informationen zu Datenzuordnungen finden Sie unter [Datenzuordnungen](../../management/mappings.md). Die Art und Weise zum Verwalten vordefinierter Zuordnungen in der Tabelle wird [hier](../../management/create-ingestion-mapping-command.md) beschrieben. |
              

Um z. B. CSV-formatierte `Logs` Daten `Test` in eine Tabelle in der Datenbank aufzunehmen, verwenden Sie die folgende Anforderungszeile:

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

, um JSON-formatierte Daten mit vorerstellter Zuordnung zu erfassen`mylogmapping`

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```


(Siehe unten für die Anforderungsheader und den zu berücksichtigenden Text.)

## <a name="request-headers"></a>Anforderungsheader

Die folgende Tabelle enthält die allgemeinen Header, die zum Ausführen von Abfrage- und Verwaltungsvorgängen verwendet werden.

|Standard-Header  |Beschreibung                                                                                                              |
|------------------|------------------------------------------------------------------------------------------------------------------------|
|`Accept`          |**Optional:** Legen Sie diesen Eintrag auf `application/json`fest.                                                                           |
|`Accept-Encoding` |**Optional:** Unterstützte Codierungen `gzip` sind `deflate`und .                                                             |
|`Authorization`   |**Erforderlich**. Siehe [Authentifizierung](./authentication.md).                                                                |
|`Connection`      |**Optional:** Es wird `Keep-Alive` empfohlen, aktiviert zu werden.                                                           |
|`Content-Length`  |**Optional:** Es wird empfohlen, die Länge des Anforderungstexts anzugeben, wenn bekannt ist.                                   |
|`Content-Encoding`|**Optional:** Kann auf `gzip` das eingestellt werden, in welchem Fall der Körper gzip-komprimiert werden muss                                 |
|`Expect`          |**Optional:** Kann auf `100-Continue`gesetzt werden.                                                                             |
|`Host`            |**Erforderlich**. Legen Sie diesen wert auf den vollqualifizierten Domänennamen, an `help.kusto.windows.net`den die Anforderung gesendet wurde (z. B. ).|

Die folgende Tabelle enthält die allgemeinen benutzerdefinierten Header, die beim Ausführen von Abfrage- und Verwaltungsvorgängen verwendet werden. Sofern nicht anders angegeben, werden diese Header nur für Telemetriezwecke verwendet und haben keine Auswirkungen auf die Funktionalität.

Alle Header sind **optional**. Es wird jedoch dringend `x-ms-client-request-id` **empfohlen,** den benutzerdefinierten Header anzugeben. In einigen Szenarien (z. B. Abbrechen einer ausgeführten Abfrage) ist dieser Header **obligatorisch,** da er zum Identifizieren der Anforderung verwendet wird.


|BenutzerdefinierteKopfzeile           |Beschreibung                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Der (freundliche) Name der Anwendung, die den Antrag stellt.                                                |
|`x-ms-user`             |Der (freundliche) Name des Benutzers, der die Anforderung stellt.                                                       |
|`x-ms-user-id`          |Identisch mit `x-ms-user`.                                                                                      |
|`x-ms-client-request-id`|Ein eindeutiger Bezeichner für die Anforderung.                                                                      |
|`x-ms-client-version`   |Die (freundliche) Versionskennung für den Client, der die Anforderung stellt.                                      |

## <a name="body"></a>Body

Der Text ist die tatsächliche Daten aufgenommen werden. Die Textformate verwenden UTF-8-Codierung.

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die HTTP-POST-Anforderung für einen übertragenden JSON-Inhalt:

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

Anforderungsheader:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 161
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

Anforderungstext:

```txt
{"Timestamp":"2018-11-14 11:34","Level":"Info","EventText":"Nothing Happened"}
{"Timestamp":"2018-11-14 11:35","Level":"Error","EventText":"Something Happened"}
```

Das folgende Beispiel zeigt die HTTP POST-Anforderung zum Einsenden derselben komprimierten Daten

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

Anforderungsheader:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 116
Content-Encoding: gzip
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

Anforderungstext:

```
... binary data ...
```