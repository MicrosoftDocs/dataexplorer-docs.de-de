---
title: 'Kusto-Verbindungs Zeichenfolgen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden Kusto-Verbindungs Zeichenfolgen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 8c5ade644f383a5a0d9e846b1a3143027d1eb467
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863182"
---
# <a name="kusto-connection-strings"></a>Kusto-Verbindungszeichenfolgen

Kusto-Verbindungs Zeichenfolgen können die erforderlichen Informationen bereitstellen, damit eine Kusto-Client Anwendung eine Verbindung mit einem Kusto-Dienst Endpunkt herstellen kann. Kusto-Verbindungs Zeichenfolgen werden nach den ADO.NET-Verbindungs Zeichenfolgen modelliert. Das heißt, die Verbindungs Zeichenfolge ist eine durch Semikolons getrennte Liste von Name-Wert-Parameter Paaren, die optional einem einzelnen URI vorangestellt wird.

**Beispiel:**

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

Der URI stellt den Dienst Endpunkt für die Kommunikation bereit:

* ( `https://help.kusto.windows.net` )-Wert der- `Data Source` Eigenschaft.
* `Samples`(Standarddatenbank): Wert der `Initial Catalog` Eigenschaft.

Zwei weitere Eigenschaften werden mit der Name-Wert-Syntax bereitgestellt: 

* `Fed`Eigenschaft (auch genannt `AAD Federated Security` ) auf festgelegt `true` .
* `Accept`die Eigenschaft ist auf festgelegt `true` .

> [!NOTE]
>
> * Bei Eigenschaften Namen wird die Groß-/Kleinschreibung nicht beachtet, und Leerzeichen zwischen Name-Wert-Paaren werden ignoriert
> * Bei Eigenschafts Werten **wird** Groß-/Kleinschreibung Ein Eigenschafts Wert, der ein Semikolon ( `;` ), ein einzelnes Anführungszeichen ( `'` ) oder ein doppeltes Anführungszeichen () enthält, `"` muss zwischen doppelten Anführungszeichen eingeschlossen werden.

Mehrere Kusto-Client Tools unterstützen eine Erweiterung über dem URI-Präfix der Verbindungs Zeichenfolge, da Sie die Verwendung der Kurzform " `@` _Clustername_ `/` _InitialCatalog_ " zulassen.
Beispielsweise wird die Verbindungs Zeichenfolge `@help/Samples` von diesen Tools in übersetzt `https://help.kusto.windows.net/Samples; Fed=true` , was drei Eigenschaften angibt ( `Data Source` , `Initial Catalog` und `AAD Federated Security` ).

Programm gesteuert können Kusto-Verbindungs Zeichenfolgen von der c#-Klasse analysiert und bearbeitet werden `Kusto.Data.KustoConnectionStringBuilder` . Diese Klasse überprüft alle Verbindungs Zeichenfolgen und generiert eine Lauf Zeit Ausnahme, wenn die Validierung fehlschlägt.
Diese Funktionalität ist in allen Varianten des Kusto SDK vorhanden.

## <a name="connection-string-properties"></a>Verbindungs Zeichen folgen Eigenschaften

In der folgenden Tabelle werden alle Eigenschaften aufgelistet, die Sie in einer Kusto-Verbindungs Zeichenfolge angeben können.
Dabei werden programmgesteuerte Namen (der Name der Eigenschaft im- `Kusto.Data.KustoConnectionStringBuilder` Objekt) sowie zusätzliche Eigenschaftsnamen, die Aliase sind, aufgelistet.

### <a name="general-properties"></a>Allgemeine Eigenschaften

| Eigenschaftenname              | Alternative Namen                      | Programm gesteuerter Name  | BESCHREIBUNG                                                                                                                          |
|----------------------------|----------------------------------------|--------------------|---------------------------------------------------|
| Client Version für die Ablauf Verfolgung |                                        | Traceclientversion | Verwenden Sie diesen Wert, wenn Sie die Client Version nachverfolgen.   |
| Data source                | Addr, Address, Network Address, Server | DataSource         | Der URI, der den Kusto-Dienst Endpunkt angibt. Beispiel: `https://mycluster.kusto.windows.net` oder `net.tcp://localhost`               |
| Anfangskatalog            | Datenbank                               | InitialCatalog     | Der Name der Datenbank, die standardmäßig verwendet werden soll. Beispiel: MyDatabase|
| Abfrage Konsistenz          | Querykonsistenz                       | Querykonsistenz   | Legen Sie auf `strongconsistency` oder fest `weakconsistency` , um zu bestimmen, ob die Abfrage vor der Ausführung mit den Metadaten synchronisiert werden soll. |

### <a name="user-authentication-properties"></a>Eigenschaften der Benutzerauthentifizierung

