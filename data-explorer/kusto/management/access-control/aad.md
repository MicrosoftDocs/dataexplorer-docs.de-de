---
title: Kusto-Azure Active Directory Authentifizierung (AAD)-Azure Daten-Explorer
description: In diesem Artikel wird die Azure Active Directory Authentifizierung (AAD) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: a511f96df09fa9ce4089a0af77ebbe09269c9b44
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343435"
---
# <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD)-Authentifizierung

Azure Active Directory (AAD) ist der bevorzugte, mehrinstanzenfähige Cloudverzeichnisdienst von Azure. Er kann Sicherheitsprinzipale authentifizieren sowie im Verbund mit anderen Identitätsanbietern (beispielsweise Active Directory von Microsoft) verwendet werden.

Aad ermöglicht die Verwendung von verschiedenen Arten (Webanwendung, Windows-Desktop Anwendung, universelle Anwendungen, Mobile Anwendungen usw.), um Kusto-Dienste einheitlich zu authentifizieren und zu verwenden.

Aad unterstützt eine Reihe von Authentifizierungs Szenarien.
Wenn während der Authentifizierung ein Benutzer vorhanden ist, muss der Benutzer von der Aad-Benutzerauthentifizierung bei Aad authentifiziert werden.
In einigen Fällen möchte ein Dienst auch dann Kusto verwenden, wenn kein Benutzer interaktiv vorhanden ist. In solchen Fällen sollte die Anwendung durch die Verwendung eines geheimen Anwendungs Schlüssels authentifiziert werden, wie in der Aad-Anwendungs Authentifizierung beschrieben.

Die folgenden Authentifizierungsmethoden werden von Kusto im Allgemeinen unterstützt, einschließlich der .NET-Bibliotheken:

* Interaktive Benutzerauthentifizierung: Dieser Modus erfordert Interaktivität, wie bei Bedarf, wird die Anmelde Benutzeroberfläche angezeigt.
* Benutzerauthentifizierung mit einem vorhandenen Aad-Token, das zuvor für Kusto ausgegeben wurde
* Anwendungs Authentifizierung mit AppID und einem gemeinsamen geheimen Schlüssel
* Anwendungs Authentifizierung mit lokal installiertem X. 509v2-Zertifikat oder bereitgestellter Zertifikat
* Anwendungs Authentifizierung mit einem vorhandenen Aad-Token, das zuvor für Kusto ausgegeben wurde
* Benutzer-oder Anwendungs Authentifizierung mit einem Aad-Token, das für eine andere Ressource ausgegeben wurde, weil eine Vertrauensstellung zwischen dieser Ressource und Kusto vorhanden ist

Anleitungen und Beispiele finden Sie in der Referenz zu [Kusto-Verbindungs](../../api/connection-strings/kusto.md) Zeichenfolgen.

## <a name="user-authentication"></a>Benutzerauthentifizierung

Die Benutzerauthentifizierung erfolgt, wenn der Benutzer Anmeldeinformationen an AAD (oder an einen anderen Identitätsanbieter, der einen Verbund mit AAD bildet – etwa AD FS) übergibt und daraufhin ein Sicherheitstoken erhält, das an den Kusto-Dienst übergeben werden kann. Für den Kusto-Dienst spielt es keine Rolle, auf welche Weise das Sicherheitstoken bezogen wurde. Er überprüft lediglich, ob das Token gültig ist und welche Informationen von AAD (oder von dem Verbundidentitätsanbieter) angegeben wurden.

Auf der Clientseite unterstützt Kusto die interaktive Authentifizierung, bei der der Benutzer von der AAD-Clientbibliothek ADAL oder von einem ähnlichen Code zur Eingabe von Anmeldeinformationen aufgefordert wird. Kusto unterstützt auch die tokenbasierte Authentifizierung, bei der die Anwendung, die Kusto verwendet, ein gültiges Benutzertoken bezieht und übergibt. Darüber hinaus wird ein Szenario unterstützt, bei dem die Anwendung, die Kusto verwendet, ein gültiges Benutzertoken für einen anderen Dienst (nicht Kusto) bezieht. Hierzu muss allerdings eine Vertrauensbeziehung zwischen dieser Ressource und Kusto bestehen.

