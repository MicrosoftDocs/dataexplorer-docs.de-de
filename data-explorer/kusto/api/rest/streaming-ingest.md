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
ms.openlocfilehash: 87b68e4a9de42a9a7085238db5919066d577ed1f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373540"
---
# <a name="streaming-ingestion-http-request"></a>Streamingerfassung (http-Anforderung)

## <a name="request-verb-and-resource"></a>Anforderungs Verb und Ressource

|Aktion    |HTTP-Verb|HTTP-Ressource                                               |
|----------|---------|------------------------------------------------------------|
|Erfassen    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>Anforderungsparameter

| Parameter    | Beschreibung                                                                 | Erforderlich/Optional |
|--------------|-----------------------------------------------------------------------------|-------------------|
| `{database}` |   Name der Zieldatenbank für die Erfassungs Anforderung                     |  Erforderlich         |
| `{table}`    |   Name der Ziel Tabelle für die Erfassungs Anforderung                        |  Erforderlich         |

## <a name="additional-parameters"></a>Zusätzliche Parameter

Zusätzliche Parameter werden als URL-Abfrage `{name}={value}` Paare formatiert, getrennt durch das & Zeichen.

| Parameter    | Beschreibung                                                                          | Erforderlich/Optional   |
|--------------|--------------------------------------------------------------------------------------|---------------------|
|`streamFormat`| Gibt das Format der Daten im Anforderungs Text an. Der Wert muss einer der folgenden Werte sein: `CSV` , `TSV` , `SCsv` , `SOHsv` , `PSV` , `JSON` , `MultiJSON` , `Avro` . Weitere Informationen finden Sie [unter Unterstützte Datenformate](../../../ingestion-supported-formats.md).| Erforderlich |
|`mappingName` | Der Name der vorab erstellten Erfassungs Zuordnung, die für die Tabelle definiert ist. Weitere Informationen finden Sie unter [Daten](../../management/mappings.md)Zuordnungen. Die Vorgehensweise zum Verwalten von vorab erstellten Zuordnungen in der Tabelle wird [hier](../../management/create-ingestion-mapping-command.md)beschrieben.| Optional, aber erforderlich, wenn `streamFormat` eine von `JSON` , `MultiJSON` oder ist.`Avro`|  |
              
Verwenden Sie zum Erfassen von CSV-formatierten Daten in eine Tabelle in der-Datenbank beispielsweise Folgendes `Logs` `Test` :

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

Verwenden Sie Folgendes, um JSON-formatierte Daten mit einer vorab erstellten Zuordnung zu erfassen `mylogmapping` :

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

## <a name="request-headers"></a>Anforderungsheader

In der folgenden Tabelle sind die allgemeinen Header für Abfrage-und Verwaltungsvorgänge enthalten.

|Standard Header   | Beschreibung                                                                               | Erforderlich/Optional | 
|------------------|-------------------------------------------------------------------------------------------|-------------------|
|`Accept`          | Legen Sie diesen Wert auf fest `application/json` .                                                     | Optional          |
|`Accept-Encoding` | Unterstützte Codierungen sind `gzip` und `deflate` .                                             | Optional          | 
|`Authorization`   | Siehe [Authentifizierung](./authentication.md).                                                | Erforderlich          |
|`Connection`      | `Keep-Alive`aktivieren.                                                                      | Optional          |
|`Content-Length`  | Gibt die Länge des Anforderungs Texts an, sofern bekannt.                                              | Optional          |
|`Content-Encoding`| Legen Sie auf fest, `gzip` aber der Text muss gzip-komprimiert sein.                                        | Optional          |
|`Expect`          | Legen Sie diese Option auf `100-Continue` fest.                                                                    | Optional          |
|`Host`            | Legen Sie auf den Domänen Namen fest, an den die Anforderung gesendet wurde (z `help.kusto.windows.net` . b.). | Erforderlich          |

In der folgenden Tabelle sind die allgemeinen benutzerdefinierten Header für Abfrage-und Verwaltungsvorgänge enthalten. Sofern nicht anders angegeben, dienen die Header nur zu telemetriezwecken und haben keine Auswirkungen auf die Funktionalität.

|Benutzerdefinierter Header           |Beschreibung                                                                           | Erforderlich/Optional |
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
