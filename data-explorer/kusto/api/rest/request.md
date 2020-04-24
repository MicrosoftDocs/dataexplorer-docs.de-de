---
title: Abfrage-/Verwaltungs-HTTP-Anforderung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Abfrage-/Verwaltungs-HTTP-Anforderung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 71fac7122ca51755beaa09ce3867143806e4f2b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502719"
---
# <a name="querymanagement-http-request"></a>HTTP-Anforderung (Abfrage/Verwaltung)

## <a name="request-verb-and-resource"></a>Anforderungsverb und Ressource

|Aktion    |HTTP-Verb|HTTP-Ressource   |
|----------|---------|----------------|
|Abfrage     |GET      |`/v1/rest/query`|
|Abfrage     |POST     |`/v1/rest/query`|
|Abfrage v2  |GET      |`/v2/rest/query`|
|Abfrage v2  |POST     |`/v2/rest/query`|
|Verwaltung|POST     |`/v1/rest/mgmt` |

Um beispielsweise einen Steuerbefehl ("Verwaltung") an einen Dienstendpunkt zu senden, verwenden Sie die folgende Anforderungszeile:

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

(Siehe unten für die Anforderungsheader und den zu berücksichtigenden Text.)

## <a name="request-headers"></a>Anforderungsheader

Die folgende Tabelle enthält die allgemeinen Header, die zum Ausführen von Abfrage- und Verwaltungsvorgängen verwendet werden.

|Standard-Header  |Beschreibung                                                                                                                    |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------|
|`Accept`         |**Erforderlich**. Legen Sie diesen Eintrag auf `application/json`fest.                                                                                  |
|`Accept-Encoding`|**Optional:** Unterstützte Codierungen `gzip` sind `deflate`und .                                                                    |
|`Authorization`  |**Erforderlich**. Siehe [Authentifizierung](./authentication.md).                                                                       |
|`Connection`     |**Optional:** Es wird `Keep-Alive` empfohlen, aktiviert zu werden.                                                                  |
|`Content-Length` |**Optional:** Es wird empfohlen, die Länge des Anforderungstexts anzugeben, wenn bekannt ist.                                          |
|`Content-Type`   |**Erforderlich**. Legen Sie `application/json` `charset=utf-8`dies auf mit fest.                                                             |
|`Expect`         |**Optional:** Kann auf `100-Continue`gesetzt werden.                                                                                    |
|`Host`           |**Erforderlich**. Legen Sie diesen Wert auf den vollqualifizierten Domänennamen fest, an den die Anforderung gesendet wurde (z. `help.kusto.windows.net`B. ).|

Die folgende Tabelle enthält die allgemeinen benutzerdefinierten Header, die beim Ausführen von Abfrage- und Verwaltungsvorgängen verwendet werden. Sofern nicht anders angegeben, werden diese Header nur für Telemetriezwecke verwendet und haben keine Auswirkungen auf die Funktionalität.

Alle Header sind **optional**. Es wird **dringend empfohlen,** `x-ms-client-request-id` den benutzerdefinierten Header anzugeben. In einigen Szenarien (z. B. Abbrechen einer ausgeführten Abfrage) ist dieser Header **obligatorisch,** da er zum Identifizieren der Anforderung verwendet wird.


|BenutzerdefinierteKopfzeile           |Beschreibung                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Der (freundliche) Name der Anwendung, die den Antrag stellt.                                                |
|`x-ms-user`             |Der (freundliche) Name des Benutzers, der die Anforderung stellt.                                                       |
|`x-ms-user-id`          |Identisch mit `x-ms-user`.                                                                                      |
|`x-ms-client-request-id`|Ein eindeutiger Bezeichner für die Anforderung.                                                                      |
|`x-ms-client-version`   |Die (freundliche) Versionskennung für den Client, der die Anforderung stellt.                                      |
|`x-ms-readonly`         |Wenn angegeben, erzwingt die Anforderung, im schreibgeschützten Modus ausgeführt zu werden (verhindert, dass sie langanhaltende Änderungen vornimmt).|

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Parameter können in der Anforderung übergeben werden. Sie werden in der Anforderung als Abfrageparameter oder als Teil des Textkörpers kodiert, je nachdem, ob GET oder POST verwendet wird.

* `csl`: Dies ist ein **obligatorischer** Parameter. Es enthält den Text der Abfrage oder des Steuerbefehls, die ausgeführt werden sollen.

* `db`: Dies ist ein **optionaler** Parameter für einige Steuerbefehle und **ein obligatorischer** Parameter für andere Steuerbefehle und alle Abfragen. Es stellt den Namen der "Datenbank im Bereich" - die Datenbank, die das Ziel der Abfrage oder des Steuerbefehls ist.

* `properties`: Dies ist ein **optionaler** Parameter. Es stellt verschiedene Clientanforderungseigenschaften bereit, die ändern, wie die Anforderung verarbeitet wird, und deren Ergebnisse. Eine vollständige Beschreibung finden Sie unter [Clientanforderungseigenschaften](../netfx/request-properties.md).

## <a name="get-query-parameters"></a>GET-Abfrageparameter

Wenn GET verwendet wird, geben die Abfrageparameter der Anforderung die oben genannten Anforderungsparameter an.

## <a name="body"></a>Body

Bei verwendungsgemäßes POST ist der Text der Anforderung ein einzelnes JSON-Dokument, das in UTF-8 codiert ist und die Werte der oben genannten Anforderungsparameter bereitstellt.

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt die HTTP-POST-Anforderung für eine Abfrage:

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

Anforderungsheader:

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

Anforderungsstelle (neue Leitungen, die aus Gründen der Klarheit eingeführt wurden; sie werden nicht benötigt):

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

Das folgende Beispiel zeigt, wie Sie eine Anforderung erstellen, die die obige Abfrage mithilfe von [curl](https://curl.haxx.se/)sendet:

1. Rufen Sie ein Token für die Authentifizierung ab.

* ersetzen `AAD_TENANT_NAME_OR_ID` `AAD_APPLICATION_ID` und `AAD_APPLICATION_KEY` mit den relevanten Werten, nachdem Sie die [AAD-Anwendungsauthentifizierung](../../management/access-control/how-to-provision-aad-app.md) eingerichtet haben

```
curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
  -F "grant_type=client_credentials" \
  -F "resource=https://help.kusto.windows.net" \
  -F "client_id=AAD_APPLICATION_ID" \
  -F "client_secret=AAD_APPLICATION_KEY"
```

Dadurch erhalten Sie das Träger-Token:

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

2. Verwenden Sie das Trägertoken in Ihrer Anforderung an den Abfrageendpunkt:

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

3. Lesen Sie die Antwort gemäß [dieser Spezifikation](response.md).