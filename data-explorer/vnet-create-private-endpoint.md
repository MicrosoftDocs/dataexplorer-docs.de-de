---
title: Verwenden eines privaten Endpunkts in Ihrem Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk
description: Verwenden eines privaten Endpunkts in Ihrem Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk
author: orspod
ms.author: orspodek
ms.reviewer: elbirnbo
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: e980527d2342543777ae8186b9166615f9016c5e
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343401"
---
# <a name="create-a-private-endpoint-in-your-azure-data-explorer-cluster-in-your-virtual-network-preview"></a>Erstellen eines privaten Endpunkts in Ihrem Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk (Vorschau)

Verwenden Sie einen Private Link-Dienst mit einem privaten Endpunkt, um sicher auf Ihren Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk (VNET) zuzugreifen. 

Verwenden Sie zum Einrichten Ihres [Private Link-Diensts](/azure/private-link/private-link-service-overview) einen privaten Endpunkt mit einer IP-Adresse aus dem Adressraum Ihres virtuellen Azure-Netzwerks. Von dem [privaten Azure-Endpunkt](/azure/private-link/private-endpoint-overview) wird eine private IP-Adresse aus Ihrem VNET verwendet, um eine private und sichere Verbindung mit Azure Data Explorer herzustellen. Darüber hinaus muss die [DNS-Konfiguration](/azure/private-link/private-endpoint-dns) für Ihren Cluster geändert werden, um eine Verbindung unter Verwendung Ihres privaten Endpunkts herzustellen. Bei diesem Setup wird Netzwerkdatenverkehr zwischen einem Client in Ihrem privaten Netzwerk und dem Azure Data Explorer-Cluster über das VNET und über einen [Private Link](/azure/private-link/)-Dienst im Microsoft-Backbonenetzwerk geleitet und das öffentliche Internet umgangen. In diesem Artikel erfahren Sie, wie Sie einen privaten Endpunkt in Ihrem Cluster für die Abfrage (Engine) und die Erfassung (Datenverwaltung) erstellen und konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie einen [Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk](./vnet-create-cluster-portal.md).
* Deaktivieren von Netzwerkrichtlinien:
  * Deaktivieren Sie im virtuellen Netzwerk des Azure Data Explorer-Clusters die [Richtlinie für den Private Link-Dienst](/azure/private-link/disable-private-link-service-network-policy).
  * Deaktivieren Sie im virtuellen Netzwerk des privaten Endpunkts, das mit dem virtuellen Netzwerk des Azure Data Explorer-Clusters identisch sein kann, die [Richtlinie für private Endpunkte](/azure/private-link/disable-private-endpoint-network-policy).

## <a name="create-private-link-service"></a>Erstellen eines Private Link-Diensts

Um alle Dienste in Ihrem Cluster sicher miteinander zu verknüpfen, muss der [Private Link-Dienst](/azure/private-link/private-link-service-overview) zweimal erstellt werden: einmal für die Abfrage (Engine) und einmal für die Erfassung (Datenverwaltung).

1. Wählen Sie in der linken oberen Ecke des Portals die Schaltfläche **+ Ressource erstellen** aus.
1. Suchen Sie nach *Private Link-Dienst* .
1. Wählen Sie unter **Private Link-Dienst** die Option **Erstellen** aus.

    :::image type="content" source="media/vnet-create-private-endpoint/create-service.gif" alt-text="GIF mit den ersten drei Schritten der Erstellung eines Private Link-Diensts im Azure Data Explorer-Portal":::

1. Füllen Sie im Bereich **Create private link service** (Private Link-Dienst erstellen) die folgenden Felder aus:

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-basics.png" alt-text="GIF mit den ersten drei Schritten der Erstellung eines Private Link-Diensts im Azure Data Explorer-Portal":::

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Ihren VNET-Cluster enthält.|
    | Resource group | Ihre Ressourcengruppe | Wählen Sie die Ressourcengruppe aus, die Ihren VNET-Cluster enthält. |
    | Name | AzureDataExplorerPLS | Verwenden Sie einen aussagekräftigen Namen für Ihren Private Link-Dienst in der Ressourcengruppe. |
    | Region | Gleicher Wert wie für das virtuellen Netzwerk | Wählen Sie die Region aus, die der Region Ihres virtuellen Netzwerks entspricht. |

