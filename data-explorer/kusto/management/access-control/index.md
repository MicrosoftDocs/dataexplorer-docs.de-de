---
title: Übersicht über die Kusto-Zugriffssteuerung – Azure Data Explorer | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Kusto-Zugriffssteuerung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 9d72373e8fc5c55740fa3e53a8f850a887517e7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490445"
---
# <a name="kusto-access-control-overview"></a>Übersicht über die Kusto-Zugriffssteuerung

Die Zugriffssteuerung in Kusto basiert auf zwei Dimensionen:
* [Authentifizierung:](#authentication) Überprüfung der Identität des Sicherheitsprinzipals, der eine Anforderung sendet
* [Autorisierung:](#authorization) Überprüfung, ob der Sicherheitsprinzipal, der eine Anforderung sendet, über die erforderlichen Berechtigungen für die Zielressource verfügt

Der Akteur kann erst nach erfolgreicher Authentifizierung und Autorisierung eine Abfrage oder einen Steuerungsbefehl für einen Kusto-Cluster, eine Datenbank oder eine Tabelle ausführen.

## <a name="authentication"></a>Authentifizierung


**Azure Active Directory (AAD)** ist der bevorzugte, mehrinstanzenfähige Cloudverzeichnisdienst von Azure. Er kann Sicherheitsprinzipale authentifizieren sowie im Verbund mit anderen Identitätsanbietern (beispielsweise Active Directory von Microsoft) verwendet werden.

AAD ist die bevorzugte Methode für die Kusto-Authentifizierung in Microsoft. Diese Lösung unterstützt eine Reihe von Authentifizierungsszenarien:
* **Benutzerauthentifizierung** (interaktive Anmeldung): Dient zur Authentifizierung menschlicher Prinzipale.
* **Anwendungsauthentifizierung** (nicht interaktive Anmeldung): Dient zur Authentifizierung von Diensten und Anwendungen, die ohne Interaktion mit einem menschlichen Benutzer ausgeführt/authentifiziert werden müssen. 

### <a name="user-authentication"></a>Benutzerauthentifizierung
Die Benutzerauthentifizierung erfolgt, wenn der Benutzer Anmeldeinformationen an AAD (oder an einen anderen Identitätsanbieter, der AAD nutzt – etwa AD FS) übergibt und daraufhin ein Sicherheitstoken erhält, das an den Kusto-Dienst übergeben werden kann. Für den Kusto-Dienst spielt es keine Rolle, auf welche Weise das Sicherheitstoken bezogen wurde. Er überprüft lediglich, ob das Token gültig ist und welche Informationen von AAD (oder von dem Verbundidentitätsanbieter) angegeben wurden.

Auf der Clientseite unterstützt Kusto die interaktive Authentifizierung, bei der der Benutzer von der AAD-Clientbibliothek ADAL oder von einem ähnlichen Code zur Eingabe von Anmeldeinformationen aufgefordert wird. Kusto unterstützt auch die tokenbasierte Authentifizierung, bei der die Anwendung, die Kusto verwendet, ein gültiges Benutzertoken bezieht und übergibt. Darüber hinaus wird ein Szenario unterstützt, bei dem die Anwendung, die Kusto verwendet, ein gültiges Benutzertoken für einen anderen Dienst (nicht Kusto) bezieht. Hierzu muss allerdings eine Vertrauensbeziehung zwischen dieser Ressource und Kusto bestehen.

Ausführliche Informationen zur Verwendung der Kusto-Client Bibliotheken und zum Authentifizieren mit Aad und Kusto finden Sie unter [Kusto-VerbindungsZeichenfolgen](../../api/connection-strings/kusto.md).

### <a name="application-authentication"></a>Anwendungsauthentifizierung
Wenn Anforderungen keinem bestimmten Benutzer zugeordnet sind oder kein Benutzer zur Eingabe von Anmeldeinformationen verfügbar ist, kann die AAD-Anwendungsauthentifizierung verwendet werden. Dabei übergibt die Anwendung ein Geheimnis an AAD (oder an den Verbundidentitätsanbieter), um sich zu authentifizieren. Von den verschiedenen Kusto-Clients werden folgende Szenarien unterstützt:

* Anwendungsauthentifizierung unter Verwendung eines lokal installierten X.509v2-Zertifikats
* Anwendungsauthentifizierung unter Verwendung eines X.509v2-Zertifikats, das als Bytestream an die Clientbibliothek übergeben wird
* Anwendungsauthentifizierung unter Verwendung einer AAD-Anwendungs-ID und eines AAD-Anwendungsschlüssels (gewissermaßen die Authentifizierung mit Benutzername/Kennwort für Anwendungen)
* Anwendungsauthentifizierung mit einem zuvor bezogenen gültigen AAD-Token (ausgestellt für Kusto)
* Anwendungsauthentifizierung mit einem zuvor bezogenen gültigen AAD-Token, das für eine andere Ressource ausgestellt wurde – vorausgesetzt, es besteht eine Vertrauensbeziehung zwischen dieser Ressource und Kusto


### <a name="microsoft-accounts-msas"></a>Microsoft-Konten (MSAs)
Microsoft-Konten (MSAS) ist der Begriff für alle von Microsoft verwalteten nicht-organisationsbezogenen Benutzerkonten, z `hotmail.com`. `live.com`b `outlook.com`.
Kusto unterstützt die Benutzerauthentifizierung für MSAs. (Hinweis: Es gibt kein Sicherheitsgruppenkonzept.) Die MSAs werden anhand ihres UPN (universeller Prinzipalname) identifiziert.
Wenn ein MSA-Prinzipal für eine Kusto-Ressource konfiguriert ist, versucht Kusto **nicht**, den angegebenen UPN aufzulösen.

### <a name="authenticated-sdk-or-rest-calls"></a>Authentifizierte SDK- oder REST-Aufrufe
* Bei Verwendung einer REST-API wird für die Authentifizierung der HTTP-Standardheader `Authorization` verwendet.
* Wenn Sie eine der Kusto-.NET-Bibliotheken verwenden, wird die Authentifizierung durch Angeben der Authentifizierungsmethode und der Parameter in der [Kusto-Verbindungszeichenfolge](../../api/connection-strings/kusto.md) oder durch Festlegen von Eigenschaften für das [Objekt für Clientanforderungseigenschaften](https://kusto.azurewebsites.net/docs/api/request-properties.html) gesteuert.

### <a name="kusto-client-sdk-as-an-aad-client-application"></a>Kusto Client SDK als AAD-Clientanwendung
Wenn die Kusto-Clientbibliotheken ADAL (die AAD-Clientbibliothek) aufrufen, um ein Token für die Kommunikation mit Kusto zu beziehen, werden folgende Informationen angegeben:

1. Die Ressource (Cluster-URI; beispielsweise `https://Cluster-and-region.kusto.windows.net`)
2. Die ID der AAD-Clientanwendung
3. Der Umleitungs-URI der AAD-Clientanwendung
4. Der AAD-Mandant (wirkt sich auf den für die Authentifizierung verwendeten AAD-Endpunkt aus; für den AAD-Mandanten `microsoft.com` lautet der AAD-Endpunkt beispielsweise `https://login.microsoftonline.com/microsoft.com`)

Das Token, das von ADAL an die Kusto-Clientbibliothek zurückgegeben wird, besitzt als Zielgruppe die entsprechende Kusto-Cluster-URL und als Umfang die Berechtigung für den Zugriff auf Kusto.

**Beispiel: Beziehen eines AAD-Benutzertokens für einen Kusto-Cluster**
```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD TenantID or name}");

// Provide your Application ID and redirect URI
var clientAppID = "{your client app id}";
var redirectUri = new Uri("{your client app redirect uri}");

// acquireTokenTask will receive the bearer token for the authenticated user
var acquireTokenTask = authContext.AcquireTokenAsync(
    $"https://{clusterNameAndRegion}.kusto.windows.net",
    clientAppID,
    redirectUri,
    new PlatformParameters(PromptBehavior.Auto, null)).GetAwaiter().GetResult();
```


## <a name="authorization"></a>Authorization

Alle authentifizierten Prinzipale durchlaufen unabhängig von der verwendeten Authentifizierungsmethode auch eine Autorisierungsüberprüfung, bevor sie eine Aktion für eine Kusto-Ressource ausführen können.
Kusto verwendet ein [rollenbasiertes Autorisierungsmodell](role-based-authorization.md): Prinzipale werden einzelnen oder mehreren **Sicherheitsrollen** zugeordnet, und die Autorisierung ist erfolgreich, solange eine der Rollen des Prinzipals autorisiert ist.

So sind beispielsweise Sicherheitsprinzipale (Benutzer oder Dienste) mit der Rolle **Datenbankbenutzer** berechtigt, die Daten einer bestimmten Datenbank zu lesen, Tabellen in der Datenbank zu erstellen und Funktionen darin zu erstellen.

Die Zuordnung von Sicherheitsprinzipalen zu Sicherheitsrollen kann einzeln oder mithilfe von (in AAD definierten) Sicherheitsgruppen definiert werden. Informationen zu den entsprechenden Befehlen finden Sie unter [Security roles management](../security-roles.md) (Verwaltung von Sicherheitsrollen).