| Eigenschaftenname          | Alternative Namen                          | Programm gesteuerter Name | BESCHREIBUNG                       |
|------------------------|--------------------------------------------|-------------------|-----------------------------------|
| Aad-Verbund Sicherheit | Verbund Sicherheit, Verbund, Fed, aadfed | Federatedsecurity | Ein boolescher Wert, der den Client anweist, Azure Active auszuführen.  |
| MFA erzwingen            | MFA, enforcemfa                             | Enforcemfa        | Ein boolescher Wert, der den Client anweist, ein mehrstufiger Authentifizierungs Token abzurufen.       |
| Benutzer-ID                | UID, Benutzer                                  | UserID            | Ein Zeichen folgen Wert, der den Client anweist, die Benutzerauthentifizierung mit dem festgelegten Benutzernamen auszuführen.           |
| Benutzer Name für die Ablauf Verfolgung  |                                            | Traceusername     | Ein Zeichen folgen Wert, der dem Dienst meldet, welcher Benutzername bei der internen Ablauf Verfolgung der Anforderung verwendet werden soll.         |
| Benutzer Token             | "Rtoken", "userToken"                        | UserToken         | Ein Zeichen folgen Wert, der den Client anweist, die Benutzerauthentifizierung mit dem angegebenen bearertoken auszuführen.<br/>Überschreibt applicationclientid, applicationkey und applicationtoken. (Wenn angegeben, wird der tatsächliche Client Authentifizierungs Fluss zugunsten des bereitgestellten Tokens übergebene.)       |
| Namespace              | NS                                         | Namespace         | (Für zukünftige Verwendung)  |



### <a name="application-authentication-properties"></a>Eigenschaften der Anwendungs Authentifizierung

|Eigenschaftenname                                     |Alternative Namen                         |Programm gesteuerter Name                             |BESCHREIBUNG      |
|--------------------------------------------------|------------------------------------------|----------------------------------------------|-----------------|
|Aad-Verbund Sicherheit                            |Verbund Sicherheit, Verbund, Fed, aadfed|Federatedsecurity                             |Ein boolescher Wert, der den Client anweist, Azure Active Directory Verbund Authentifizierung (AAD) auszuführen.|
|Fingerabdruck des Anwendungs Zertifikats                |Appcert                                   |Applicationcertifierethumschlag Print              |Ein Zeichen folgen Wert, der den Fingerabdruck des Client Zertifikats bereitstellt, das verwendet wird, wenn ein Client Zertifikat für die Client Authentifizierung verwendet wird.|
|Anwendungs Client-ID                             |AppClientId                               |Applicationclientid                           |Ein Zeichen folgen Wert, der die Anwendungs Client-ID zur Verwendung bei der Authentifizierung bereitstellt.|
|Anwendungsschlüssel                                   |AppKey                                    |ApplicationKey                                |Ein Zeichen folgen Wert, der den Anwendungs Schlüssel bereitstellt, der bei der Authentifizierung mit einem geheimen Anwendungs Schlüssel verwendet wird.|
|Anwendungs Name für die Ablauf Verfolgung                      |Traceappname                              |Applicationnamefortracing                     |Ein Zeichen folgen Wert, der dem Dienst meldet, welcher Anwendungsname bei der internen Ablauf Verfolgung der Anforderung verwendet werden soll.|
|Anwendungs Token                                 |AppToken                                  |ApplicationToken                              |Ein Zeichen folgen Wert, der den Client anweist, die Anwendung mit dem angegebenen bearertoken zu authentifizieren.|
|Autoritäts-ID                                      |TenantId                                  |Authority                                     |Ein Zeichen folgen Wert, der den Namen oder die ID des Mandanten bereitstellt, in dem die Anwendung registriert ist.|
|                                                  |                                          |Embedabdmanagedidentity                       |Ein Zeichen folgen Wert, der den Client anweist, welche Anwendungs Identität für die verwaltete Identitäts Authentifizierung verwendet werden soll. verwenden `system` Sie, um die vom System zugewiesene Identität anzugeben. Diese Eigenschaft kann nur Programm gesteuert mit einer Verbindungs Zeichenfolge festgelegt werden.|Managedserviceidentity                        |AUFGABE|
|Distinguished Name des Anwendungs Zertifikats|Betreff des Anwendungs Zertifikats           |Applicationcertifieresubjecterkennbare shedname||
|Distinguished Name des Anwendungs Zertifikats Ausstellers |Aussteller des Anwendungs Zertifikats            |Applicationcertificeissuererkennbar shedname ||
|Öffentliches Zertifikat des Anwendungs Zertifikats senden   |Anwendungs Zertifikat SendX5c, SendX5c  |Applicationcertifikatesendpubliccertificate   ||



### <a name="client-communication-properties"></a>Eigenschaften der Client Kommunikation

|Eigenschaftenname                      |Alternative Namen|Programm gesteuerter Name  |BESCHREIBUNG                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|Akzeptieren      ||Akzeptieren      |Ein boolescher Wert, der detaillierte Fehler Objekte anfordert, bei einem Fehler zurückgegeben werden.|
|Streaming   ||Streaming   |Ein boolescher Wert, der anfordert, dass der Client keine Daten sammelt, bevor er für den Aufrufer bereitgestellt wird.|
|Nicht komprimiert||Nicht komprimiert|Ein boolescher Wert, der den Client anfordert, dass die Komprimierung auf Transport Ebene nicht angefordert wird.|

