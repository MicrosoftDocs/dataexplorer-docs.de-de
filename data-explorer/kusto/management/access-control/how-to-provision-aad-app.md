---
title: Erstellen einer Aad-Anwendung mit Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Erstellen einer Aad-Anwendung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 0816d55cda6d29820084514b0bfec27d726693f9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617968"
---
# <a name="howto-creating-an-aad-application"></a>HOWTO: Erstellen einer Aad-Anwendung

Obwohl die Aad-Benutzerauthentifizierung sehr einfach ist (weil Benutzer in Aad vom Mandanten Administrator definiert sind, z. b. MSIT), ist die Aad-Anwendungs Authentifizierung etwas komplexer. Dies liegt daran, dass es erforderlich ist, die Anwendung mit Aad zu erstellen und zu registrieren. Dieser Vorgang wird unten in einigen Details beschrieben.

Die Aad-Anwendungs Authentifizierung ist nützlich für Anwendungen, die auf Kusto zugreifen müssen, ohne dass ein Benutzer angemeldet oder vorhanden ist (z. b. ein unbeaufsichtigter Dienst oder ein geplanter Flow).

Client Anwendungen und Anwendungen der mittleren Ebene mit interaktivem Benutzer Kontext sollten dieses Modell vermeiden. Dies liegt daran, dass die Autorisierung basierend auf der Aad-Anwendungs Identität anstelle der Benutzeridentität erfolgt. Daher muss die aufrufenden Anwendung ihre eigene Autorisierungs Logik implementieren, um Missbrauch zu verhindern.

## <a name="application-authentication-use-cases"></a>Anwendungsfälle für Anwendungs Authentifizierung

Es gibt zwei Haupt Szenarien, in denen die Anwendungs Authentifizierung verwendet wird:
* Anwendungen, die sich direkt und in Ihrem eigenen Namen an den Kusto-Dienst wenden
* Anwendungen, die Ihre Benutzer bei Kusto authentifizieren (delegierte Authentifizierung)

## <a name="1-provisioning-a-new-application"></a>1. Bereitstellen einer neuen Anwendung

#### <a name="register-the-new-application"></a>Registrieren der neuen Anwendung

