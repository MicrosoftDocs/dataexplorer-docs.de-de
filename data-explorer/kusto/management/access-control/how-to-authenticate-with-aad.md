---
title: How-How-To Authenticate mit AAD for Azure Data Explorer Access - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Sie sich mit AAD für Azure Data Explorer Access in Azure Data Explorer authentifizieren.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: dc3a87a290ac535ac475af5017170a0478cd9b54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522915"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>How-To Authenticate mit AAD für Azure Data Explorer Access

Die empfohlene Möglichkeit für den Zugriff auf Azure Data Explorer besteht darin, sich beim **Azure Active Directory-Dienst** zu authentifizieren (manchmal auch **als Azure AD**oder einfach **AAD**bezeichnet). Dadurch wird sichergestellt, dass Azure Data Explorer die Verzeichnisanmeldeinformationen des zugriffsbereiten Prinzipals nie sieht, indem ein zweistufiger Prozess verwendet wird:

1. Im ersten Schritt kommuniziert der Client mit dem AAD-Dienst, authentifiziert ihn und fordert ein Zugriffstoken an, das speziell für den jeweiligen Azure Data Explorer-Endpunkt ausgestellt wurde, auf den der Client zugreifen möchte.
2. Im zweiten Schritt stellt der Client Anforderungen an Azure Data Explorer aus, wobei das im ersten Schritt als Identitätsnachweis erworbene Zugriffstoken azure.

Azure Data Explorer führt dann die Anforderung im Auftrag des Sicherheitsprinzipals aus, für den AAD das Zugriffstoken ausgestellt hat, und alle Autorisierungsprüfungen werden mit dieser Identität durchgeführt.

In den meisten Fällen wird empfohlen, eines der Azure Data Explorer-SDKs zu verwenden, um programmgesteuert auf den Dienst zuzugreifen, da sie einen Großteil des Unumsatoles der Implementierung des oben genannten Flows (und vieles mehr) beseitigen. Siehe z. B. das [.NET SDK](../../api/netfx/about-the-sdk.md).
Die Authentifizierungseigenschaften werden dann von der [Kusto-Verbindungszeichenfolge](../../api/connection-strings/kusto.md)festgelegt.
Wenn dies nicht möglich ist, lesen Sie bitte weiter, um detaillierte Informationen darüber zu erhalten, wie Sie diesen Flow selbst implementieren können.

Die wichtigsten Authentifizierungsszenarien sind:

* **Eine Clientanwendung, die einen angemeldeten Benutzer authentifiziert.**
  In diesem Szenario löst eine interaktive (Client-)Anwendung eine AAD-Eingabeaufforderung an den Benutzer aus, die Anmeldeinformationen (z. B. Benutzername und Kennwort) anfordert.
  Siehe [Benutzerauthentifizierung](#user-authentication),

* **Eine "kopflose" Anwendung**.
  In diesem Szenario wird eine Anwendung ausgeführt, in der kein Benutzer vorhanden ist, um Anmeldeinformationen bereitzustellen, und stattdessen authentifiziert sich die Anwendung als "sich selbst" bei AAD mit einigen Anmeldeinformationen, mit denen sie konfiguriert wurde.
  Siehe [Anwendungsauthentifizierung](#application-authentication).

* **Bei-der-Authentifizierung**.
  In diesem Szenario, das manchmal als "Webdienst" oder "Web-App"-Szenario bezeichnet wird, ruft die Anwendung ein AAD-Zugriffstoken von einer anderen Anwendung ab und "konvertiert" es dann in ein anderes AAD-Zugriffstoken, das mit Azure Data Explorer verwendet werden kann.
  Mit anderen Worten, die Anwendung fungiert als Vermittler zwischen dem Benutzer oder der Anwendung, der Anmeldeinformationen bereitgestellt hat, und dem Azure Data Explorer-Dienst.
  Siehe [Authentifizierung im Namen](#on-behalf-of-authentication).

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Angeben der AAD-Ressource für Azure Data Explorer

Beim Abrufen eines Zugriffstokens von AAD muss der Client AAD mitteilen, für welche **AAD-Ressource** das Token ausgegeben werden soll. Die AAD-Ressource eines Azure Data Explorer-Endpunkts ist der URI des Endpunkts, mit Ausnahme der Portinformationen und des Pfads. Beispiel:

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>Angeben der AAD-Mandanten-ID

AAD ist ein mehrinstanzenfähiger Dienst, und jede Organisation kann ein Objekt mit dem Namen **Verzeichnis** in AAD erstellen. Das Verzeichnisobjekt enthält sicherheitsrelevante Objekte wie Benutzerkonten, Anwendungen und Gruppen. AAD bezieht sich häufig auf das Verzeichnis als **Mandanten**. AAD-Mandanten werden durch eine GUID (**Mandanten-ID**) identifiziert. In vielen Fällen können AAD-Mandanten auch anhand des Domänennamens der Organisation identifiziert werden.

Beispielsweise kann eine Organisation mit dem Namen "Contoso" die Mandanten-ID `4da81d62-e0a8-4899-adad-4349ca6bfe24` und den Domänennamen `contoso.com`haben.

## <a name="specifying-the-aad-authority"></a>Angeben der AAD-Berechtigung

AAD verfügt über eine Reihe von Endpunkten für die Authentifizierung:

* Wenn der Mandant, der den zu authentifizierenden Prinzipal hostet, bekannt ist (d. h., wenn man weiß, in welchem AAD-Verzeichnis sich der Benutzer oder die Anwendung befindet), ist `https://login.microsoftonline.com/{tenantId}`der AAD-Endpunkt .
  Hier `{tenantId}` bei der Organisation ist entweder die Mandanten-ID der Organisation in `contoso.com`AAD oder ihr Domänenname (z. B. ) der Fall.

* Wenn der Mandant, der den zu authentifizierenden Prinzipal hostet, `{tenantId}` nicht bekannt `common`ist, kann der "gemeinsame" Endpunkt verwendet werden, indem der obige durch den Wert ersetzt wird.

> [!NOTE]
> Der AAD-Endpunkt, der für die Authentifizierung verwendet wird, wird auch als **AAD-Berechtigungs-URL** oder einfach **Als AAD-Berechtigung**bezeichnet.

## <a name="aad-token-cache"></a>AAD-Tokencache

Bei Verwendung des Azure Data Explorer SDK werden die AAD-Token auf dem lokalen Computer in einem benutzerdefinierten Tokencache gespeichert (eine Datei mit dem Namen **%APPDATA%-Kusto-TokenCache.data,** auf die nur der angemeldete Benutzer zugreifen oder diese entschlüsselt werden kann). Der Cache wird auf Token überprüft, bevor der Benutzer nach Anmeldeinformationen aufgefordert wird, wodurch die Anzahl der Anmeldeinformationen, die ein Benutzer eingeben muss, erheblich reduziert wird.

> [!NOTE]
> Der AAD-Tokencache reduziert die Anzahl der interaktiven Eingabeaufforderungen, die einem Benutzer beim Zugriff auf Azure Data Explorer angezeigt werden, reduziert sie jedoch nicht vollständig. Darüber hinaus können Benutzer nicht im Voraus vorhersehen, wann sie zur Eingabe von Anmeldeinformationen aufgefordert werden.
> Dies bedeutet, dass man nicht versuchen darf, ein Benutzerkonto für den Zugriff auf Azure Data Explorer zu verwenden, wenn nicht interaktive Anmeldungen unterstützt werden müssen (z. B. beim Planen von Aufgaben), da, wenn die Zeit für die Aufforderung zum angemeldeten Benutzer nach Anmeldeinformationen kommt, diese Eingabeaufforderung fehlschlägt, wenn sie unter nicht interaktiver Anmeldung ausgeführt wird.


## <a name="user-authentication"></a>Benutzerauthentifizierung

Die einfachste Möglichkeit, mit Benutzerauthentifizierung auf Azure Data Explorer zuzugreifen, `Federated Authentication` besteht darin, das Azure `true`Data Explorer SDK zu verwenden und die Eigenschaft der Azure Data Explorer-Verbindungszeichenfolge auf festzulegen. Wenn das SDK zum ersten Mal zum Senden einer Anforderung an den Dienst verwendet wird, wird dem Benutzer ein Anmeldeformular zur Eingabe der AAD-Anmeldeinformationen angezeigt, und bei erfolgreicher Authentifizierung wird die Anforderung gesendet.

Anwendungen, die das Azure Data Explorer SDK nicht verwenden, können weiterhin die AAD-Clientbibliothek (ADAL) verwenden, anstatt den AAD-Dienstsicherheitsprotokollclient zu implementieren. Ein Beispielhttps://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNetdafür finden Sie unter [ ] aus einer .NET-Anwendung.

Um Benutzer für azure Data Explorer-Zugriff zu `Access Kusto` authentifizieren, muss einer Anwendung zunächst die delegierte Berechtigung erteilt werden. Weitere Informationen finden Sie in der [Kusto-Anleitung zur Bereitstellung von AAD-Anwendungen.](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application)

Der folgende kurze Codeausschnitt veranschaulicht die Verwendung von ADAL zum Abrufen eines AAD-Benutzertokens für den Zugriff auf Azure Data Explorer (startet die Anmeldebenutzeroberfläche):

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

Der folgende kurze Codeausschnitt veranschaulicht die Verwendung von ADAL zum Abrufen eines AAD-Anwendungstokens für den Zugriff auf Azure Data Explorer. In diesem Flow wird keine Eingabeaufforderung angezeigt, und die Anwendung muss bei AAD registriert und mit Anmeldeinformationen ausgestattet sein, die für die Anwendungsauthentifizierung erforderlich sind (z. B. ein von AAD ausgestellter App-Schlüssel oder ein X509v2-Zertifikat, das bei AAD vorregistriert wurde).

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

## <a name="on-behalf-of-authentication"></a>On-Behalf-of-Authentifizierung

In diesem Szenario wurde einer Anwendung ein AAD-Zugriffstoken für eine beliebige Ressource gesendet, die von der Anwendung verwaltet wird, und sie verwendet dieses Token, um ein neues AAD-Zugriffstoken für die Azure Data Explorer-Ressource zu erwerben, damit die Anwendung im Namen des vom ursprünglichen AAD-Zugriffstoken angegebenen Prinzipals auf Kusto zugreifen kann.

Dieser Flow wird als [OAuth2-Tokenaustauschfluss](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)bezeichnet.
In der Regel sind mehrere Konfigurationsschritte mit AAD erforderlich, und in einigen Fällen (abhängig von der AAD-Mandantenkonfiguration) kann dies eine besondere Zustimmung des Administrators des AAD-Mandanten erfordern.



**Schritt 1: Einrichten einer Vertrauensstellung zwischen Ihrer Anwendung und dem Azure Data Explorer-Dienst**

1. Öffnen Sie das [Azure-Portal,](https://portal.azure.com/) und stellen Sie sicher, dass Sie beim richtigen Mandanten angemeldet sind (siehe obere/rechte Ecke für die Identität, die zum Anmelden beim Portal verwendet wird).

2. Klicken Sie im Ressourcenbereich auf **Azure Active Directory**und dann auf **App-Registrierungen**.

3. Suchen Sie die Anwendung, die den In-Behalf-of-Flow verwendet, und öffnen Sie sie.

4. Klicken Sie auf **API-Berechtigungen**, und fügen Sie dann **eine Berechtigung hinzu.**

5. Suchen Sie nach der Anwendung mit dem Namen **Azure Data Explorer,** und wählen Sie sie aus.

6. Wählen Sie **user_impersonation / Access Kusto**.

7. Klicken Sie auf **Berechtigung hinzufügen**.

**Schritt 2: Tokenaustausch in Ihrem Servercode durchführen**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for for Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application 
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**Schritt 3: Bereitstellen des Tokens für die Kusto-Clientbibliothek und Ausführen von Abfragen**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}", 
    clusterName, 
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Web Client -Authentifizierung und -Autorisierung (JavaScript)



**AAD-Anwendungskonfiguration**

> [!NOTE]
> Zusätzlich zu den [Standardschritten,](./how-to-provision-aad-app.md) die Sie befolgen müssen, um eine AAD-App einzurichten, sollten Sie auch den impliziten Oauth-Fluss in Ihrer AAD-Anwendung aktivieren. Sie können dies erreichen, indem Sie Manifest auf Ihrer Anwendungsseite im azure-Portal auswählen und oauth2AllowImplicitFlow auf true festlegen.

**Details**

Wenn es sich bei dem Client um einen JavaScript-Code handelt, der im Browser des Benutzers ausgeführt wird, wird der implizite Grant-Fluss verwendet. Das Token, das der Clientanwendung Zugriff auf den Azure Data Explorer-Dienst gewährt, wird unmittelbar nach einer erfolgreichen Authentifizierung als Teil des Umleitungs-URI (in einem URI-Fragment) bereitgestellt. In diesem Flow wird kein Aktualisierungstoken angegeben, sodass der Client das Token nicht über einen längeren Zeitraum zwischenspeichern und wiederverwenden kann.

Wie im systemeigenen Clientfluss sollten zwei AAD-Anwendungen (Server und Client) mit einer konfigurierten Beziehung zwischen ihnen vorhanden sein. 

AdalJs erfordert eine id_token, bevor access_token Anrufe getätigt werden.

Das Zugriffstoken wird durch `AuthenticationContext.login()` Aufrufen der Methode abgerufen, und access_tokens werden durch Aufrufen `Authenticationcontext.acquireToken()`abgerufen.

* Erstellen Sie einen AuthenticationContext mit der richtigen Konfiguration:

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* Rufen `authContext.login()` Sie `acquireToken()` an, bevor Sie versuchen, wenn Sie nicht angemeldet sind. eine gute Möglichkeit ot wissen, ob Sie angemeldet `authContext.getCachedUser()` sind oder `false`nicht, ist zu rufen und zu sehen, ob es zurückkehrt )
* Rufen `authContext.handleWindowCallback()` Sie an, wenn Ihre Seite geladen wird. Dies ist der Code, der die Umleitung von AAD abfängt und das Token aus der Fragment-URL herauszieht und zwischenspeichert.
* Rufen `authContext.acquireToken()` Sie an, um das eigentliche Zugriffstoken abzurufen, nachdem Sie über ein gültiges ID-Token verfügen. Der erste Parameter, der Token erfasst, ist die Kusto-Server-AAD-Anwendungsressourcen-URL.  

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* Im callbackThatUsesTheToken können Sie das Token als Trägertoken in der Azure Data Explorer-Anforderung verwenden. Beispiel:

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

> Warnung - wenn Sie bei der Authentifizierung die folgende oder ähnliche Ausnahme erhalten:`ReferenceError: AuthenticationContext is not defined` 
Dies liegt wahrscheinlich daran, dass Sie AuthenticationContext nicht im globalen Namespace haben. Leider verfügt AdalJS derzeit über eine nicht dokumentierte Anforderung, dass der Authentifizierungskontext im globalen Namespace definiert wird.