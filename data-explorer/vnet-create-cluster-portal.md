---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank in Ihrem virtuellen Netzwerk
description: In diesem Artikel erfahren Sie, wie Sie einen Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/24/2020
ms.openlocfilehash: cb31dc576e7bcaa30a37b04dcd4ff7ad3b5fae9c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874034"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Erstellen eines Azure Data Explorer-Clusters in Ihrem virtuellen Netzwerk

Azure Data Explorer unterstützt die Bereitstellung eines Clusters in einem Subnetz in Ihrem virtuellen Netzwerk (VNET). Dies ermöglicht den privaten Zugriff auf den Cluster über Ihr virtuelles Azure-Netzwerk oder lokal. Außerdem können Sie auf Ressourcen wie Event Hub und Storage in Ihrem virtuellen Netzwerk zugreifen sowie ein- und ausgehenden Datenverkehr einschränken.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-network-security-group-nsg"></a>Erstellen einer Netzwerksicherheitsgruppe (NSG)

[Netzwerksicherheitsgruppen (NSG)](/azure/virtual-network/security-overview) bieten die Möglichkeit, den Netzwerkzugriff innerhalb eines VNET zu steuern. Auf Azure Data Explorer kann über zwei Endpunkte zugegriffen werden: HTTPS (443) und TDS (1433). Die folgenden NSG-Regeln müssen konfiguriert werden, um den Zugriff auf diese Endpunkte für die Verwaltung, Überwachung und den ordnungsgemäßen Betrieb Ihres Clusters zuzulassen.

So erstellen Sie die Netzwerksicherheitsgruppe:

1. Wählen Sie in der linken oberen Ecke des Portals die Schaltfläche **+ Ressource erstellen** aus.
1. Suchen Sie nach *Netzwerksicherheitsgruppe*.
1. Wählen Sie unter **Netzwerksicherheitsgruppe** am unteren Bildschirmrand die Option **Erstellen** aus.
1. Geben Sie im Fenster **Netzwerksicherheitsgruppe erstellen** die folgenden Informationen an:

   ![Formular „Netzwerksicherheitsgruppe erstellen“](media/vnet-create-cluster-portal/network-security-group.png)

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Cluster verwenden möchten.|
    | Resource group | Ihre Ressourcengruppe | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. |
    | Name | AzureDataExplorerNsg | Verwenden Sie einen aussagekräftigen Namen für Ihre Netzwerksicherheitsgruppe (NSG) in der Ressourcengruppe.  |
    | Region | *USA, Westen* | Wählen Sie die Region aus, die Ihre Anforderungen am besten erfüllt.
    | | | |

1. Wählen Sie **Bewerten + erstellen**, um Ihre Clusterdetails zu überprüfen, und dann **Erstellen**, um den Cluster bereitzustellen.

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

    ![Zu Ressource wechseln](media/vnet-create-cluster-portal/notification-nsg.png)