1. Melden Sie sich bei [Azure-Portal](https://portal.azure.com) an, `Azure Active Directory` und öffnen Sie das Blatt.

    :::image type="content" source="images/aad-create-app-step-0.png" alt-text="Aad-app erstellen (Schritt 0)":::

1. Wählen `App registrations` Sie aus, und wählen Sie `New application registration`beim Laden des Blatts aus.

    :::image type="content" source="images/aad-create-app-step-1.png" alt-text="Aad-app erstellen, Schritt 1":::

1. Geben Sie die Anwendungsdetails ein:
    * Name
    * Typ: Legen Sie auf fest.`Web app/API`
    * Anmelde-URL: die URL, die Benutzer für den Zugriff auf die Anwendung verwendet. Aad überprüft die URL nicht,<br>
        Sie sind jedoch verpflichtet, einen Wert bereitzustellen. Wenn für die Anwendung keine URL verfügbar ist,<br>
        Geben Sie eine URL ein, die zur Anwendung gehört (z. b. https://<App-CNAME> oder https://<Cloud-Service-Name>. cloudapp.net).<br>
        Sie können sogar einen Wert bereitstellen und fortfahren, wenn die Anwendung noch nicht geschrieben wurde.

    
    :::image type="content" source="images/aad-create-app-step-2.png" alt-text="Aad-app erstellen, Schritt 2":::

1. Sobald Ihre Anwendung bereit ist, öffnen Sie `Settings` das zugehörige Blatt.

    :::image type="content" source="images/aad-create-app-step-3.png" alt-text="Aad erstellen Schritt 3":::

1. Richten Sie `Keys` auf dem Blatt die Authentifizierung für die neue Anwendung ein:
    * Wenn Sie einen gemeinsam verwendeten Schlüssel verwenden möchten, wählen Sie im Dropdown Menü die Option Key Duration aus, und kopieren Sie den Schlüssel, wenn er generiert wird.
        Dieser Schlüssel kann nicht wieder hergestellt werden.
    * Alternativ können Sie ein X509-Zertifikat verwenden, um Ihre Anwendung zu authentifizieren.
        Wählen Sie `Upload Public Key` hierzu die Anweisungen zum Hochladen des öffentlichen Teils des Zertifikats aus, und befolgen Sie die Anweisungen.
    * Vergessen Sie nicht, `Save` Wenn Sie fertig sind.

    :::image type="content" source="images/aad-create-app-step-4.png" alt-text="Aad-app erstellen Schritt 4":::

Ihre Anwendung ist eingerichtet. Wenn Sie lediglich mit der neu erstellten Anwendung auf Kusto zugreifen müssen, sind Sie dabei.

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Delegierte Berechtigungen für die Kusto-Dienst Anwendung einrichten

Wenn Sie die Anwendung benötigen, um die Benutzer-oder App-Authentifizierung für den Kusto-Zugriff ausführen zu können, müssen Sie die Vertrauensstellung zwischen Ihrer Anwendung und Kusto einrichten:

1. Wählen Sie `Required permissions` `Add`auf dem Blatt aus.

    :::image type="content" source="images/aad-create-app-step-5.png" alt-text="Aad-app erstellen Schritt 5":::
   
1. Wählen `Select an API`Sie, `KustoService` geben Sie im Filter Feld ein `KustoService (Kusto)`, und wählen Sie aus.
1. Wenn Ihre Kusto-Cluster MFA erfordern, `KustoMFA` geben Sie in das Filter Feld `KustoServiceMFA (KustoMFA)`ein, und wählen Sie aus.

    :::image type="content" source="images/aad-create-app-step-6.png" alt-text="Aad-app erstellen Schritt 6":::

1. Nachdem Sie Ihre Auswahl bestätigt haben, wählen Sie `Access Kusto`Delegierte Berechtigungen für aus.

   :::image type="content" source="images/aad-create-app-step-7.png" alt-text="Aad-app erstellen, Schritt 7":::

1. Wählen `Done` Sie diese Option, um den Vorgang abzuschließen.


### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Festlegen von Berechtigungen für die Anwendung auf dem Kusto-Cluster

> Vergewissern Sie sich vor der Verwendung der neu bereitgestellten Anwendung, dass Sie von Graph-API aufgelöst wird.<br>
    Es kann mehrere Stunden dauern, bis die Aad-Änderungen weitergegeben wurden.

1. Greifen Sie auf den Datenbankadministrator zu, um der neuen App Berechtigungen zu erteilen.
Ausführliche Informationen zum Festlegen der Berechtigungen finden Sie im Abschnitt [Verwalten von Daten Bank Prinzipale](../security-roles.md) .<br>
Informationen zum Einrichten von Erfassungs Berechtigungen finden Sie unter Erfassungs [Berechtigungen](../../api/netfx/kusto-ingest-client-permissions.md).
1. Fügen Sie im Kusto-Explorer eine Verbindung mit diesem Dienst hinzu, falls noch nicht vorhanden.

### <a name="3-application-can-now-access-kusto"></a>3. die Anwendung kann jetzt auf Kusto zugreifen.

Wenn Sie die neu bereitgestellte Anwendung für den Zugriff auf Kusto-Cluster mithilfe von Kusto-Client Bibliotheken verwenden, geben Sie die folgende Verbindungs Zeichenfolge an (wenn Sie sich für die Authentifizierung mit einem gemeinsam verwendeten Schlüssel entschieden haben):

`https://`*Clusterdnsname*`/;Federated Security=True;Application Client Id=`*applicationclientid*`;Application Key=`*applicationkey*


### <a name="appendix-a-aad-errors"></a>Anhang A: AAD-Fehler

#### <a name="aad-error-aadsts650057"></a>Aad-Fehler AADSTS650057

Wenn Ihre Anwendung zum Authentifizieren von Benutzern oder Anwendungen für den Kusto-Zugriff verwendet wird, müssen Sie Delegierte Berechtigungen für die Kusto-Dienst Anwendung einrichten, d. h., dass Ihre Anwendung Benutzer oder Anwendungen für den Kusto-Zugriff authentifizieren kann.
Wenn dies nicht geschieht, führt dies zu einem Fehler, der dem folgenden ähnelt, wenn ein Authentifizierungsversuch erfolgt:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Befolgen Sie die Anweisungen unter [Einrichten von Delegierten Berechtigungen für die Kusto-Dienst Anwendung](#set-up-delegated-permissions-for-kusto-service-application).

#### <a name="enable-user-consent-if-needed"></a>Benutzer Zustimmung bei Bedarf aktivieren

Ihr Aad-Mandanten Administrator kann eine Richtlinie implementieren, die verhindert, dass Mandanten Benutzer Anwendungen zustimmen. Dies führt zu einem Fehler, der dem folgenden Beispiel ähnelt, wenn ein Benutzer versucht, sich bei Ihrer Anwendung anzumelden:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Sie müssen ihren Aad-Administrator bitten, die Zustimmung für alle Benutzer im Mandanten zu erteilen oder die Zustimmung des Benutzers für Ihre bestimmte Anwendung zu aktivieren.

### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>Anhang B: Weiterführende Themen und Problembehandlung

* Eine vollständige Referenz der unterstützten Verbindungs Zeichenfolgen finden Sie in der Dokumentation zu [Kusto](../../api/connection-strings/kusto.md)
