---
title: 'Kusto authentifizieren Sie sich mit Aad für den Zugriff: Azure-Daten-Explorer'
description: In diesem Artikel wird How-To Authentifizierung mit Aad für Azure Daten-Explorer Access in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref, devx-track-js
ms.date: 09/13/2019
ms.openlocfilehash: 65e15fca7c7a69e1c9ba2d79f7dca531304ea91b
ms.sourcegitcommit: 8a7165b28ac6b40722186300c26002fb132e6e4a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92749483"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>How-To Authentifizieren mit Aad für Azure Daten-Explorer Access

Die empfohlene Vorgehensweise für den Zugriff auf Azure Daten-Explorer ist die Authentifizierung beim **Azure Active Directory** -Dienst (manchmal auch als **Azure AD** oder einfach als **Aad** bezeichnet). Dadurch wird sichergestellt, dass Azure Daten-Explorer die Verzeichnis Anmelde Informationen des Zugriffs Prinzipals nicht mit einem zweistufigen Prozess ansieht:

1. Im ersten Schritt kommuniziert der Client mit dem AAD-Dienst, authentifiziert ihn und fordert ein Zugriffs Token an, das speziell für den jeweiligen Azure-Daten-Explorer Endpunkt ausgestellt wird, auf den der Client zugreifen möchte.
2. Im zweiten Schritt gibt der Client Anforderungen an Azure Daten-Explorer aus und stellt das Zugriffs Token bereit, das im ersten Schritt als Identitätsnachweis für Azure Daten-Explorer eingerichtet wurde.

Azure Daten-Explorer führt dann die Anforderung im Namen des Sicherheits Prinzipals aus, für den Aad das Zugriffs Token ausgestellt hat, und alle Autorisierungs Überprüfungen werden mit dieser Identität ausgeführt.

In den meisten Fällen empfiehlt es sich, einen der Azure-Daten-Explorer sdche für den programmgesteuerten Zugriff auf den Dienst zu verwenden, da dadurch ein Großteil des Aufwands für die Implementierung des obigen Flows entfernt wird (und vieles mehr). Siehe z. b. das [.NET SDK](../../api/netfx/about-the-sdk.md).
Die Authentifizierungs Eigenschaften werden dann von der [Kusto-Verbindungs Zeichenfolge](../../api/connection-strings/kusto.md)festgelegt.
Wenn dies nicht möglich ist, lesen Sie weiter, um ausführliche Informationen zur Implementierung dieses Flows selbst zu erhalten.

Die wichtigsten Authentifizierungs Szenarien sind:

