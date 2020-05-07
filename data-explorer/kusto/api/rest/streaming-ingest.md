---
title: Streamingerfassung http-Anforderung-Azure-Daten-Explorer
description: In diesem Artikel wird die HTTP-Anforderung zum Streamen von Daten in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 672f924865cab14dff6c7d5319c3c34cca1a67ee
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862008"
---
# <a name="streaming-ingestion-http-request"></a>Streamingerfassung (http-Anforderung)

## <a name="request-verb-and-resource"></a>Anforderungs Verb und Ressource

|Aktion    |HTTP-Verb|HTTP-Ressource                                               |
|----------|---------|------------------------------------------------------------|
|Erfassen    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>Anforderungsparameter

| Parameter    | BESCHREIBUNG                                                                 | Erforderlich/optional |
|--------------|-----------------------------------------------------------------------------|-------------------|
| `{database}` |   Name der Zieldatenbank für die Erfassungs Anforderung                     |  Erforderlich         |
| `{table}`    |   Name der Ziel Tabelle für die Erfassungs Anforderung                        |  Erforderlich         |

## <a name="additional-parameters"></a>Zusätzliche Parameter

Zusätzliche Parameter werden als URL-Abfrage `{name}={value}` Paare formatiert, getrennt durch das & Zeichen.

| Parameter    | BESCHREIBUNG                                                                          | Erforderlich/optional   |
|--------------|--------------------------------------------------------------------------------------|---------------------|
|`streamFormat`| Gibt das Format der Daten im Anforderungs Text an. Der Wert muss einer der folgenden Werte `CSV`sein`TSV`:`SCsv`,`SOHsv`,`PSV`,`JSON`,`SingleJSON`,`MultiJSON`,`Avro`,,. Weitere Informationen finden Sie [unter Unterstützte Datenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).| Erforderlich |
|`mappingName` | Der Name der vorab erstellten Erfassungs Zuordnung, die für die Tabelle definiert ist. Weitere Informationen finden Sie unter [Daten](../../management/mappings.md)Zuordnungen. Die Vorgehensweise zum Verwalten von vorab erstellten Zuordnungen in der Tabelle wird [hier](../../management/create-ingestion-mapping-command.md)beschrieben.| Optional, aber erforderlich, `streamFormat` wenn eine von `JSON`,`SingleJSON`,`MultiJSON`oder ist.`Avro`|  |
              
Verwenden Sie zum Erfassen von CSV-formatierten Daten in eine `Logs` Tabelle in `Test`der-Datenbank beispielsweise Folgendes:

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

Verwenden Sie Folgendes, um JSON-formatierte Daten mit einer `mylogmapping`vorab erstellten Zuordnung zu erfassen:

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

## <a name="request-headers"></a>Anforderungsheader

In der folgenden Tabelle sind die allgemeinen Header für Abfrage-und Verwaltungsvorgänge enthalten.

|Standard Header   | BESCHREIBUNG                                                                               | Erforderlich/optional | 
|------------------|-------------------------------------------------------------------------------------------|-------------------|
|`Accept`          | Legen Sie diesen Wert `application/json`auf fest.                                                     | Optional          |
|`Accept-Encoding` | Unterstützte Codierungen `gzip` sind `deflate`und.                                             | Optional          | 
|`Authorization`   | Siehe [Authentifizierung](./authentication.md).                                                | Erforderlich          |
|`Connection`      | `Keep-Alive`aktivieren.                                                                      | Optional          |
|`Content-Length`  | Gibt die Länge des Anforderungs Texts an, sofern bekannt.                                              | Optional          |
|`Content-Encoding`| Legen Sie `gzip` auf fest, aber der Text muss gzip-komprimiert sein.                                        | Optional          |
|`Expect`          | Legen Sie diese Option auf `100-Continue` fest.                                                                    | Optional          |
|`Host`            | Legen Sie auf den Domänen Namen fest, an den die Anforderung gesendet wurde `help.kusto.windows.net`(z. b.). | Erforderlich          |

In der folgenden Tabelle sind die allgemeinen benutzerdefinierten Header für Abfrage-und Verwaltungsvorgänge enthalten. Sofern nicht anders angegeben, dienen die Header nur zu telemetriezwecken und haben keine Auswirkungen auf die Funktionalität.

|Benutzerdefinierter Header           |BESCHREIBUNG                                                                           | Erforderlich/optional |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Der (benutzerfreundliche) Name der Anwendung, die die Anforderung sendet.                            | Optional          |
|`x-ms-user`             |Der (benutzerfreundliche) Name des Benutzers, der die Anforderung sendet.                                   | Optional          |
|`x-ms-user-id`          |Wie in `x-ms-user`.                                                                  | Optional          |
|`x-ms-client-request-id`|Ein eindeutiger Bezeichner für die Anforderung.                                                  | Optional          |
|`x-ms-client-version`   |Der (benutzerfreundliche) Versions Bezeichner für den Client, der die Anforderung sendet. Erforderlich in Szenarien, in denen es zum Identifizieren der Anforderung verwendet wird, z. b. zum Abbrechen einer laufenden Abfrage.                                                        | Optional/erforderlich  |

## <a name="body"></a>Body

Der Text ist die tatsächlich erfassten Daten. Die Textformate sollten UTF-8-Codierung verwenden.

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die HTTP POST-Anforderung zum Erfassen von JSON-Inhalten:

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

Das folgende Beispiel zeigt die HTTP POST-Anforderung zum Erfassen derselben komprimierten Daten.

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
