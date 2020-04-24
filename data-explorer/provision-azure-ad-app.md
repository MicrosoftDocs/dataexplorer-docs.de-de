---
title: Erstellen einer Azure AD-Anwendung in Azure Data Explorer
description: Erfahren Sie, wie Sie eine Azure AD Anwendung in Azure Data Explorer erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 7bbb8064cd71e61b6fdfb1205e783099ec21c687
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494258"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Erstellen einer Azure Active Directory-Anwendungsregistrierung in Azure Data Explorer

Azure Active Directory-Anwendungsauthentifizierung (Azure AD) wird für Anwendungen verwendet (z. B. für einen unbeaufsichtigten Dienst oder einen geplanten Flow), die auf Azure Data Explorer zugreifen müssen, ohne dass ein Benutzer vorhanden ist. Wenn Sie eine Verbindung mit einer Azure Data Explorer-Datenbank herstellen, indem Sie eine Anwendung wie eine Web-App verwenden, sollten Sie sich mit Dienstprinzipalauthentifizierung authentifizieren. In diesem Artikel wird ausführlich beschrieben, wie ein Azure AD-Dienstprinzipal erstellt und registriert und anschließend für den Zugriff auf eine Azure Data Explorer-Datenbank autorisiert wird.

## <a name="create-azure-ad-application-registration"></a>Erstellen einer Azure AD-Anwendungsregistrierung