1. Füllen Sie im Bereich **Ausgangseinstellungen** die folgenden Felder aus:

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-outbound.png" alt-text="GIF mit den ersten drei Schritten der Erstellung eines Private Link-Diensts im Azure Data Explorer-Portal":::

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Load Balancer | Der Lastenausgleich für Ihre Engine oder *Datenverwaltung* | Wählen Sie den Lastenausgleich aus, der für Ihre Clusterengine erstellt wurde (Lastenausgleich, der auf die öffentliche IP-Adresse Ihrer Engine verweist).  Der Name des Lastenausgleichs für die Engine hat folgendes Format: kucompute-{Clustername}-elb <br> *Der Name des Lastenausgleichs für die Datenverwaltung hat folgendes Format: kudatamgmt-{Clustername}-elb*|
    | Front-End-IP-Adresse des Lastenausgleichs | Die öffentliche IP-Adresse Ihrer Engine oder Datenverwaltung. | Wählen Sie die öffentliche IP-Adresse des Lastenausgleichs aus. |
    | NAT-Quellsubnetz | Subnetz des Clusters | Ihr Subnetz, in dem der Cluster bereitgestellt wird.
    
1. Wählen Sie im Bereich **Zugriffssicherheit** die Benutzer aus, die Zugriff auf Ihren Private Link-Dienst anfordern können.
1. Wählen Sie **Überprüfen + erstellen** aus, um die Konfiguration Ihres Private Link-Diensts zu überprüfen. Wählen Sie **Erstellen** aus, um den Private Link-Dienst zu erstellen.
1. Öffnen Sie nach der Erstellung des Private Link-Diensts die Ressource, und speichern Sie den Private Link-Alias für den nächsten Schritt ( **Erstellen eines privaten Endpunkts** ). Beispielalias: *AzureDataExplorerPLS.111-222-333.westus.azure.privatelinkservice*

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

Um alle Dienste in Ihrem Cluster sicher miteinander zu verknüpfen, muss der [private Endpunkt](/azure/private-link/private-endpoint-overview) zweimal erstellt werden: einmal für die Abfrage (Engine) und einmal für die Erfassung (Datenverwaltung).

1. Wählen Sie in der linken oberen Ecke des Portals die Schaltfläche **+ Ressource erstellen** aus.
1. Suchen Sie nach *privater Endpunkt* .
1. Wählen Sie unter **Privater Endpunkt** die Option **Erstellen** aus.
1. Füllen Sie im Bereich **Privaten Endpunkt erstellen** die folgenden Felder aus:

    :::image type="content" source="media/vnet-create-private-endpoint/step-one-basics.png" alt-text="GIF mit den ersten drei Schritten der Erstellung eines Private Link-Diensts im Azure Data Explorer-Portal":::

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren privaten Endpunkt verwenden möchten.|
    | Resource group | Ihre Ressourcengruppe | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. |
    | Name | AzureDataExplorerPE | Verwenden Sie einen aussagekräftigen Namen für Ihr virtuelles Netzwerk in der Ressourcengruppe.
    | Region | *USA, Westen* | Wählen Sie die Region aus, die Ihre Anforderungen am besten erfüllt.
    
1. Füllen Sie im Bereich **Ressource** die folgenden Felder aus:

    :::image type="content" source="media/vnet-create-private-endpoint/step-two-resource.png" alt-text="GIF mit den ersten drei Schritten der Erstellung eines Private Link-Diensts im Azure Data Explorer-Portal":::

    **Einstellung** | **Wert**
    |---|---|
    | Verbindungsmethode | Der Alias Ihres Private Link-Diensts |
    | Ressourcen-ID oder Alias | Der Alias Ihres Private Link-Diensts (Engine). Beispielalias: *AzureDataExplorerPLS.111-222-333.westus.azure.privatelinkservice*  |
    
1. Wählen Sie **Überprüfen + erstellen** aus, um die Konfiguration Ihres privaten Endpunkts zu überprüfen, und wählen Sie anschließend **Erstellen** aus, um den Dienst für den privaten Endpunkt zu erstellen.
1. Führen Sie zum Erstellen des privaten Endpunkts für die Erfassung (Datenverwaltung) die gleichen Schritte aus, allerdings mit folgender Änderung:
    1. Wählen Sie im Bereich **Ressourcen** den Alias für den Private Link-Dienst für die Erfassung (Datenverwaltung) aus.

