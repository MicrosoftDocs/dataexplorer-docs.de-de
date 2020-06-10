---
title: 'Prinzipale und Identitäts Anbieter: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt Prinzipale und Identitäts Anbieter in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e34c724799cffe38db93869e96fcfae83a92b55
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664992"
---
# <a name="principals-and-identity-providers"></a>Prinzipale und Identitäts Anbieter

Das Kusto-Autorisierungs Modell unterstützt mehrere Identitäts Anbieter (IDPs) und mehrere Prinzipal Typen.
In diesem Artikel werden die unterstützten Prinzipal Typen überprüft und ihre Verwendung mit [Rollen Zuweisungs Befehlen](../../management/security-roles.md)veranschaulicht.

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) ist der bevorzugte mehr Instanzen fähige cloudverzeichnisdienst und Identitäts Anbieter von Azure, der die Sicherheits Prinzipale oder den Verbund mit anderen Identitäts Anbietern, wie z. b. dem Active Directory von Microsoft, authentifizieren kann.

Aad ist die bevorzugte Methode für die Authentifizierung bei Kusto. Diese Lösung unterstützt eine Reihe von Authentifizierungsszenarien:
* **Benutzerauthentifizierung** (interaktive Anmeldung): Dient zur Authentifizierung menschlicher Prinzipale.
* **Anwendungsauthentifizierung** (nicht interaktive Anmeldung): Dient zur Authentifizierung von Diensten und Anwendungen, die ohne Interaktion mit einem menschlichen Benutzer ausgeführt/authentifiziert werden müssen.

> [!NOTE]
> Azure Active Directory lässt keine Authentifizierung von Dienst Konten zu (die durch lokale Ad-Entitäten definiert werden).
Die Aad-Entsprechung des AD-Dienst Kontos ist die Aad-Anwendung.

#### <a name="aad-group-principals"></a>Aad-Gruppen Prinzipale
Kusto unterstützt nur Sicherheitsgruppen Prinzipale (und nicht Verteiler Gruppen). Der Versuch, den Zugriff für eine GD in einem Kusto-Cluster einzurichten, führt zu einem Fehler.

#### <a name="aad-tenants"></a>Aad-Mandanten

Wenn der Aad-Mandant nicht explizit angegeben wird, versucht Kusto, ihn aus dem UPN (universalprincipalname, z. b.) aufzulösen, `johndoe@fabrikam.com` Falls angegeben. Wenn Ihr Prinzipal die Mandanten Informationen nicht enthält (nicht im UPN-Format), müssen Sie ihn explizit erwähnen, indem Sie die Mandanten-ID oder den Namen an den Prinzipal Deskriptor anhängen.

**Beispiele für Aad-Prinzipale**

|Aad-Mandant |type |Syntax |
|-----------|-----|-------|
|Implizit (UPN)  |Benutzer  |`aaduser=`*Useremailaddress*
|Explizit (ID)   |Benutzer  |`aaduser=`*Useremailaddress* `;` *Tenantid* oder `aaduser=` *objectID* `;` *tenantid*
|Explizit (Name) |Benutzer  |`aaduser=`*Useremailaddress* `;` *Tenantname* oder `aaduser=` *objectID* `;` *tenantname*
|Implizit (UPN)  |Gruppieren |`aadgroup=`*Grouetmailaddress*
|Explizit (ID)   |Gruppieren |`aadgroup=`*Groupobjectid* `;` *Tenantid* oder `aadgroup=` *groupdisplayname* `;` *tenantid*
|Explizit (Name) |Gruppieren |`aadgroup=`*Groupobjectid* `;` *Tenantname* oder `aadgroup=` *groupdisplayname* `;` *tenantname*
|Explizit (UPN)  |App   |`aadapp`=*ApplicationDisplayName* `;` *Tenantid*
|Explizit (Name) |App   |`aadapp=`*ApplicationId* `;` *Tenantname*

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

|IdP  |type  |Syntax |
|-----|------|-------|
|Live.com |Benutzer  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

