---
title: Herstellen einer Verbindung mit Azure Data Explorer von Azure Databricks
description: In diesem Thema erfahren Sie, wie Sie Azure Databricks verwenden, um von Azure Data Explorer aus auf Daten zuzugreifen.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/21/2020
ms.openlocfilehash: b019f0f8cfefab2ed111b023993c929bd5f19e5f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342517"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks"></a>Herstellen einer Verbindung mit Azure Data Explorer von Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) ist eine Apache Spark-basierte Analyseplattform, die für die Microsoft Azure Platform optimiert ist. In diesem Artikel erfahren Sie, wie Sie Azure Databricks verwenden, um von Azure Data Explorer aus auf Daten zuzugreifen. Es gibt mehrere Möglichkeiten der Authentifizierung mit Azure Data Explorer, z.B. per Geräteanmeldung und per Azure AD-App (Azure Active Directory).
 
## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md)
- [Erstellen eines Azure Databricks-Arbeitsbereichs](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Wählen Sie unter **Azure Databricks-Dienst** in der Dropdownliste **Tarif** die Option **Premium** . Wenn Sie diese Option wählen, können Sie mit Azure Databricks-Geheimnissen Ihre Anmeldeinformationen speichern und dann in Notebooks und Aufträgen darauf verweisen.

- [Erstellen Sie einen Cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) mit den Standardeinstellungen in Azure Databricks.

 ## <a name="install-the-kusto-spark-connector-on-your-azure-databricks-cluster"></a>Installieren des Kusto Spark-Connectors im Azure Databricks-Cluster

So installieren Sie den [spark-kusto-connector](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector) in Ihrem Azure Databricks-Cluster

1. Wechseln Sie zu Ihrem Azure Databricks-Arbeitsbereich, und [erstellen Sie eine Bibliothek](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
1. Suchen Sie in Maven Central nach dem Paket *spark-kusto-connector* , installieren Sie die neueste Version, und fügen Sie sie an den Cluster an. 

## <a name="connect-to-azure-data-explorer-by-using-a-device-authentication"></a>Herstellen einer Verbindung mit Azure Data Explorer per Geräteauthentifizierung

[Beispielcode:](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py)

## <a name="connect-to-azure-data-explorer-by-using-an-azure-ad-app"></a>Herstellen einer Verbindung mit Azure Data Explorer mithilfe einer Azure AD-App

1. Erstellen Sie eine Azure AD-App mit [Bereitstellung einer Azure AD-Anwendung](./provision-azure-ad-app.md).
1. Gewähren Sie in Ihrer Azure Data Explorer-Datenbank wie folgt Zugriff auf Ihre Azure AD-App:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```

    | Parameter | BESCHREIBUNG |
    | - | - |
    | `DB Name` | Ihr Datenbankname |
    | `AAD App ID` | Ihre Azure AD-App-ID |
    | `AAD Tenant ID` | Ihre Azure AD-Mandanten-ID |

### <a name="find-your-azure-ad-tenant-id"></a>Ermitteln Ihrer Azure AD-Mandanten-ID

Für die Authentifizierung von Anwendungen verwendet der Azure Data Explorer Ihre Azure AD-Mandanten-ID. Verwenden Sie die folgende URL, um nach Ihrer Mandanten-ID zu suchen. Ersetzen Sie *YourDomain* durch Ihre Domäne.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Wenn Ihre Domäne beispielsweise *contoso.com* ist, lautet die URL: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Wählen Sie diese URL aus, um die Ergebnisse anzuzeigen. Die erste Zeile lautet wie folgt: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Ihre Mandanten-ID ist `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key-optional"></a>Speichern und Sichern Ihrer Azure AD-App-ID und des Schlüssels (optional)  

Speichern und sichern Sie Ihre Azure AD-App-ID und Ihren Schlüssel wie folgt mit Azure Databricks-[Geheimnissen](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets):

1. [Richten Sie die CLI ein](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installieren Sie die CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Richten Sie die Authentifizierung ein.](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)
1. Konfigurieren Sie die [Geheimnisse](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) mit den folgenden Beispielbefehlen:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="sample-code"></a>Beispielcode

1. [Beispielcode:](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py) 
1. Aktualisieren Sie die Platzhalterwerte mit Ihren Werten für Clustername, Datenbankname, Azure AD-Mandanten-ID, Azure AD-App-ID und Azure AD-App-Schlüssel. Wenn Sie Ihre Anmeldeinformationen im Geheimnisspeicher von Databricks speichern, passen Sie den Code entsprechend an, um die Werte aus dbutils abzurufen.