1. Wählen Sie auf der Registerkarte **Eingangssicherheitsregeln** die Option **+Hinzufügen** aus.
1. Geben Sie im Fenster **Eingangssicherheitsregel hinzufügen** die folgenden Informationen an:

    ![Erstellen der Netzwerksicherheitsgruppe: Formular für Eingangsregel](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Einstellung** | **Empfohlener Wert** 
    |---|---|
    | `Source` | ServiceTag
    | Quelldiensttag | AzureDataExplorerManagement
    | Source port ranges | *
    | Destination | VirtualNetwork
    | Zielportbereiche | *
    | Protocol | TCP
    | Aktion | Allow
    | Priority | 100
    | Name | AllowAzureDataExplorerManagement
    | | |
    
1. Wiederholen Sie die beiden vorherigen Schritte für alle ein- und ausgehenden Abhängigkeiten gemäß [Abhängigkeiten für die VNET-Bereitstellung](vnet-deployment.md#dependencies-for-vnet-deployment). Alternativ können die Ausgangsregeln durch eine einzelne Regel ersetzt werden, um *Internet* für die Ports 443 und 80 zuzulassen.
    
    Die NSG-Regeln für ein- und ausgehende Abhängigkeiten sollten wie folgt aussehen:

    ![NSG-Regeln](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Erstellen öffentlicher IP-Adressen

So erstellen Sie die öffentlichen IP-Adressen für Abfragen (Engine):

1. Wählen Sie in der linken oberen Ecke des Portals die Schaltfläche **+ Ressource erstellen** aus.
1. Suchen Sie nach *Öffentliche IP-Adresse*.
1. Klicken Sie unter **Öffentliche IP-Adresse** auf **Erstellen**.
1. Geben Sie im Bereich **Öffentliche IP-Adresse erstellen** die folgenden Informationen an:
   
   ![Formular „Öffentliche IP-Adresse erstellen“](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | IP-Version | IPv4 | Wählen Sie die IP-Version aus. Es wird nur IPv4 unterstützt.|
    | Sku | Standard | Für den URI-Endpunkt für Abfragen (Engine) muss **Standard** angegeben werden. |
    | Name | engine-pip | Verwenden Sie einen aussagekräftigen Namen für Ihre öffentliche IP-Adresse in der Ressourcengruppe.
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihre öffentliche IP-Adresse verwenden möchten.|
    | Resource group | Ihre Ressourcengruppe | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. |
    | Standort | *USA, Westen* | Wählen Sie die Region aus, die Ihre Anforderungen am besten erfüllt.
    | | | |

1. Wählen Sie **Erstellen** aus, um die öffentliche IP-Adresse zu erstellen.

1. Befolgen Sie zum Erstellen der öffentlichen IP-Adresse für die Erfassung (Datenverwaltung) die gleichen Anweisungen.

## <a name="create-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und eines Subnetzes

So erstellen Sie das virtuelle Netzwerk und das Subnetz:

1. Wählen Sie in der linken oberen Ecke des Portals die Schaltfläche **+ Ressource erstellen** aus.
1. Suchen Sie nach *Virtuelles Netzwerk*.
1. Wählen Sie unter **Virtuelles Netzwerk** am unteren Bildschirmrand die Option **Erstellen** aus.
1. Geben Sie im Fenster **Virtuelles Netzwerk erstellen** die folgenden Informationen an:

   ![Formular „Virtuelles Netzwerk erstellen“](media/vnet-create-cluster-portal/vnet-blade.png)

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Cluster verwenden möchten.|
    | Resource group | Ihre Ressourcengruppe | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. |
    | Name | AzureDataExplorerVnet | Verwenden Sie einen aussagekräftigen Namen für Ihr virtuelles Netzwerk in der Ressourcengruppe.
    | Region | *USA, Westen* | Wählen Sie die Region aus, die Ihre Anforderungen am besten erfüllt.
    | | | |

    > [!NOTE]
    > Orientieren Sie sich bei der Planung der Subnetzgröße für Produktionsworkloads an [Planen der Subnetzgröße in Ihrem VNET](vnet-deployment.md#plan-subnet-size-in-your-vnet).

1. Wählen Sie **Bewerten + erstellen**, um Ihre Clusterdetails zu überprüfen, und dann **Erstellen**, um den Cluster bereitzustellen.

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.
1. Navigieren Sie zum Blatt **Subnetze**, und wählen Sie das Subnetz **default** aus.
    
    ![Blatt „Subnetze“](media/vnet-create-cluster-portal/subnets.png)

1. Gehen Sie im Fenster des Subnetzes **default** wie folgt vor:
    1. Wählen Sie im Dropdownmenü Ihre **Netzwerksicherheitsgruppe** aus. 
    1. Wählen Sie den Namen Ihrer Netzwerksicherheitsgruppe aus (in diesem Fall: **AzureDataExplorerNsg**). 
    1. **Speichern**

    ![Konfigurieren des Subnetzes](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Erstellen eines Clusters

Erstellen Sie einen Azure Data Explorer-Cluster mit einem definierten Satz von Compute- und Speicherressourcen in einer Azure-Ressourcengruppe, wie unter [Erstellen eines Clusters](create-cluster-database-portal.md#create-a-cluster) beschrieben.

1. Wählen Sie vor Abschluss der Clustererstellung im Fenster **Create an Azure Data Explorer cluster** (Azure Data Explorer-Cluster erstellen) die Registerkarte **Netzwerk** aus, um Details zum virtuellen Netzwerk anzugeben, und verwenden Sie dabei die auf den vorherigen Registerkarten erstellten Ressourcen:

   ![VNET-Formular für die Clustererstellung](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihre Netzwerkressourcen verwenden möchten.|
    | Virtual Network | AzureDataExplorerVnet | Wählen Sie das in den vorherigen Schritten erstellte virtuelle Netzwerk aus.
    | Subnet | default | Wählen Sie das in den vorherigen Schritten erstellte Subnetz aus.
    | Öffentliche IP-Adresse für Abfragen | engine-pip | Wählen Sie die öffentliche IP-Adresse für Abfragen aus, die in den vorherigen Schritten erstellt wurde.
    | Öffentliche IP-Adresse zur Datenerfassung | dm-pip | Wählen Sie die öffentliche IP-Adresse für die Erfassung aus, die in den vorherigen Schritten erstellt wurde.
    | | | |

1. Wählen Sie **Überprüfen + erstellen** aus, um Ihren Cluster zu erstellen.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

Verwenden Sie die [Azure Resource Manager-Vorlage zum Bereitstellen eines Azure Data Explorer-Clusters in Ihrem VNET](https://azure.microsoft.com/resources/templates/101-kusto-vnet/), um Ihren Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen von Azure Data Explorer in Ihrem virtuellen Netzwerk](vnet-deployment.md)
