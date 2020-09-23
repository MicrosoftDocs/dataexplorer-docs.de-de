---
title: Übersicht über die Kusto-Zugriffssteuerung – Azure Data Explorer
description: Dieser Artikel enthält eine Übersicht über die Kusto-Zugriffssteuerung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: 7b97d62e007b5294bf776fb5d5adcbac435056ef
ms.sourcegitcommit: 3fc8e9b6a313a863916031d4beba84123edcf123
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847852"
---
# <a name="kusto-access-control-overview"></a>Übersicht über die Kusto-Zugriffssteuerung

Die Zugriffssteuerung in Azure Data Explorer basiert auf zwei zentralen Faktoren.
* [Authentifizierung:](#authentication) Überprüft die Identität des Sicherheitsprinzipals, der eine Anforderung sendet
* [Autorisierung:](#authorization) Überprüft, ob der Sicherheitsprinzipal, der eine Anforderung sendet, über die erforderlichen Berechtigungen für die Zielressource verfügt

Eine Abfrage oder ein Steuerungsbefehl in einem Azure Data Explorer-Cluster, einer Datenbank oder einer Tabelle muss sowohl Authentifizierungs- als auch Autorisierungsüberprüfungen bestehen.

## <a name="authentication"></a>Authentifizierung

**Azure Active Directory (Azure AD)** ist der bevorzugte mehrinstanzenfähige Cloudverzeichnisdienst von Azure. Er kann Sicherheitsprinzipale authentifizieren oder einen Verbund mit anderen Identitätsanbietern einrichten.

Azure AD ist die bevorzugte Methode zur Authentifizierung für Azure Data Explorer in Microsoft. Sie unterstützt eine Reihe von Authentifizierungsszenarien.
* **Benutzerauthentifizierung** (interaktive Anmeldung): Dient zur Authentifizierung menschlicher Prinzipale.
* **Anwendungsauthentifizierung** (nicht interaktive Anmeldung): Dient zur Authentifizierung von Diensten und Anwendungen, die ohne Interaktion mit einem menschlichen Benutzer ausgeführt und authentifiziert werden müssen.

### <a name="user-authentication"></a>Benutzerauthentifizierung

Die Benutzerauthentifizierung erfolgt, wenn der Benutzer Anmeldeinformationen für Folgendes eingibt:
* Azure AD 
* ein Identitätsanbieter, der mit Azure AD verwendet wird

Bei erfolgreicher Ausführung erhält der Benutzer ein Sicherheitstoken, das an den Azure Data Explorer-Service übergeben werden kann. Für den Azure Data Explorer-Dienst ist es nicht wichtig, wie das Sicherheitstoken abgerufen wurde. Wichtig ist vielmehr, ob das Token gültig ist und welche Informationen von Azure AD (oder dem Verbundidentitätsanbieter) übergeben werden.

Auf der Clientseite unterstützt Azure Data Explorer die interaktive Authentifizierung, bei der der Benutzer von der Microsoft-Authentifizierungsbibliothek oder einem ähnlichen Code zur Eingabe von Anmeldeinformationen aufgefordert wird. Es unterstützt auch die tokenbasierte Authentifizierung, bei der die Anwendung, die Azure Data Explorer verwendet, ein gültiges Benutzertoken abruft. Die Anwendung, die Azure Data Explorer verwendet, kann auch ein gültiges Benutzertoken für einen anderen Dienst abrufen. Das Benutzertoken kann nur dann abgerufen werden, wenn eine Vertrauensstellung zwischen dieser Ressource und Azure Data Explorer vorhanden ist.

Weitere Informationen zur Verwendung der Kusto-Clientbibliotheken und zur Authentifizierung mithilfe von Azure AD bei Azure Data Explorer finden Sie unter [Kusto-Verbindungszeichenfolgen](../../api/connection-strings/kusto.md).

### <a name="application-authentication"></a>Anwendungsauthentifizierung

Verwenden Sie den Azure AD-Anwendungsauthentifizierungsablauf, wenn Anforderungen keinem bestimmten Benutzer zugeordnet sind oder kein Benutzer zur Eingabe von Anmeldeinformationen verfügbar ist. Dabei übergibt die Anwendung ein Geheimnis an Azure AD (oder an den Verbundidentitätsanbieter), um sich zu authentifizieren. Von den verschiedenen Azure Data Explorer-Clients werden folgende Szenarien unterstützt.

* Anwendungsauthentifizierung unter Verwendung eines lokal installierten X.509v2-Zertifikats
* Anwendungsauthentifizierung unter Verwendung eines X.509v2-Zertifikats, das als Bytestream an die Clientbibliothek übergeben wird
* Anwendungsauthentifizierung unter Verwendung einer Azure AD-Anwendungs-ID und eines Azure AD-Anwendungsschlüssels

    > [!NOTE] 
    > Die ID und der Schlüssel entsprechen einem Benutzernamen und Kennwort.

* Anwendungsauthentifizierung mit einem zuvor bezogenen gültigen Azure AD-Token (ausgestellt für Azure Data Explorer)
* Anwendungsauthentifizierung mit einem zuvor bezogenen gültigen Azure AD-Token (ausgestellt für eine andere Ressource). Diese Methode funktioniert, wenn zwischen dieser Ressource und Azure Data Explorer eine Vertrauensstellung besteht.

### <a name="microsoft-accounts-msas"></a>Microsoft-Konten (MSAs)

Microsoft-Konto (Microsoft Account, MSA) ist der Begriff für alle von Microsoft verwalteten, nicht organisationsbezogenen Benutzerkonten wie etwa `hotmail.com`, `live.com` oder `outlook.com`.
Kusto unterstützt die Benutzerauthentifizierung für MSAs (es gibt kein Sicherheitsgruppenkonzept), die anhand ihres Benutzerprinzipalnamens (User Principal Name, UPN) identifiziert werden.

Wenn ein MSA-Prinzipal für eine Azure Data Explorer-Ressource konfiguriert ist, versucht Azure Data Explorer **nicht**, den bereitgestellten UPN aufzulösen.

### <a name="authenticated-sdk-or-rest-calls"></a>Authentifizierte SDK- oder REST-Aufrufe

* Bei Verwendung der REST-API wird für die Authentifizierung der Standard-HTTP-`Authorization`-Header verwendet.
* Wenn Sie eine der Azure Data Explorer-.NET-Bibliotheken verwenden, wird die Authentifizierung durch Angeben der Authentifizierungsmethode und der Parameter in der [Verbindungszeichenfolge](../../api/connection-strings/kusto.md) gesteuert. Eine andere Methode besteht darin, die Eigenschaften für das Objekt für [Clientanforderungseigenschaften](../../api/netfx/request-properties.md) festzulegen.

### <a name="azure-data-explorer-client-sdk-as-an-azure-ad-client-application"></a>Azure Data Explorer-Client-SDK als Azure AD-Clientanwendung

Wenn die Kusto-Clientbibliotheken die Microsoft-Authentifizierungsbibliothek aufrufen, um ein Token für die Kommunikation mit Kusto zu beziehen, werden folgende Informationen angegeben:

* Die Ressource (Cluster-URI; z. B. `https://Cluster-and-region.kusto.windows.net`)
* Die Azure AD-Clientanwendungs-ID
* Der Umleitungs-URI der Azure AD-Clientanwendung
* Der Azure AD-Mandant, der sich auf den Azure AD-Endpunkt auswirkt, der für die Authentifizierung verwendet wird. Für den Azure AD-Mandanten `microsoft.com` ist der Azure AD-Endpunkt beispielsweise `https://login.microsoftonline.com/microsoft.com`.

Das Token, das von der Microsoft-Authentifizierungsbibliothek an die Azure Data Explorer-Clientbibliothek zurückgegeben wird, verfügt über die entsprechende Azure Data Explorer-Cluster-URL als Zielgruppe und über die Berechtigung zum Zugriff auf Azure Data Explorer als Bereich.

**Beispiel: Abrufen eines Azure AD-Benutzertokens für einen Azure Data Explorer-Cluster**

```csharp
// Create Auth Context for Azure AD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD TenantID or name}");

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

Alle authentifizierten Prinzipale durchlaufen eine Autorisierungsüberprüfung, bevor sie eine Aktion für eine Azure Data Explorer-Ressource durchführen können.
Azure Data Explorer verwendet ein [rollenbasiertes Autorisierungsmodell](role-based-authorization.md), bei dem Prinzipale einer oder mehreren Sicherheitsrollen zugeordnet werden. Die Autorisierung ist erfolgreich, wenn eine der Prinzipalrollen autorisiert ist.

Die Rolle „Datenbankbenutzer“ gewährt z. B. Sicherheitsprinzipalen, Benutzern oder Diensten die folgenden Rechte:
* Lesen der Daten einer bestimmten Datenbank
* Erstellen von Tabellen in der Datenbank
* Erstellen von Funktionen in der Datenbank

Die Zuordnung von Sicherheitsprinzipalen zu Sicherheitsrollen kann einzeln oder mithilfe von in Azure AD definierten Sicherheitsgruppen definiert werden. Die Definitionen der Befehle finden Sie unter [Verwaltung von Sicherheitsrollen](../security-roles.md).
