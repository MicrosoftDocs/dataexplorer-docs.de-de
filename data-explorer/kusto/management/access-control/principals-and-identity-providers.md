---
title: Prinzipale und Identitätsanbieter - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Prinzipale und Identitätsanbieter in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0638ba0031162dadbb4b9a2815940e66d4dcfb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522643"
---
# <a name="principals-and-identity-providers"></a>Prinzipale und Identitätsanbieter

Das Kusto-Autorisierungsmodell unterstützt mehrere Identitätsanbieter (IdPs) und mehrere Prinzipaltypen.
In diesem Artikel werden die unterstützten Prinzipaltypen überprüft und ihre Verwendung mit [Rollenzuweisungsbefehlen](../../management/security-roles.md)veranschaulicht.

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) ist Azures bevorzugter mehrinstanzenfähiger Cloudverzeichnisdienst und Identitätsanbieter, der Sicherheitsprinzipale authentifizieren oder sich mit anderen Identitätsanbietern, z. B. Microsofts Active Directory, zusammenschließt.

AAD ist die bevorzugte Methode für die Authentifizierung in Kusto. Diese Lösung unterstützt eine Reihe von Authentifizierungsszenarien:
* **Benutzerauthentifizierung** (interaktive Anmeldung): Dient zur Authentifizierung menschlicher Prinzipale.
* **Anwendungsauthentifizierung** (nicht interaktive Anmeldung): Dient zur Authentifizierung von Diensten und Anwendungen, die ohne Interaktion mit einem menschlichen Benutzer ausgeführt/authentifiziert werden müssen.

>HINWEIS: Azure Active Directory lässt keine Authentifizierung von Dienstkonten zu (die definitionsgemäß ON-Prem AD-Entitäten sind).
Das AAD-Äquivalent des AD-Dienstkontos ist die AAD-Anwendung.

#### <a name="aad-group-principals"></a>AAD-Gruppenleiter
Kusto unterstützt nur Die Prinzipale der Sicherheitsgruppe (und nicht die Prinzipale der Verteilergruppe). Der Versuch, den Zugriff für eine GD in einem Kusto-Cluster einzurichten, führt zu einem Fehler.

#### <a name="aad-tenants"></a>AAD Mieter


>Wenn AAD-Mandant nicht explizit angegeben ist, versucht Kusto, ihn aus dem UPN `johndoe@fabrikam.com`(UniversalPrincipalName, z. B. ) aufzulösen, falls angegeben.
Wenn Ihr Prinzipal die Mandanteninformationen (nicht in UPN-Form) nicht enthält, müssen Sie diese explizit erwähnen, indem Sie die Mandanten-ID oder den Namen an den Hauptdeskriptor anhängen.

**Beispiele für AAD-Prinzipale**

|AAD Mieter |type |Syntax |
|-----------|-----|-------|
|Implizit (UPN)  |Benutzer  |`aaduser=`*UserEmailAddress*
|Explizit (ID)   |Benutzer  |`aaduser=`*UserEmailAddress*`;`*TenantId* oder `aaduser=` *ObjectID*`;`*TenantId*
|Explizit (Name) |Benutzer  |`aaduser=`*UserEmailAddress*`;`*TenantName* oder `aaduser=` *ObjectID*`;`*TenantName*
|Implizit (UPN)  |Group |`aadgroup=`*GroupEmailAddress*
|Explizit (ID)   |Group |`aadgroup=`*GroupObjectId*`;`*TenantId* oder`aadgroup=`*GroupDisplayName*`;`*TenantId*
|Explizit (Name) |Group |`aadgroup=`*GroupObjectId*`;`*TenantName* oder`aadgroup=`*GroupDisplayName*`;`*TenantName*
|Explizit (UPN)  |App   |`aadapp`=*ApplicationDisplayName*`;`*TenantId*
|Explizit (Name) |App   |`aadapp=`*ApplicationId*`;`*TenantName*

```kusto
// No need to specify AAD tenant for UPN, as Kusto performs the resolution by itself
.add database Test users ('aaduser=imikeoein@fabrikam.com') 'Test user (AAD)'

// AAD SG on 'fabrikam.com' tenant
.add database Test users ('aadgroup=SGDisplayName;fabrikam.com') 'Test group @fabrikam.com (AAD)'

// AAD App on 'fabrikam.com' tenant - by tenant name
.add database Test users ('aadapp=4c7e82bd-6adb-46c3-b413-fdd44834c69b;fabrikam.com') 'Test app @fabrikam.com (AAD)'
```

### <a name="microsoft-accounts-msas"></a>Microsoft-Konten (MSAs)
Microsoft-Konten (MSAS) ist der Begriff für alle von Microsoft verwalteten nicht-organisationsbezogenen Benutzerkonten, z `hotmail.com`. `live.com`b `outlook.com`.
Kusto unterstützt die Benutzerauthentifizierung für MSAs. (Hinweis: Es gibt kein Sicherheitsgruppenkonzept.) Die MSAs werden anhand ihres UPN (universeller Prinzipalname) identifiziert.
Wenn ein MSA-Prinzipal für eine Kusto-Ressource konfiguriert ist, versucht Kusto **nicht**, den angegebenen UPN aufzulösen.

**Beispiele für MSA-Prinzipale**

|IdP-  |type  |Syntax |
|-----|------|-------|
|Live.com |Benutzer  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