> [!NOTE]
> Mit dem Private Link-Dienst kann von mehreren privaten Endpunkten aus eine Verbindung hergestellt werden.

## <a name="approve-your-private-endpoint"></a>Genehmigen Ihres privaten Endpunkts

> [!NOTE]
> Wenn Sie beim Erstellen des Private Link-Diensts im Bereich **Zugriffssicherheit** die Option *Automatisch genehmigen* ausgewählt haben, ist dieser Schritt optional.

1. Wählen Sie in den Einstellungen Ihres Private Link-Diensts die Option **Verbindungen mit privatem Endpunkt** aus.
1. Wählen Sie in der Verbindungsliste Ihren privaten Endpunkt und anschließend **Genehmigen** aus.

:::image type="content" source="media/vnet-create-private-endpoint/private-link-approve.png" alt-text="GIF mit den ersten drei Schritten der Erstellung eines Private Link-Diensts im Azure Data Explorer-Portal"::: 

## <a name="set-dns-configuration"></a>Festlegen der DNS-Konfiguration

Wenn Sie einen Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk bereitstellen, wird der [DNS-Eintrag](/azure/private-link/private-endpoint-dns) aktualisiert, um auf den kanonischen Namen zu verweisen (mit *privatelink* zwischen dem Namen des Eintrags und dem Namen des Zonenhosts). Dieser Eintrag wird sowohl für die Engine als auch für die Erfassung (Datenverwaltung) aktualisiert. 

Wenn Ihr DNS-Name für die Engine also beispielsweise „myadx.westus.kusto.windows.net“ lautet, ergibt sich folgende Namensauflösung:

* **name** : myadx.westus.kusto.windows.net   <br> **Typ** : CNAME   <br> **value** : myadx.privatelink.westus.kusto.windows.net
* **name** : myadx.privatelink.westus.kusto.windows.net   <br> **Typ** : A   <br> **value:** 40.122.110.154
    > [!NOTE]
    > Dieser Wert ist die öffentliche IP-Adresse für die Abfrage (Engine), die Sie bei der Clustererstellung angegeben haben.

Richten Sie einen privaten DNS-Server oder eine Zone mit privatem Azure-DNS ein. Zu Testzwecken können Sie den Hosteintrag Ihres Testcomputers ändern.

Erstellen Sie die folgende DNS-Zone: **privatelink.region.kusto.windows.net** . DNS-Zone im Beispiel: *privatelink.westus.kusto.windows.net* . Registrieren Sie den Eintrag für Ihre Engine mit einem A-Eintrag und der IP-Adresse des privaten Endpunkts.

Der Name wird beispielsweise wie folgt aufgelöst:

* **name** : myadx.westus.kusto.windows.net   <br>**type** : CNAME   <br>**value** : myadx.privatelink.westus.kusto.windows.net
* **name** : myadx.privatelink.westus.kusto.windows.net   <br>**Typ** : A   <br>**value:** 10.3.0.9
    > [!NOTE]
    > Dieser Wert ist die IP-Adresse Ihres privaten Endpunkts. Sie haben die IP-Adresse bereits mit dem Private Link-Dienst für die Abfrage (Engine) verknüpft.

Nach dem Festlegen dieser DNS-Konfiguration ist die Abfrage (Engine) in Ihrem virtuellen Netzwerk privat über die folgende URL erreichbar: myadx.region.kusto.windows.net.

Registrieren Sie zum Einrichten der privaten Erreichbarkeit der Erfassung (Datenverwaltung) den Eintrag für Ihre Erfassung (Datenverwaltung) mit einem A-Eintrag und der IP-Adresse des privaten Endpunkts für die Erfassung.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Azure Data Explorer-Clustern in Ihrem virtuellen Netzwerk](vnet-deployment.md)
* [Behandlung von Problemen im Zusammenhang mit dem Zugriff, der Erfassung und dem Betrieb Ihres Azure Data Explorer-Clusters in Ihrem virtuellen Netzwerk](vnet-deploy-troubleshoot.md)