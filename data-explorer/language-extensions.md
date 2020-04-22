---
title: Verwalten von Spracherweiterungen in Ihrem Azure Data Explorer-Cluster
description: Verwenden Sie die Spracherweiterung, um andere Sprachen in Ihre Azure Data Explorer-KQL-Abfragen zu integrieren.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 52855651daccfe3c9baf5bb059530bcf7bfb0f19
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81494546"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Verwalten von Spracherweiterungen in Ihrem Azure Data Explorer-Cluster (Vorschauversion)

Mit dem Spracherweiterungsfeature können Sie mithilfe von Spracherweiterungs-Plug-Ins andere Sprachen in Ihre Azure Data Explorer-KQL-Abfragen integrieren. Wenn Sie eine benutzerdefinierte Funktion mit einem entsprechenden Skript ausführen, ruft das Skript Tabellendaten als Eingabe ab und soll damit eine tabellarische Ausgabe erzeugen. Die Runtime des Plug-Ins wird in einer [Sandbox](kusto/concepts/sandboxes.md) gehostet, einer isolierten und sicherem Umgebung, die auf den Knoten des Clusters ausgeführt wird. In diesem Artikel verwalten Sie das Plug-In für Spracherweiterungen in Ihrem Azure Data Explorer-Cluster im Azure-Portal.

> [!NOTE]
> Derzeit werden Azure Data Explorer-Spracherweiterungen für Python und R unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Aktivieren von Spracherweiterungen auf Ihrem Cluster

> [!WARNING]
> Überprüfen Sie vor dem Aktivieren einer Spracherweiterung die [Einschränkungen](#limitations).

Führen Sie die folgenden Schritte aus, um Spracherweiterungen für Ihren Cluster zu aktivieren:

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster. 
1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus. 
1. Wählen Sie im Bereich **Konfigurationen** die Option **Ein** aus, um eine Spracherweiterung zu aktivieren.
1. Wählen Sie **Speichern** aus.
 
    ![Spracherweiterung aktiviert](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Der Aktivierungsvorgang für eine Spracherweiterung kann einige Minuten dauern. Während dieser Zeit wird der Cluster angehalten.
 
## <a name="run-language-extension-integrated-queries"></a>Ausführen von integrierten Abfragen der Spracherweiterung

* Erfahren Sie, wie Sie [in Python integrierte KQL-Abfragen ausführen](kusto/query/pythonplugin.md).
* Erfahren Sie, wie Sie [in R integrierte KQL-Abfragen ausführen](kusto/query/rplugin.md). 

## <a name="disable-language-extensions-on-your-cluster"></a>Deaktivieren von Spracherweiterungen auf Ihrem Cluster

> [!NOTE]
> Das Deaktivieren von Spracherweiterungen kann einige Minuten dauern.

Führen Sie die folgenden Schritte aus, um Spracherweiterungen für Ihren Cluster zu deaktivieren:

1. Navigieren Sie im Azure-Portal zum Azure Data Explorer-Cluster. 
1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus. 
1. Wählen Sie im Bereich **Konfigurationen** die Option **Aus** aus, um eine Spracherweiterung zu deaktivieren.
1. Wählen Sie **Speichern** aus.

    ![Spracherweiterung deaktiviert](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Einschränkungen

* Das Spracherweiterungsfeature unterstützt keine [Datenträgerverschlüsselung](manage-cluster-security.md). 
* Die Runtime-Sandbox für Spracherweiterungen weist auch dann Speicherplatz zu, wenn keine Abfrage im Bereich der jeweiligen Sprache ausgeführt wird.
Ausführlichere Informationen finden Sie unter [Sandboxes](kusto/concepts/sandboxes.md).
