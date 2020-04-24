---
title: HowTo - Erstellen einer AAD-Anwendung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird HowTo - Erstellen einer AAD-Anwendung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 91d646d3bd0922f9daf9e8caec6fa6be5e32e2b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522898"
---
# <a name="howto-creating-an-aad-application"></a>HowTo: Erstellen einer AAD-Anwendung

Während die AAD-Benutzerauthentifizierung sehr einfach ist (da Benutzer in AAD vom Mandantenadministrator wie MSIT definiert werden), ist die AAD-Anwendungsauthentifizierung etwas komplexer. Dies liegt daran, dass Sie die Anwendung bei AAD erstellen und registrieren müssen. Dieser Prozess wird im Folgenden in einigen Details beschrieben.

Die AAD-Anwendungsauthentifizierung ist nützlich für Anwendungen, die auf Kusto zugreifen müssen, ohne dass ein Benutzer angemeldet oder vorhanden ist (z. B. ein unbeaufsichtigter Dienst oder ein geplanter Flow).

Clientanwendungen und Anwendungen der mittleren Ebene mit interaktivem Benutzerkontext sollten dieses Modell vermeiden. Dies liegt daran, dass die Autorisierung basierend auf der AAD-Anwendungsidentität anstelle der Benutzeridentität ausgeführt wird, sodass die aufrufende Anwendung eine eigene Autorisierungslogik implementieren muss, um Missbrauch zu verhindern.

## <a name="application-authentication-use-cases"></a>Anwendungsanwendungsfälle für die Anwendungsauthentifizierung

Es gibt zwei Hauptszenarien, die die Anwendungsauthentifizierung verwenden:
* Anwendungen, die sich direkt und in ihrem eigenen Namen an den Kusto-Dienst wenden
* Anwendungen, die ihre Benutzer bei Kusto authentifizieren (delegierte Authentifizierung)

## <a name="1-provisioning-a-new-application"></a>1. Bereitstellung einer neuen Anwendung

#### <a name="register-the-new-application"></a>Registrieren der neuen Anwendung