## <a name="authentication-properties-details"></a>Authentifizierungs Eigenschaften (Details)

Eine der wichtigsten Aufgaben der Verbindungs Zeichenfolge besteht darin, den Client zu informieren, wie er sich bei dem Dienst authentifizieren kann.
Der folgende Algorithmus wird in der Regel von Clients für die Authentifizierung bei http/https-Endpunkten verwendet:

1. Wenn aadfederatedsecurity den Wert "true" hat:
    1. Wenn userToken angegeben ist, verwenden Sie die Aad-Verbund Authentifizierung mit dem angegebenen Token.
    1. Wenn "applicationtoken" angegeben ist, wird eine Verbund Authentifizierung mit dem angegebenen Token durchgeführt.
    1. Wenn "applicationclientid" und "applicationkey" angegeben sind, führen Sie andernfalls eine Verbund Authentifizierung mit der angegebenen Anwendungs Client-ID und dem angegebenen Schlüssel aus.
    1. Wenn applicationclientid und applicationcertifiskiethumschlag Print angegeben sind, führen Sie andernfalls eine Verbund Authentifizierung mit der angegebenen Anwendungs Client-ID und dem angegebenen Zertifikat aus.
    1. Andernfalls wird eine Verbund Authentifizierung mit der Identität des aktuellen angemeldeten Benutzers durchgeführt (Benutzer wird aufgefordert, wenn dies die erste Authentifizierung in der Sitzung ist).

1. Andernfalls authentifizieren Sie sich nicht.


### <a name="aad-federated-application-authentication-with-application-certificate"></a>Azure AD-Verbund Anwendungs Authentifizierung mit Anwendungs Zertifikat

1. Die Authentifizierung auf Grundlage des Zertifikats einer Anwendung wird nur für Webanwendungen unterstützt (und nicht für Native Client-Anwendungen).
1. Die Webanwendung sollte so konfiguriert werden, dass Sie das angegebene Zertifikat akzeptiert. [Authentifizieren basierend auf dem Zertifikat der Aad-Anwendung](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
1. Die Webanwendung sollte als autorisierter Prinzipal im relevanten Kusto-Cluster konfiguriert werden.
1. Das Zertifikat mit dem angegebenen Fingerabdruck muss installiert sein (im lokalen Computerspeicher oder im aktuellen Benutzerspeicher).
1. Der öffentliche Schlüssel des Zertifikats muss mindestens 2048 Bits enthalten.

## <a name="aad-based-authentication-examples"></a>Beispiele für die Aad-basierte Authentifizierung

**Aad-Verbund Authentifizierung unter Verwendung der aktuell angemeldeten Benutzeridentität (Benutzer wird bei Bedarf aufgefordert)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;Authority Id={authority}"
```

**Aad-Verbund Authentifizierung mit Benutzer-ID-Hinweis (Benutzer werden bei Bedarf aufgefordert)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var userUPN = "johndoe@contoso.com";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);
kustoConnectionStringBuilder.UserID = userUPN;

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    UserID = userUPN,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;User ID={userUPN};Authority Id={authority}"
```

**Authentifizierung von Aad-Verbund Anwendungen mithilfe von applicationclientid und applicationkey**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};Authority Id={authority}"
```

**Aad-Verbund Authentifizierung mithilfe von Benutzer-/anwendungstoken**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var access_token = "<access token obtained from AAD>"

// Recommended syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadUserTokenAuthentication(access_token, authority);

// Legacy syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    UserToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source={serviceUri};Database=NetDefaultDB;Fed=True;UserToken={access_token};Authority Id={authority}"

// Recommended syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationTokenAuthentication(access_token, authority);

// Legacy syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppToken={applicationToken};Authority Id={authority}"
```

**Der Rückruf des Tokenanbieters wird verwendet (wird jedes Mal aufgerufen, wenn ein Token erforderlich ist)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
Func<string> tokenProviderCallback; // User-defined method to retrieve the access token

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadTokenProviderAuthentication(tokenProviderCallback);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    TokenProviderCallback = () => Task.FromResult(tokenProviderCallback()),
};
```

**Verwenden der verwalteten Identität**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var managedIdentity = "<managed identity>"; // For system-assigned identity use "system"

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadManagedIdentity(managedIdentity);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    EmbeddedManagedIdentity = managedIdentity,
};
```

**Verwenden des X. 509-Zertifikats**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
X509Certificate2 applicationCertificate = "<certificate blob>";
bool sendX5c = <desired value>; // Set too 'True' to use Trusted Issuer feature of AAD

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationCertificateAuthentication(applicationClientId, applicationCertificate, authority, sendX5c);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateBlob = applicationCertificate,
    ApplicationCertificateSendX5c = sendX5c,
    Authority = authority,
};
```

**Verwenden des X. 509-Zertifikats nach Fingerabdruck (der Client versucht, das Zertifikat aus dem lokalen Speicher zu laden)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};Authority Id={authority}"
```
