---
title: Azure Active Directory (AAD)-Authentifizierung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Azure Active Directory (AAD)-Authentifizierung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: 637252b91af53198b3ee494309857b2d4b6e6828
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522932"
---
# <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD)-Authentifizierung

Azure Active Directory (AAD) ist der bevorzugte, mehrinstanzenfähige Cloudverzeichnisdienst von Azure. Er kann Sicherheitsprinzipale authentifizieren sowie im Verbund mit anderen Identitätsanbietern (beispielsweise Active Directory von Microsoft) verwendet werden.

AAD ermöglicht die Anwendung verschiedener Arten (Webanwendung, Windows-Desktopanwendung, universelle Anwendungen, mobile Anwendungen usw.), um Kusto-Dienste gleichmäßig zu authentifizieren und zu nutzen.

AAD unterstützt eine Reihe von Authentifizierungsszenarien.
Wenn während der Authentifizierung ein Benutzer vorhanden ist, sollte der Benutzer durch AAD-Benutzerauthentifizierung bei AAD authentifiziert werden.
In einigen Fällen möchte man, dass ein Dienst Kusto auch dann nutzt, wenn kein Benutzer interaktiv anwesend ist. In solchen Fällen sollte man die Anwendung mithilfe eines Anwendungsgeheimnisses authentifizieren, wie in AAD Application Authentication beschrieben.

Die folgenden Authentifizierungsmethoden werden von Kusto im Allgemeinen unterstützt, auch über seine .NET-Bibliotheken:

* Interaktive Benutzerauthentifizierung – Dieser Modus erfordert Interaktivität, bei Bedarf wird die Anmeldebenutzeroberfläche angezeigt
* Benutzerauthentifizierung mit einem vorhandenen AAD-Token, das zuvor für Kusto ausgegeben wurde
* Anwendungsauthentifizierung mit AppID und shared Secret
* Anwendungsauthentifizierung mit lokal installiertem X.509v2-Zertifikat oder -Zertifikat inline bereitgestellt
* Anwendungsauthentifizierung mit einem vorhandenen AAD-Token, das zuvor für Kusto ausgegeben wurde
* Benutzer- oder Anwendungsauthentifizierung mit einem AAD-Token, das für eine andere Ressource ausgestellt wurde, sofern zwischen dieser Ressource und Kusto eine Vertrauensstellung besteht

Hinweise und Beispiele finden Sie in der [Kusto-Verbindungszeichenfolgenreferenz.](../../api/connection-strings/kusto.md)

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

## <a name="aad-server-application-permissions"></a>AAD Server-Anwendungsberechtigungen

Im allgemeinen Fall kann eine AAD-Serveranwendung mehrere Berechtigungen definieren (z. B. schreibgeschützte Und eine Leseschreibberechtigung), und die AAD-Clientanwendung kann entscheiden, welche Berechtigungen sie benötigt, wenn sie ein Autorisierungstoken anfordert. Im Rahmen der Tokenerfassung wird der Benutzer aufgefordert, die AAD-Clientanwendung zu autorisieren, im Namen des Benutzers mit der Berechtigung zu handeln, diese Berechtigungen zu besitzen. Sollte der Benutzer zustimmen, werden diese Berechtigungen im Gültigkeitsbereich des Tokens aufgeführt, das für die AAD-Clientanwendung ausgestellt wird.



Die AAD-Clientanwendung ist so konfiguriert, dass sie die Berechtigung "Access Kusto" vom Benutzer anfordert (die AAD als "Ressourcenbesitzer" bezeichnet).

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>Kusto Client SDK als AAD-Clientanwendung

Wenn die Kusto-Clientbibliotheken ADAL (die AAD-Clientbibliothek) aufrufen, um ein Token für die Kommunikation mit Kusto zu beziehen, werden folgende Informationen angegeben:

1. Der AAD-Mieter, wie vom Anrufer empfangen
2. Die ID der AAD-Clientanwendung
3. Die AAD-Clientressourcen-ID
4. Die AAD ReplyUrl (die URL, zu der der AAD-Dienst nach abschluss der Authentifizierung umgeleitet wird; ADAL erfasst dann diese Umleitung und extrahiert den Autorisierungscode daraus).
5. Der Cluster-URIhttps://Cluster-and-region.kusto.windows.net(' ').

Das Token, das von ADAL an die Kusto-Clientbibliothek zurückgegeben wird, hat die Kusto AAD Server-Anwendung als Zielgruppe und die Berechtigung "Zugriff kusto" als Bereich.

## <a name="authenticating-with-aad-programmatically"></a>Authentifizierung mit AAD Programmatisch

In den folgenden Artikeln wird erläutert, wie Sie Kusto programmgesteuert mit AAD authentifizieren:

* [Bereitstellen einer AAD-Anwendung](./how-to-provision-aad-app.md)
* [So führen Sie die AAD-Authentifizierung durch](./how-to-authenticate-with-aad.md)

