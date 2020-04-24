---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492374"
---
## <a name="configure-the-data-source"></a>Konfigurieren der Datenquelle

Sie führen die folgenden Schritte aus, um den Azure Data Explorer als Datenquelle für Ihr Dashboardtool zu konfigurieren. Diese Schritte werden in diesem Abschnitt näher erläutert:

1. Erstellen Sie einen Azure Active Directory-Dienstprinzipal (Azure AD). Der Dienstprinzipal wird von Ihrem Dashboardtool für den Zugriff auf den Azure Data Explorer-Dienst verwendet.

1. Fügen Sie den Azure AD-Dienstprinzipal der Rolle *Betrachter* in der Azure Data Explorer-Datenbank hinzu.

1. Geben Sie die Verbindungseigenschaften Ihres Dashboardtools basierend auf Informationen des Azure AD-Dienstprinzipals an, und testen Sie die Verbindung.

### <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Sie können den Dienstprinzipal im [Azure-Portal](#azure-portal) oder mit der [Azure-Befehlszeilenschnittstelle](#azure-cli) erstellen. Unabhängig von der verwendeten Methode erhalten Sie nach der Erstellung Werte für vier Verbindungseigenschaften, die Sie in späteren Schritten verwenden.

#### <a name="azure-portal"></a>Azure-Portal

1. Befolgen Sie zum Erstellen des Dienstprinzipals die Anweisungen in der [Dokumentation zum Azure-Portal](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Weisen Sie im Abschnitt [Zuweisen der Anwendung zu einer Rolle](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) den Rollentyp **Leser** zu Ihrem Azure Data Explorer-Cluster zu.

    1. Kopieren Sie im Abschnitt [Abrufen von Werten für die Anmeldung](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) die drei in den Schritten behandelten Eigenschaftswerte: **Verzeichnis-ID** (Mandanten-ID), **Anwendungs-ID** und **Kennwort**.

1. Wählen Sie im Azure-Portal **Abonnements**, und kopieren Sie dann die ID für das Abonnement, in dem Sie den Dienstprinzipal erstellt haben.

    ![Abonnement-ID – Portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

1. Erstellen eines Dienstprinzipals Legen Sie einen geeigneten Bereich und den Rollentyp `reader` fest.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Weitere Informationen finden Sie unter [Erstellen eines Azure-Dienstprinzipals mit Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Der Befehl gibt ein Resultset wie das folgende zurück. Kopieren Sie die drei Eigenschaftswerte: **appID**, **password** und **tenant**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Rufen Sie eine Liste Ihrer Abonnements ab.

    ```azurecli
    az account list --output table
    ```

    Kopieren Sie die entsprechende Abonnement-ID.

    ![Abonnement-ID – CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Hinzufügen des Dienstprinzipals zur Rolle des Betrachters

Nachdem Sie jetzt über einen Dienstprinzipal verfügen, fügen Sie ihn der Rolle *Betrachter* in der Azure Data Explorer-Datenbank hinzu. Sie können diese Aufgabe unter **Berechtigungen** im Azure-Portal oder unter **Abfrage** mit einem Verwaltungsbefehl ausführen.

#### <a name="azure-portal---permissions"></a>Azure-Portal – Berechtigungen

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster.

1. Wählen Sie im Abschnitt **Übersicht** die Datenbank mit den StormEvents-Beispieldaten aus.

    ![Datenbank auswählen](media/data-explorer-configure-data-source/select-database.png)

1. Wählen Sie **Berechtigungen** und dann **Hinzufügen** aus.

    ![Datenbankberechtigungen](media/data-explorer-configure-data-source/database-permissions.png)

1. Wählen Sie unter **Datenbankberechtigungen hinzufügen** die Rolle **Betrachter** und dann **Prinzipale auswählen** aus.

    ![Hinzufügen von Datenbankberechtigungen](media/data-explorer-configure-data-source/add-permission.png)

1. Suchen Sie den zuvor erstellten Dienstprinzipal. Wählen Sie den Prinzipal und dann **Auswählen** aus.

    ![Verwalten von Berechtigungen im Azure-Portal](media/data-explorer-configure-data-source/new-principals.png)

1. Wählen Sie **Speichern** aus.

    ![Verwalten von Berechtigungen im Azure-Portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Verwaltungsbefehl – Abfrage

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster, und wählen Sie **Abfrage** aus.

    ![Abfrage](media/data-explorer-configure-data-source/query.png)

1. Führen Sie den folgenden Befehl im Abfragefenster aus. Verwenden Sie die Anwendungs-ID und die Mandanten-ID aus dem Azure-Portal oder der Befehlszeilenschnittstelle.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Der Befehl gibt ein Resultset wie das folgende zurück. In diesem Beispiel ist die erste Zeile für einen vorhandenen Benutzer in der Datenbank und die zweite Zeile für den gerade hinzugefügten Dienstprinzipal.

    ![Resultset](media/data-explorer-configure-data-source/result-set.png)