Für Azure AD-Anwendungsauthentifizierung muss eine Anwendung mit Azure AD erstellt und registriert werden. Ein Dienstprinzipal wird automatisch erstellt, wenn die Anwendungsregistrierung in einem Azure AD-Mandanten erstellt wird. 

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und öffnen Sie das Blatt `Azure Active Directory`.

    ![Auswählen von „Azure Active Directory“ im Menü des Portals](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Wählen Sie das Blatt **App-Registrierungen** aus, und wählen Sie dann **Neue Registrierung** aus.

    ![Starten einer neuen App-Registrierung](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Geben Sie die folgenden Informationen ein: 

    * **Name** 
    * **Unterstützte Kontotypen**
    * **Umleitungs-URI** > **Web**
        > [!IMPORTANT] 
        > Der Anwendungstyp sollte **Web** sein. Der URI ist optional und wird in diesem Fall leer gelassen.
    * Wählen Sie **Registrieren** aus.

    ![Registrieren der neuen App-Registrierung](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Wählen Sie das Blatt **Übersicht** aus, und kopieren Sie die **Anwendungs-ID**.

    > [!NOTE]
    > Sie benötigen die Anwendungs-ID, um den Dienstprinzipal für den Zugriff auf die Datenbank zu autorisieren.

    ![Kopieren der App-Registrierungs-ID](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. Wählen Sie auf dem Blatt **Zertifikate und Geheimnisse** die Option **Neuer geheimer Clientschlüssel** aus.

    ![Starten der Erstellung des geheimen Clientschlüssels](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > In diesem Artikel wird die Verwendung eines geheimen Clientschlüssels für die Anmeldeinformationen der Anwendung beschrieben.  Sie können auch ein X509-Zertifikat verwenden, um Ihre Anwendung zu authentifizieren. Wählen Sie **Zertifikat hochladen** aus, und befolgen Sie die Anweisungen, um den öffentlichen Teil des Zertifikats hochzuladen.

1. Geben Sie eine Beschreibung und den Ablauf ein, und wählen Sie dann **Hinzufügen** aus.

    ![Eingeben der Parameter für den geheimen Clientschlüssel](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Kopieren Sie den Schlüsselwert.

    > [!NOTE]
    > Wenn Sie diese Seite verlassen, kann auf den Schlüsselwert nicht zugegriffen werden.  Sie benötigen den Schlüssel, um die Clientanmeldeinformationen für die Datenbank zu konfigurieren.

    ![Kopieren des Werts des geheimen Clientschlüssels](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Ihre Anwendung wird erstellt. Wenn Sie nur Zugriff auf eine autorisierte Azure Data Explorer-Ressource benötigen (wie im folgenden Programmbeispiel), überspringen Sie den nächsten Abschnitt. Unterstützung für delegierte Berechtigungen finden Sie unter [Konfigurieren von delegierten Berechtigungen für die Anwendungsregistrierung](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Konfigurieren von delegierten Berechtigungen für die Anwendungsregistrierung

Wenn Ihre Anwendung mit den Anmeldeinformationen des aufrufenden Benutzers auf Azure Data Explorer zugreifen muss, konfigurieren Sie delegierte Berechtigungen für Ihre Anwendungsregistrierung. Beispielsweise, wenn Sie eine Web-API für den Zugriff auf Azure Data Explorer erstellen und sich mit den Anmeldeinformationen des Benutzers authentifizieren möchten, der Ihre API *aufruft*.  

1. Wählen Sie auf dem Blatt **API-Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Wählen Sie **Von meiner Organisation verwendete APIs** aus. Suchen Sie nach **Azure Data Explorer**, und wählen Sie diese Option aus.

    ![Hinzufügen einer Azure Data Explorer-API-Berechtigung](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. Wählen Sie in **Delegierte Berechtigungen** das Feld **user_impersonation** und dann **Berechtigungen hinzufügen** aus.

    ![Auswählen von delegierten Berechtigungen mit Benutzeridentitätswechsel](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Gewähren des Zugriffs auf eine Azure Data Explorer-Datenbank für den Dienstprinzipal

Nachdem nun die Anwendungsregistrierung Ihres Dienstprinzipals erstellt würde, müssen Sie dem entsprechenden Dienstprinzipal Zugriff auf Ihre Azure Data Explorer-Datenbank gewähren. 

1. Stellen Sie in der [Webbenutzeroberfläche](https://dataexplorer.azure.com/) eine Verbindung mit Ihrer Datenbank her, und öffnen Sie eine Abfrageregisterkarte.

1. Führen Sie den folgenden Befehl aus:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Beispiel:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Der letzte Parameter ist eine Zeichenfolge, die als Notizen angezeigt wird, wenn Sie die einer Datenbank zugeordneten Rollen abfragen.
    
    > [!NOTE]
    > Nachdem die Anwendungsregistrierung erstellt wurde, kann es einige Minuten dauern, bis Azure Data Explorer darauf verweisen kann. Wenn Sie beim Ausführen des Befehls eine Fehlermeldung erhalten, dass die Anwendung nicht gefunden wurde, warten Sie, und wiederholen Sie den Vorgang dann.

Weitere Informationen finden Sie unter [Verwaltung von Sicherheitsrollen](kusto/management/security-roles.md) und [Datenerfassungsberechtigungen](kusto/api/netfx/kusto-ingest-client-permissions.md).  

## <a name="using-application-credentials-to-access-a-database"></a>Verwenden von Anwendungsanmeldeinformationen für den Zugriff auf eine Datenbank

Verwenden Sie die Anwendungsanmeldeinformationen, um programmgesteuert auf Ihre Datenbank zuzugreifen, indem Sie die [Azure Data Explorer-Clientbibliothek](kusto/api/netfx/about-kusto-data.md) verwenden.

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Geben Sie die Anwendungs-ID und den Schlüssel der zuvor erstellten Anwendungsregistrierung (Dienstprinzipal) an.

Weitere Informationen finden Sie unter [Authentifizieren mit Azure AD für Azure Data Explorer-Zugriff](kusto/management/access-control/how-to-authenticate-with-aad.md) und [Verwenden von Azure Key Vault mit einer .NET Core-Web-App](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="invalid-resource-error"></a>Fehler „Ungültige Ressource“

Wenn Ihre Anwendung zum Authentifizieren von Benutzern oder Anwendungen für den Azure Data Explorer-Zugriff verwendet wird, müssen Sie delegierte Berechtigungen für die Azure Data Explorer- Dienstanwendung einrichten. Deklarieren Sie, dass Ihre Anwendung Benutzer oder Anwendungen für den Azure Data Explorer-Zugriff authentifizieren kann. Wenn dies nicht geschieht, führt dies zu einem Fehler, der dem folgenden ähnelt, wenn ein Authentifizierungsversuch erfolgt:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Sie müssen die Anweisungen unter [Einrichten von delegierten Berechtigungen für die Azure Data Explorer-Dienstanwendung](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Fehler „Benutzereinwilligung aktivieren“

Der Azure AD-Mandantenadministrator kann eine Richtlinie implementieren, die verhindert, dass Mandantenbenutzer ihre Einwilligung für Anwendungen geben. Diese Situation führt zu einem Fehler, der dem folgenden ähnelt, wenn ein Benutzer versucht, sich bei Ihrer Anwendung anzumelden:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Sie müssen sich an Ihren Azure AD-Administrator wenden, um Einwilligung für alle Benutzer im Mandanten zu erteilen oder Benutzereinwilligung für Ihre spezifische Anwendung zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte

* Eine Liste der unterstützten Verbindungszeichenfolgen finden Sie unter [Kusto-Verbindungszeichenfolgen](kusto/api/connection-strings/kusto.md).