1. Melden Sie sich beim `Azure Active Directory` [Azure-Portal](https://portal.azure.com) an, und öffnen Sie das Blatt.

    ![alt text](./images/Aad-create-app-step-0.png "Aad-create-app-step-0")

1. Wählen `App registrations` Sie aus, und `New application registration`wenn die Klinge geladen wird, wählen Sie .

    ![alt text](./images/Aad-create-app-step-1.png "Aad-create-app-schritt-1")

1. Füllen Sie die Anwendungsdetails aus:
    * name
    * Typ: auf`Web app/API`
    * Anmelde-URL: die URL, die von Benutzern für den Zugriff auf die Anwendung verwendet wird. AAD überprüft die URL nicht,<br>
        aber mandatiert, dass Sie einen Wert angeben. Wenn die Anwendung also keine URL hat, auf die Benutzer zugreifen können,<br>
        Geben Sie eine URL ein, die zur Anwendung gehört (z. B. https://<APP-CNAME> oder https://<CLOUD-SERVICE-NAME>.cloudapp.net).<br>
        Sie können sogar einen Wert angeben und fortfahren, wenn die Anwendung noch nicht geschrieben wurde.

    ![alt text](./images/Aad-create-app-step-2.png "Aad-create-app-schritt-2")

1. Sobald Ihre Anwendung fertig `Settings` ist, öffnen Sie die Klinge.

    ![alt text](./images/Aad-create-app-step-3.png "Aad-create-app-schritt-3")

1. Richten `Keys` Sie auf dem Blatt die Authentifizierung für Ihre neue Anwendung ein:
    * Wenn Sie einen freigegebenen Schlüssel verwenden möchten, wählen Sie die Schlüsseldauer aus dem Dropdown-Menü aus, und kopieren Sie den Schlüssel, wenn er generiert wird.
        Sie können diesen Schlüssel nicht wiederherstellen.
    * Alternativ können Sie ein X509-Zertifikat verwenden, um Ihre Anwendung zu authentifizieren.
        Wählen Sie `Upload Public Key` dazu die Anweisungen zum Hochladen des öffentlichen Teils des Zertifikats aus und folgen Sie ihnen.
    * Vergessen Sie nicht, `Save` auszuwählen, wann Sie fertig sind.

    ![alt text](./images/Aad-create-app-step-4.png "Aad-create-app-schritt-4")

Ihre Anwendung ist eingerichtet. Wenn Sie nur Zugriff auf Kusto mit der neu erstellten Anwendung benötigen, sind Sie fertig.

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Einrichten delegierter Berechtigungen für die Kusto-Dienstanwendung

Wenn Sie die Anwendung benötigen, um benutzer- oder App-Authentifizierung für kusto-Zugriff durchführen zu können, müssen Sie die Vertrauensstellung zwischen Ihrer Anwendung und Kusto einrichten:

1. Wählen `Required permissions` Sie `Add`auf der Klinge aus.

    ![alt text](./images/Aad-create-app-step-5.png "Aad-create-app-schritt-5")

1. Wählen `Select an API`Sie `KustoService` , geben Sie `KustoService (Kusto)`in das Filterfeld ein und wählen Sie .
1. Wenn Ihre Kusto-Cluster MFA `KustoMFA` benötigen, geben `KustoServiceMFA (KustoMFA)`Sie in das Filterfeld ein und wählen Sie aus.

    ![alt text](./images/Aad-create-app-step-6.png "Aad-create-app-schritt-6")

1. Nachdem Sie Ihre Auswahl bestätigt `Access Kusto`haben, wählen Sie delegierte Berechtigungen für aus.

    ![alt text](./images/Aad-create-app-step-7.png "Aad-create-app-schritt-7")

1. Wählen `Done` Sie diese Option aus, um den Vorgang abzuschließen.



### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Festlegen von Berechtigungen für die Anwendung auf Kusto Cluster

> Bevor Sie Ihre neu bereitgestellte Anwendung verwenden, überprüfen Sie, ob sie aus der Graph-API auflöst.<br>
    Es kann bis zu mehreren Stunden dauern, bis die AAD-Änderungen weitergegeben werden.

1. Greifen Sie auf Ihren Datenbankadministrator zu, um berechtigungen für die neue App zu erteilen.
Im Abschnitt [Verwalten von Datenbankprinzipalen](../security-roles.md) finden Sie Details zum Festlegen der Berechtigungen.<br>
Informationen zum Einrichten von Aufnahmeberechtigungen finden Sie unter [Abstellberechtigungen](../../api/netfx/kusto-ingest-client-permissions.md).
1. Fügen Sie diesem Dienst in Ihrem Kusto Explorer eine Verbindung hinzu, wenn Sie ihn noch nicht haben.

### <a name="3-application-can-now-access-kusto"></a>3. Anwendung kann jetzt auf Kusto zugreifen

Wenn Sie Ihre neu bereitgestellte Anwendung für den Zugriff auf Kusto-Cluster mithilfe von Kusto-Clientbibliotheken verwenden, geben Sie die folgende Verbindungszeichenfolge an (wenn Sie sich mit einem freigegebenen Schlüssel authentifizieren möchten):

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*ApplicationClientId*`;Application Key=`*ApplicationKey*


### <a name="appendix-a-aad-errors"></a>Anhang A: AAD-Fehler

#### <a name="aad-error-aadsts650057"></a>AAD-Fehler AADSTS650057

Wenn Ihre Anwendung zum Authentifizieren von Benutzern oder Anwendungen für den Kusto-Zugriff verwendet wird, müssen Sie delegierte Berechtigungen für die Kusto-Dienstanwendung einrichten, d. h. deklarieren, dass Ihre Anwendung Benutzer oder Anwendungen für den Kusto-Zugriff authentifizieren kann.
Wenn dies nicht geschieht, führt dies zu einem Fehler, der dem folgenden ähnelt, wenn ein Authentifizierungsversuch erfolgt:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Sie müssen die Anweisungen zum [Einrichten delegierter Berechtigungen für die Kusto-Dienstanwendung](#set-up-delegated-permissions-for-kusto-service-application)befolgen.

#### <a name="enable-user-consent-if-needed"></a>Aktivieren der Benutzerzustimmung bei Bedarf

Ihr AAD-Mandantenadministrator kann eine Richtlinie erlassen, die Mandantenbenutzer daran hindert, Anwendungen ihre Zustimmung zu erteilen. Dies führt zu einem ähnlichen Fehler wie dem folgenden, wenn ein Benutzer versucht, sich bei Ihrer Anwendung anzumelden:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Sie müssen Ihren AAD-Administrator bitten, allen Benutzern im Mandanten die Zustimmung zu erteilen oder die Benutzerzustimmung für Ihre spezifische Anwendung zu aktivieren.



### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>Anhang B: Erweiterte Themen und Fehlerbehebung

* Weitere Informationen zu unterstützten Verbindungszeichenfolgen finden Sie in der Dokumentation zu [Kusto-Verbindungszeichenfolgen](../../api/connection-strings/kusto.md)
