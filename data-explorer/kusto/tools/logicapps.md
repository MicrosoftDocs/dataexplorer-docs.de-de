---
title: Microsoft Logic App und Kusto - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Microsoft Logic App und Kusto in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f7d719ece5df6eb3f6d4060a2fb07e7092902601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523816"
---
# <a name="microsoft-logic-app-and-kusto"></a>Microsoft Logic App und Kusto

Mit dem Azure Kusto Logic App-Connector können Benutzer Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe mithilfe von [Microsoft Logic App](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)ausführen.

Die Logic App- und Flow-Connectors basieren auf demselben Connector, daher gelten für beide, wie auf der [Seite Flow-Dokumentation](flow.md)erwähnt, dieselben [Einschränkungen,](flow.md#limitations) [Aktionen,](flow.md#azure-kusto-flow-actions) [Authentifizierungs-](flow.md#authentication) und [Verwendungsbeispiele.](flow.md#usage-examples)


## <a name="how-to-create-a-logic-app-with-azure-kusto"></a>Erstellen einer Logik-App mit Azure Kusto

Öffnen Sie das Azure-Portal, und klicken Sie auf Erstellen einer neuen Logik-App-Ressource.
Fügen Sie den gewünschten Namen, das Abonnement, die Resoure-Gruppe und den Speicherort hinzu, und klicken Sie auf Erstellen.

![Erstellen einer Logik-App](./Images/KustoTools-LogicApp/logicapp-createlogicapp.png "logicapp-createlogicapp")

Nachdem die Logik-App erstellt wurde, klicken Sie auf die Schaltfläche Bearbeiten

![Bearbeiten des Logik-App-Designers](./Images/KustoTools-LogicApp/logicapp-editdesigner.png "logicapp-editdesigner")

Erstellen einer leeren Logik-App

![Leere Vorlage für Logik-App](./Images/KustoTools-LogicApp/logicapp-blanktemplate.png "logicapp-blanktemplate")

Wiederholungsaktion hinzufügen und dann "Azure Kusto" auswählen

![Logik-App Kusto Flow-Anschluss](./Images/KustoTools-LogicApp/logicapp-kustoconnector.png "logicapp-kustoconnector")