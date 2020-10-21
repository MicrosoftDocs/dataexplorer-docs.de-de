---
title: Verwenden von Logic Apps zum automatischen Ausführen von Kusto-Abfragen
description: Erfahren Sie, wie Sie mit Logic Apps die Kusto-Abfragen und-Befehle automatisch ausführen und planen.
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 4c918c43f748f97b3bb3f6d0342c660775c1e5c8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342653"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Microsoft Logic APP und Azure Daten-Explorer

Der Azure Kusto Logic App-Connector ermöglicht das automatische Ausführen von Kusto-Abfragen und-Befehlen im Rahmen einer geplanten oder ausgelösten Aufgabe mithilfe des [Microsoft Logic App](/azure/logic-apps/logic-apps-what-are-logic-apps) -Connector.

Logik-APP und Energie Automatisierung werden auf demselben Connector erstellt. Daher gelten die [Einschränkungen](../../flow.md#limitations), [Aktionen](../../flow.md#flow-actions), [Authentifizierungs](../../flow.md#authentication) -und [Verwendungs Beispiele](../../flow-usage.md) , die für die Energie Automatisierung gelten, auch für Logic apps, wie auf der [Dokumentationsseite zur Energie Automatisierung](../../flow.md)erwähnt.

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Erstellen einer Logik-App mit Azure Daten-Explorer

1. Öffnen Sie die [Microsoft Azure-Portal](https://ms.portal.azure.com/). 
1. Suchen Sie nach `logic app`, und wählen Sie diese Option aus.

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="Suchen nach &quot;Logik-app&quot; in der Azure-Portal, Azure Daten-Explorer" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. Wählen Sie **+ Hinzufügen** aus.

    ![Logik-app hinzufügen](./Images/logicapps/logicapp-add.png)

1. Geben Sie die erforderlichen Details zum Formular ein:
    * Abonnement
    * Ressourcengruppe
    * Name der Logik-App
    * Region oder Integrationsdienstumgebung
    * Standort
    * Protokollanalyse ein-oder ausschalten
1. Klicken Sie auf **Überprüfen + erstellen**.

    ![Erstellen einer Logik-App](./Images/logicapps/logicapp-create-new.png)

1. Wenn die Logik-App erstellt wurde, wählen Sie **Bearbeiten**aus.

    ![Logik-App-Designer bearbeiten](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. Erstellen Sie eine leere Logik-app.

    ![Leere Vorlage für Logik-App](./Images/logicapps/logicapp-blanktemplate.png "logicapp-blanktemplate")

1. Fügen Sie eine Wiederholungs Aktion hinzu, und wählen Sie **Azure Kusto**.

    ![Kusto-Flow-Connector für Logik-apps](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Konfigurieren einer Wiederholungs Aktion finden Sie auf der [Dokumentationsseite zur Energie Automatisierung](../../flow.md) .
* Werfen Sie einen Blick auf einige [Verwendungs Beispiele](../../flow-usage.md) für Ideen zum Konfigurieren ihrer Logik-App-Aktionen.
