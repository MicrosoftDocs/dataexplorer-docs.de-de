---
title: 'Abfrage-/Verwaltung-http-Anforderung: Azure Daten-Explorer'
description: In diesem Artikel wird die HTTP-Anforderung Abfrage/Verwaltung in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: cf9f9e5f6a9c5afca58e2637ed4e639882e3749d
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337413"
---
# <a name="querymanagement-http-request"></a>HTTP-Anforderung (Abfrage/Verwaltung)

## <a name="request-verb-and-resource"></a>Anforderungs Verb und Ressource

|Aktion    |HTTP-Verb|HTTP-Ressource   |
|----------|---------|----------------|
|Abfrage     |GET      |`/v1/rest/query`|
|Abfrage     |POST     |`/v1/rest/query`|
|Abfrage v2  |GET      |`/v2/rest/query`|
|Abfrage v2  |POST     |`/v2/rest/query`|
|Verwaltung|POST     |`/v1/rest/mgmt` |

Um z. b. einen Steuerungs Befehl ("Verwaltung") an einen Dienst Endpunkt zu senden, verwenden Sie die folgende Anforderungs Zeile:

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

Nachstehend finden Sie die einzuschließenden Anforderungs Header und Text.

## <a name="request-headers"></a>Anforderungsheader

Die folgende Tabelle enthält die allgemeinen Header, die für Abfrage-und Verwaltungsvorgänge verwendet werden.

|Standard Header  |BESCHREIBUNG                                                                                 |Erforderlich/Optional |
|-----------------|--------------------------------------------------------------------------------------------|------------------|
|`Accept`         |Legen Sie den Wert `application/json`                                                                   |Erforderlich          |
|`Accept-Encoding`|Unterstützte Codierungen sind `gzip` und. `deflate`                                                |Optional          |
|`Authorization`  |Siehe [Authentifizierung](./authentication.md)                                                   |Erforderlich          |
|`Connection`     |Es wird empfohlen, Folgendes zu aktivieren: `Keep-Alive`                                                   |Optional          |
|`Content-Length` |Es wird empfohlen, die Länge des Anforderungs Texts anzugeben, wenn bekannt                            |Optional          |
|`Content-Type`   |Legen Sie auf fest. `application/json``charset=utf-8`                                              |Erforderlich          |
|`Expect`         |Kann auf festgelegt werden. `100-Continue`                                                                |Optional          |
|`Host`           |Legen Sie auf den qualifizierten Domänen Namen fest, an den die Anforderung gesendet wurde (z. b. `help.kusto.windows.net` ). |Erforderlich|

Die folgende Tabelle enthält die allgemeinen benutzerdefinierten Header, die für Abfrage-und Verwaltungsvorgänge verwendet werden. Sofern nicht anders angegeben, werden diese Header nur für Telemetriezwecke verwendet und haben keine Auswirkungen auf die Funktionalität.

Alle Header sind optional. Es wird empfohlen, dass Sie den `x-ms-client-request-id` benutzerdefinierten Header angeben. In einigen Szenarien, z. b. beim Abbrechen einer laufenden Abfrage, ist dieser Header erforderlich, da er zur Identifizierung der Anforderung verwendet wird.

|Benutzerdefinierter Header           |BESCHREIBUNG                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Der (freundliche) Name der Anwendung, die die Anforderung sendet.                                                 |
|`x-ms-user`             |Der (benutzerfreundliche) Name des Benutzers, der die Anforderung sendet.                                                        |
|`x-ms-user-id`          |Identisch mit `x-ms-user`                                                                                       |
|`x-ms-client-request-id`|Ein eindeutiger Bezeichner für die Anforderung.                                                                       |
|`x-ms-client-version`   |Der (benutzerfreundliche) Versions Bezeichner für den Client, der die Anforderung sendet.                                       |
|`x-ms-readonly`         |Wenn angegeben, erzwingt, dass die Anforderung im schreibgeschützten Modus ausgeführt wird, sodass lange dauerhafte Änderungen verhindert werden. |

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Parameter können in der Anforderung übermittelt werden. Sie werden in der Anforderung als Abfrage Parameter oder als Teil des Texts codiert, je nachdem, ob Get oder Post verwendet wird.

|Parameter   |BESCHREIBUNG                                                                                 |Erforderlich/Optional |
|------------|--------------------------------------------------------------------------------------------|------------------|
|`csl`       |Text des auszuführenden Abfrage-oder Steuerelement Befehls                                             |Erforderlich          |
|`db`        |Der Name der Datenbank im Bereich, der das Ziel des Abfrage-oder Steuerungs Befehls ist.            |Optional für einige Steuerbefehle. <br>Erforderlich für andere Befehle und alle Abfragen. </br>                                                                   |
|`properties`|Stellt Client Anforderungs Eigenschaften bereit, die die Verarbeitung der Anforderung und ihre Ergebnisse ändern. Weitere Informationen finden Sie unter [Eigenschaften von Client Anforderungen](../netfx/request-properties.md) .                                               | Optional         |

## <a name="get-query-parameters"></a>Abfrage Parameterabfragen

Wenn Get verwendet wird, geben die Abfrage Parameter der Anforderung die Anforderungs Parameter an.

## <a name="body"></a>Text

Wenn Post verwendet wird, ist der Anforderungs Text ein einzelnes JSON-Dokument, das in UTF-8 codiert ist, mit den Werten der Anforderungs Parameter.

## <a name="examples"></a>Beispiele

Dieses Beispiel zeigt die HTTP POST-Anforderung für eine Abfrage.

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

Anforderungsheader

```txt
Accept: application/json
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Content-Type: application/json; charset=utf-8
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1
x-ms-user-id: EARTH\davidbg
x-ms-app: MyApp
```

Anforderungstext

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

In diesem Beispiel wird gezeigt, wie eine Anforderung erstellt wird, die die obige Abfrage mithilfe von [curl](https://curl.haxx.se/)sendet.

1. Rufen Sie ein Token für die Authentifizierung ab.

    Ersetzen `AAD_TENANT_NAME_OR_ID` `AAD_APPLICATION_ID` Sie, und `AAD_APPLICATION_KEY` durch die relevanten Werte nach dem Einrichten der [Aad-Anwendungs Authentifizierung](../../../provision-azure-ad-app.md) .

    ```
    curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
      -F "grant_type=client_credentials" \
      -F "resource=https://help.kusto.windows.net" \
      -F "client_id=AAD_APPLICATION_ID" \
      -F "client_secret=AAD_APPLICATION_KEY"
    ```

    Mit diesem Code Ausschnitt wird das bearertoken bereitgestellt.

    ```
    {
      "token_type": "Bearer",
      "expires_in": "3599",
      "ext_expires_in":"3599", 
      "expires_on":"1578439805",
      "not_before":"1578435905",
      "resource":"https://help.kusto.windows.net",
      "access_token":"eyJ0...uXOQ"
    }
    ```

1. Verwenden Sie das bearertoken in der Anforderung an den Abfrage Endpunkt.

    ```
    curl -d '{"db":"Samples","csl":"print Test=\"Hello, World!\"","properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"}}"}"' \
    -H "Accept: application/json" \
    -H "Authorization: Bearer eyJ0...uXOQ" \
    -H "Content-Type: application/json; charset=utf-8" \
    -H "Host: help.kusto.windows.net" \
    -H "x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1" \
    -H "x-ms-user-id: EARTH\davidbg" \
    -H "x-ms-app: MyApp" \
    -X POST https://help.kusto.windows.net/v2/rest/query
    ```

1. Lesen Sie die Antwort entsprechend [dieser Spezifikation](response.md).