* **Eine Client Anwendung, die einen angemeldeten Benutzer authentifiziert** .
  In diesem Szenario löst eine interaktive (Client-) Anwendung eine Aad-Eingabeaufforderung für Anmelde Informationen (z. b. Benutzername und Kennwort) für den Benutzer aus.
  Siehe [Benutzerauthentifizierung](#user-authentication),

* **Eine "Start lose" Anwendung** .
  In diesem Szenario wird eine Anwendung ausgeführt, ohne dass ein Benutzer vorhanden ist, um Anmelde Informationen bereitzustellen, und die Anwendung authentifiziert sich mit einigen Anmelde Informationen, mit denen Sie konfiguriert wurde, als "selbst" in Aad.
  Siehe [Anwendungs Authentifizierung](#application-authentication).

* **Im-Auftrag-der-Authentifizierung** .
  In diesem Szenario, das manchmal als "Webdienst" oder "Web-App"-Szenario bezeichnet wird, erhält die Anwendung ein Aad-Zugriffs Token aus einer anderen Anwendung und konvertiert es dann in ein anderes Aad-Zugriffs Token, das mit Azure Daten-Explorer verwendet werden kann.
  Anders ausgedrückt: die Anwendung fungiert als Vermittler zwischen dem Benutzer oder der Anwendung, der Anmelde Informationen bereitgestellt hat, und dem Azure Daten-Explorer-Dienst.
  Siehe [im Auftrag der Authentifizierung](#on-behalf-of-authentication).

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Angeben der Aad-Ressource für Azure Daten-Explorer

Wenn Sie ein Zugriffs Token aus Aad abrufen, muss der Client Aad mitteilen, für welche **Aad-Ressource** das Token ausgestellt werden soll. Die Aad-Ressource eines Azure Daten-Explorer-Endpunkts ist der URI des Endpunkts, der die Port Informationen und den Pfad ausweist. Beispiel:

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>Angeben der Aad-Mandanten-ID

Aad ist ein mehr Instanzen fähiger Dienst, und jede Organisation kann ein Objekt mit dem Namen " **Directory** " in Aad erstellen. Das Verzeichnis Objekt enthält sicherheitsbezogene Objekte, wie z. b. Benutzerkonten, Anwendungen und Gruppen. Aad bezieht sich häufig auf das **Verzeichnis als Mandant.** Aad-Mandanten werden anhand einer GUID (Mandanten- **ID** ) identifiziert. In vielen Fällen können Aad-Mandanten auch durch den Domänen Namen der Organisation identifiziert werden.

Beispielsweise kann eine Organisation mit dem Namen "kontoso" die Mandanten `4da81d62-e0a8-4899-adad-4349ca6bfe24` -ID und den Domänen Namen aufweisen `contoso.com` .

## <a name="specifying-the-aad-authority"></a>Angeben der Aad-Autorität

Aad verfügt über eine Reihe von Endpunkten für die Authentifizierung:

* Wenn der Mandant, der als Host für den authentifizierten Prinzipal dient, bekannt ist (d. h., wenn er weiß, welches Aad-Verzeichnis der Benutzer oder die Anwendung ist), ist der Aad-Endpunkt `https://login.microsoftonline.com/{tenantId}` .
  Hier `{tenantId}` ist entweder die Mandanten-ID der Organisation in Aad oder der zugehörige Domänen Name (z. b. `contoso.com` ).

* Wenn der Mandant, der den Prinzipal, der authentifiziert wird, nicht bekannt ist, kann der "Common"-Endpunkt verwendet werden, indem der `{tenantId}` obige durch den Wert ersetzt wird `common` .

> [!NOTE]
> Der für die Authentifizierung verwendete Aad-Endpunkt wird auch als **Aad-Autoritäts-URL** oder einfach als **Aad-Autorität** bezeichnet.

## <a name="aad-token-cache"></a>Aad-Tokencache

Wenn Sie das Azure Daten-Explorer SDK verwenden, werden die Aad-Token auf dem lokalen Computer in einem Tokencache pro Benutzer gespeichert (eine Datei namens " **%AppData%\kusto\tokencache.Data** ", auf die nur der angemeldete Benutzer zugreifen oder diese entschlüsseln kann). Der Cache wird auf Token überprüft, bevor der Benutzer zur Eingabe von Anmelde Informationen aufgefordert wird. Dadurch wird die Häufigkeit, mit der ein Benutzer Anmelde Informationen eingeben muss, erheblich verringert.

> [!NOTE]
> Der Aad-Tokencache reduziert die Anzahl der interaktiven Eingabe Aufforderungen, die einem Benutzer für den Zugriff auf Azure-Daten-Explorer angezeigt werden, aber er wird nicht beendet. Außerdem können Benutzer nicht im Voraus vorhersehen, wann Sie zur Eingabe von Anmelde Informationen aufgefordert werden.
> Dies bedeutet, dass Sie nicht versuchen dürfen, ein Benutzerkonto für den Zugriff auf Azure Daten-Explorer zu verwenden, wenn es erforderlich ist, nicht interaktive Anmeldungen zu unterstützen (z. b. beim Planen von Aufgaben), denn wenn der angemeldete Benutzer nach Anmelde Informationen aufgefordert werden soll, tritt bei der Ausführung unter nicht interaktiver Anmeldung ein Fehler auf.


## <a name="user-authentication"></a>Benutzerauthentifizierung

Die einfachste Möglichkeit, auf Azure-Daten-Explorer mit Benutzerauthentifizierung zuzugreifen, ist die Verwendung des Azure Daten-Explorer SDK und die Festlegung der- `Federated Authentication` Eigenschaft der Azure Daten-Explorer-Verbindungs Zeichenfolge auf `true` . Wenn das SDK zum ersten Mal verwendet wird, um eine Anforderung an den Dienst zu senden, wird dem Benutzer ein Anmeldeformular für die Eingabe der Aad-Anmelde Informationen angezeigt. bei erfolgreicher Authentifizierung wird die Anforderung gesendet.

Anwendungen, die das Azure Daten-Explorer SDK nicht verwenden, können weiterhin die Aad-Client Bibliothek (Adal) verwenden, anstatt den Aad-Dienst Sicherheitsprotokoll-Client zu implementieren. Ein Beispiel hierfür finden Sie https://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet unter [] in einer .NET-Anwendung.

Zum Authentifizieren von Benutzern für Azure Daten-Explorer Access muss einer Anwendung zunächst die `Access Kusto` Delegierte Berechtigung erteilt werden. Ausführliche Informationen finden Sie im [Kusto-Handbuch zur](../../../provision-azure-ad-app.md#configure-delegated-permissions-for-the-application-registration) Bereitstellung von Aad-Anwendungen.

Der folgende kurze Code Ausschnitt veranschaulicht die Verwendung von Adal zum Abrufen eines Aad-Benutzer Tokens für den Zugriff auf Azure Daten-Explorer (Anmelde Benutzeroberfläche wird gestartet):

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire user token for the interactive user for Kusto:
AuthenticationResult result = authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", "your client app id",
    new Uri("your client app resource id"), new PlatformParameters(PromptBehavior.Auto)).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="application-authentication"></a>Anwendungsauthentifizierung

Der folgende kurze Code Ausschnitt veranschaulicht die Verwendung von Adal zum Abrufen eines Aad-Anwendungs Tokens für den Zugriff auf Azure Daten-Explorer. In diesem Flow wird keine Eingabeaufforderung angezeigt, und die Anwendung muss bei Aad registriert und mit den Anmelde Informationen ausgestattet sein, die zum Durchführen der Anwendungs Authentifizierung (z. b. ein von Aad ausgestelltes App-Schlüssel oder ein X509v2-Zertifikat, das in Aad vorab registriert wurde) benötigt wird.

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire application token for Kusto:
ClientCredential applicationCredentials = new ClientCredential("your application client ID", "your application key");
AuthenticationResult result =
        authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", applicationCredentials).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="on-behalf-of-authentication"></a>"Im Auftrag von"-Authentifizierung

In diesem Szenario wurde eine Anwendung ein Aad-Zugriffs Token für eine beliebige Ressource gesendet, die von der Anwendung verwaltet wird, und Sie verwendet dieses Token zum Abrufen eines neuen Aad-Zugriffs Tokens für die Azure Daten-Explorer-Ressource, damit die Anwendung im Namen des Prinzipals, der durch das ursprüngliche Aad-Zugriffs Token angegeben wird, auf Kusto zugreifen kann.

Dieser Flow wird als [OAuth2-tokenaustausch](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)bezeichnet.
In der Regel sind mehrere Konfigurationsschritte mit Aad erforderlich, und in einigen Fällen (abhängig von der Konfiguration des Aad-Mandanten) ist möglicherweise eine besondere Zustimmung vom Administrator des Aad-Mandanten erforderlich.



**Schritt 1: Einrichten einer Vertrauensstellung zwischen Ihrer Anwendung und dem Azure Daten-Explorer-Dienst**

1. Öffnen Sie die [Azure-Portal](https://portal.azure.com/) , und stellen Sie sicher, dass Sie beim richtigen Mandanten angemeldet sind (Weitere Informationen finden Sie in der oberen/rechten Ecke der Identität, die für die Anmeldung beim Portal verwendet wird).

2. Klicken Sie im Bereich Ressourcen auf **Azure Active Directory** und dann auf **App-Registrierungen** .

3. Suchen Sie die Anwendung, die den im-Auftrag-von-Flow verwendet, und öffnen Sie Sie.

4. Klicken Sie auf **API-Berechtigungen** und dann auf **Berechtigung hinzufügen** .

5. Suchen Sie nach der Anwendung namens **Azure Daten-Explorer** , und wählen Sie Sie aus.

6. Wählen Sie **user_impersonation/zugriffskusto** aus.

7. Klicken Sie auf **Berechtigung hinzufügen** .

**Schritt 2: Ausführen von tokenaustausch in Ihrem Servercode**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for a Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**Schritt 3: Bereitstellen des Tokens für die Kusto-Client Bibliothek und Ausführen von Abfragen**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}",
    clusterName,
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Authentifizierung und Autorisierung von Webclients (JavaScript)



**Aad-Anwendungskonfiguration**

> [!NOTE]
> Zusätzlich zu den Standard [Schritten](../../../provision-azure-ad-app.md) , die Sie befolgen müssen, um eine Aad-App einzurichten, sollten Sie auch den impliziten OAuth-Fluss in ihrer Aad-Anwendung aktivieren. Wählen Sie dazu im Azure-Portal auf der Anwendungsseite die Option Manifest aus, und legen Sie oauth2AllowImplicitFlow auf true fest.

**Details**

Wenn der Client ein JavaScript-Code ist, der im Browser des Benutzers ausgeführt wird, wird der implizite Zuweisungs Fluss verwendet. Das Token, das der Client Anwendung Zugriff auf den Azure Daten-Explorer-Dienst gewährt, wird unmittelbar nach einer erfolgreichen Authentifizierung als Teil des Umleitungs-URIs (in einem URI-Fragment) bereitgestellt. in diesem Flow wird kein Aktualisierungs Token angegeben. Daher kann der Client das Token für längere Zeiträume nicht zwischenspeichern und wieder verwenden.

Wie im Native Client-Flow sollten zwei Aad-Anwendungen (Server und Client) mit einer konfigurierten Beziehung zwischen Ihnen vorhanden sein.

Adaljs erfordert das erhalten einer id_token, bevor access_token Aufrufe durchgeführt werden.

Das Zugriffs Token wird durch Aufrufen der `AuthenticationContext.login()` -Methode abgerufen, und access_tokens durch Aufrufen von abgerufen werden `Authenticationcontext.acquireToken()` .

* Erstellen Sie einen authenticationcontext mit der richtigen Konfiguration:

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* `authContext.login()`Wird aufgerufen, bevor `acquireToken()` Sie versuchen, wenn Sie nicht angemeldet sind. eine gute Methode, um zu erfahren, ob Sie angemeldet sind oder nicht, ist das aufzurufen. `authContext.getCachedUser()` `false`
* Wird aufgerufen, `authContext.handleWindowCallback()` Wenn die Seite geladen wird. Dies ist der Code, der die Umleitung von Aad abfängt und das Token aus der Fragment-URL abruft und speichert.
* Aufrufen, `authContext.acquireToken()` um das tatsächliche Zugriffs Token abzurufen, jetzt verfügen Sie über ein gültiges ID-Token. Der erste Parameter für acquiretoken ist die Azure AD-Anwendungs Ressourcen-URL für den Kusto-Server.

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* im callbackthattoesthetoken können Sie das Token als bearertoken in der Azure-Daten-Explorer Anforderung verwenden. Beispiel:

```javascript
var settings = {
    url: "https://" + clusterAndRegionName + ".kusto.windows.net/v1/rest/query",
    type: "POST",
    data: JSON.stringify({
        "db": dbName,
        "csl": query,
        "properties": null
    }),
    contentType: "application/json; charset=utf-8",
    headers: { "Authorization": "Bearer " + token },
    dataType: "json",
    jsonp: false,
    success: function(data, textStatus, jqXHR) {
        if (successCallback !== undefined) {
            successCallback(data.Tables[0]);
        }

    },
    error: function(jqXHR, textStatus, errorThrown) {
        if (failureCallback !==  undefined) {
            failureCallback(textStatus, errorThrown, jqXHR.responseText);
        }

    },
};

$.ajax(settings).then(function(data) {/* do something wil the data */});
```

> Warnung: Wenn die folgende oder eine ähnliche Ausnahme bei der Authentifizierung angezeigt wird: `ReferenceError: AuthenticationContext is not defined`
Dies liegt wahrscheinlich daran, dass Sie nicht über authenticationcontext im globalen Namespace verfügen.
Leider hat adaljs zurzeit eine nicht dokumentierte Anforderung, dass der Authentifizierungs Kontext im globalen Namespace definiert wird.