Ausführliche Informationen zur Verwendung der Kusto-Client Bibliotheken und zum Authentifizieren mit Aad und Kusto finden Sie unter [Kusto-VerbindungsZeichenfolgen](../../api/connection-strings/kusto.md).

## <a name="application-authentication"></a>Anwendungsauthentifizierung

Wenn Anforderungen keinem bestimmten Benutzer zugeordnet sind oder kein Benutzer zur Eingabe von Anmeldeinformationen verfügbar ist, kann die AAD-Anwendungsauthentifizierung verwendet werden. Dabei übergibt die Anwendung ein Geheimnis an AAD (oder an den Verbundidentitätsanbieter), um sich zu authentifizieren. Von den verschiedenen Kusto-Clients werden folgende Szenarien unterstützt:

* Anwendungsauthentifizierung unter Verwendung eines lokal installierten X.509v2-Zertifikats
* Anwendungsauthentifizierung unter Verwendung eines X.509v2-Zertifikats, das als Bytestream an die Clientbibliothek übergeben wird
* Anwendungsauthentifizierung unter Verwendung einer AAD-Anwendungs-ID und eines AAD-Anwendungsschlüssels (gewissermaßen die Authentifizierung mit Benutzername/Kennwort für Anwendungen)
* Anwendungsauthentifizierung mit einem zuvor bezogenen gültigen AAD-Token (ausgestellt für Kusto)
* Anwendungsauthentifizierung mit einem zuvor bezogenen gültigen AAD-Token, das für eine andere Ressource ausgestellt wurde – vorausgesetzt, es besteht eine Vertrauensbeziehung zwischen dieser Ressource und Kusto

## <a name="aad-server-application-permissions"></a>Aad-Server Anwendungs Berechtigungen

Im Allgemeinen kann eine Aad-Server Anwendung mehrere Berechtigungen (z. b. schreibgeschützte Berechtigungen und Lese-/Schreibberechtigungen) definieren, und die Aad-Client Anwendung entscheidet möglicherweise, welche Berechtigungen Sie benötigt, wenn Sie ein Autorisierungs Token anfordert. Im Rahmen der tokenübernahme wird der Benutzer aufgefordert, die Aad-Client Anwendung zu autorisieren, damit Sie im Auftrag des Benutzers agieren kann, um diese Berechtigungen zu erteilen. Wenn der Benutzer eine Genehmigung durchführt, werden diese Berechtigungen im Bereichs Anspruch des Tokens aufgeführt, das für die Aad-Client Anwendung ausgegeben wird.



Die Aad-Client Anwendung ist so konfiguriert, dass Sie die Berechtigung "Zugriff auf Kusto" für den Benutzer anfordert (die Aad "den Ressourcen Besitzer" aufruft).

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>Kusto Client SDK als AAD-Clientanwendung

Wenn die Kusto-Clientbibliotheken ADAL (die AAD-Clientbibliothek) aufrufen, um ein Token für die Kommunikation mit Kusto zu beziehen, werden folgende Informationen angegeben:

1. Der Aad-Mandant, wie er vom Aufrufer empfangen wurde.
2. Die ID der AAD-Clientanwendung
3. Die Ressourcen-ID für den Aad-Client
4. Die Aad replyurl (die URL, an die der Aad-Dienst nach dem erfolgreichen Abschluss der Authentifizierung umgeleitet wird; Adal erfasst diese Umleitung und extrahiert den Autorisierungs Code aus der Anwendung.
5. Der Cluster-URI (' https://Cluster-and-region.kusto.windows.net ').

Das Token, das von Adal an die Kusto-Client Bibliothek zurückgegeben wird, verfügt über die Kusto Aad-Server Anwendung als Zielgruppe und über die Berechtigung "Zugriff auf Kusto" als Bereich.

## <a name="authenticating-with-aad-programmatically"></a>Programm gesteuertes Authentifizieren mit Aad

In den folgenden Artikeln wird erläutert, wie Sie Programm gesteuert bei Kusto mit Aad authentifiziert werden:

* [Bereitstellen einer Aad-Anwendung](../../../provision-azure-ad-app.md)
* [Vorgehensweise beim Ausführen der Aad-Authentifizierung](./how-to-authenticate-with-aad.md)