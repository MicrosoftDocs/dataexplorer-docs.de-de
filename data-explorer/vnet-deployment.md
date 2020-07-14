---
title: Bereitstellen von Azure Data Explorer in Ihrem virtuellen Netzwerk
description: Erfahren Sie, wie Sie Azure Data Explorer in Ihrem virtuellen Netzwerk bereitstellen.
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: dcc196675b29ac1989fb0753e87ef5f1bf0477aa
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291575"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Bereitstellen von Azure Data Explorer-Clustern in Ihrem virtuellen Netzwerk

In diesem Artikel werden die Ressourcen erläutert, die beim Bereitstellen eines Azure Data Explorer-Clusters in einem benutzerdefinierten virtuellen Azure-Netzwerk vorhanden sind. Diese Informationen helfen Ihnen bei der Bereitstellung eines Clusters in einem Subnetz in Ihrem virtuellen Netzwerk (VNET). Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie unter [Was ist Azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![VNET-Diagramm](media/vnet-deployment/vnet-diagram.png)

Azure Data Explorer unterstützt die Bereitstellung eines Clusters in einem Subnetz in Ihrem virtuellen Netzwerk (VNET). Diese Funktion ermöglicht Ihnen Folgendes:

* Erzwingen von [NSG-Regeln (Network Security Group)](/azure/virtual-network/security-overview) für Ihren Azure Data Explorer-Clusterdatenverkehr
* Verbinden Ihres lokalen Netzwerks mit dem Subnetz des Azure Data Explorer-Clusters
* Sichern Ihrer Datenverbindungsquellen ([Event Hub](/azure/event-hubs/event-hubs-about) und [Event Grid](/azure/event-grid/overview)) mit [Dienstendpunkten](/azure/virtual-network/virtual-network-service-endpoints-overview)

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Zugreifen auf den Azure Data Explorer-Cluster in Ihrem VNET

Sie können auf Ihren Azure Data Explorer-Cluster mit den folgenden IP-Adressen für die einzelnen Dienste (Engine- und Datenverwaltungsdienste) zugreifen:

* **Private IP**: Wird für den Zugriff auf den Cluster innerhalb des VNET verwendet.
* **Öffentliche IP**: Wird für den Zugriff auf den Cluster von außerhalb des VNET zur Verwaltung und Überwachung sowie als Quelladresse für ausgehende Verbindungen, die aus dem Cluster gestartet werden, verwendet.

Die folgenden DNS-Einträge werden für den Zugriff auf den Dienst erstellt: 

* `[clustername].[geo-region].kusto.windows.net` (Engine) und `ingest-[clustername].[geo-region].kusto.windows.net` (Datenverwaltung) werden der öffentlichen IP-Adresse für jeden Dienst zugeordnet. 

* `private-[clustername].[geo-region].kusto.windows.net` (Engine) und `private-ingest-[clustername].[geo-region].kusto.windows.net` (Datenverwaltung) werden der privaten IP-Adresse für jeden Dienst zugeordnet.

## <a name="plan-subnet-size-in-your-vnet"></a>Planen der Subnetzgröße in Ihrem VNET

Die Größe des Subnetzes, das zum Hosten eines Azure Data Explorer-Clusters verwendet wird, kann nach der Bereitstellung des Subnetzes nicht geändert werden. In Ihrem VNET verwendet Azure Data Explorer eine private IP-Adresse für jede VM und zwei private IP-Adressen für die internen Lastenausgleiche (Engine und Datenverwaltung). Das Azure-Netzwerk verwendet außerdem fünf IP-Adressen für jedes Subnetz. Azure Data Explorer stellt zwei VMs für den Datenverwaltungsdienst bereit. Enginedienst-VMs werden nach Skalierungskapazität der Benutzerkonfiguration bereitgestellt.

Gesamtanzahl von IP-Adressen:

| Zweck | Anzahl der Adressen |
| --- | --- |
| Enginedienst | 1 pro Instanz |
| Datenverwaltungsdienst | 2 |
| Interne Lastenausgleiche | 2 |
| Für Azure reservierte Adressen | 5 |
| **Gesamt** | **Anzahl der Engine-Instanzen + 9** |

> [!IMPORTANT]
> Die Subnetzgröße muss im Voraus geplant werden, da sie nach der Bereitstellung von Azure Data Explorer nicht mehr geändert werden kann. Reservieren Sie deshalb die erforderliche Subnetzgröße entsprechend.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Dienstendpunkte zum Herstellen einer Verbindung mit Azure Data Explorer

Mit [Azure-Dienstendpunkten](/azure/virtual-network/virtual-network-service-endpoints-overview) können Sie Ihre mehrinstanzenfähigen Azure-Ressourcen im virtuellen Netzwerk sichern.
Wenn Sie Azure Data Explorer-Cluster in Ihrem Subnetz bereitstellen, können Sie Datenverbindungen mit [Event Hub](/azure/event-hubs/event-hubs-about) oder [Event Grid](/azure/event-grid/overview) einrichten, während Sie die zugrunde liegenden Ressourcen für das Azure Data Explorer-Subnetz einschränken.

> [!NOTE]
> Wenn Sie die EventGrid-Einrichtung mit [Storage](/azure/storage/common/storage-introduction) und [Event Hub] verwenden, kann das im Abonnement verwendete Speicherkonto mit Dienstendpunkten im Subnetz von Azure Data Explorer gesperrt werden, während vertrauenswürdige Azure-Plattformdienste in der [Firewallkonfiguration](/azure/storage/common/storage-network-security) zugelassen werden. Der Event Hub kann jedoch Dienstendpunkte nicht aktivieren, da er keine vertrauenswürdigen [Azure-Plattformdienste](/azure/event-hubs/event-hubs-service-endpoints) unterstützt.

## <a name="dependencies-for-vnet-deployment"></a>Abhängigkeiten für die VNET-Bereitstellung

### <a name="network-security-groups-configuration"></a>Konfiguration von Netzwerksicherheitsgruppen

[Netzwerksicherheitsgruppen (NSG)](/azure/virtual-network/security-overview) bieten die Möglichkeit, den Netzwerkzugriff innerhalb eines VNET zu steuern. Auf Azure Data Explorer kann über zwei Endpunkte zugegriffen werden: HTTPs (443) und TDS (1433). Die folgenden NSG-Regeln müssen konfiguriert werden, um den Zugriff auf diese Endpunkte für die Verwaltung, Überwachung und den ordnungsgemäßen Betrieb Ihres Clusters zuzulassen. Zusätzliche Regeln sind abhängig von Ihren Sicherheitsrichtlinien.

#### <a name="inbound-nsg-configuration"></a>Konfiguration eingehender Netzwerksicherheitsgruppen

| **Verwenden Sie**   | **From**   | **An**   | **Protokoll**   |
| --- | --- | --- | --- |
| Verwaltung  |[ADX-Verwaltungsadressen](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | ADX-Subnetz: 443  | TCP  |
| Systemüberwachung  | [ADX-Systemüberwachungsadressen](#health-monitoring-addresses)  | ADX-Subnetz: 443  | TCP  |
| Interne ADX-Kommunikation  | ADX-Subnetz: Alle Ports  | ADX-Subnetz: Alle Ports  | All  |
| Azure Load Balancer eingehend zulassen (Integritätstest)  | AzureLoadBalancer  | ADX-Subnetz: 80, 443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Konfiguration ausgehender Netzwerksicherheitsgruppen

| **Verwenden Sie**   | **From**   | **An**   | **Protokoll**   |
| --- | --- | --- | --- |
| Abhängigkeit von Azure Storage  | ADX-Subnetz  | Storage: 443  | TCP  |
| Abhängigkeit von Azure Data Lake  | ADX-Subnetz  | AzureDataLake: 443  | TCP  |
| EventHub-Erfassung und Dienstüberwachung  | ADX-Subnetz  | EventHub: 443, 5671  | TCP  |
| Metriken veröffentlichen  | ADX-Subnetz  | AzureMonitor: 443 | TCP  |
| Azure Monitor-Konfigurationsdownload  | ADX-Subnetz  | [Azure Monitor-Konfigurationsendpunktadressen](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (falls zutreffend) | ADX-Subnetz | AzureActiveDirectory: 443 | TCP |
| Zertifizierungsstelle | ADX-Subnetz | Internet: 80 | TCP |
| Interne Kommunikation  | ADX-Subnetz  | ADX-Subnetz: Alle Ports  | All  |
| Ports, die für die Plug-Ins `sql\_request` und `http\_request` verwendet werden  | ADX-Subnetz  | Internet: Benutzerdefiniert  | TCP  |

### <a name="relevant-ip-addresses"></a>Relevante IP-Adressen

#### <a name="azure-data-explorer-management-ip-addresses"></a>IP-Adressen der Azure Data Explorer-Verwaltung

> [!NOTE]
> Verwenden Sie für künftige Bereitstellungen das AzureDataExplorer-Diensttag.

| Region | Adressen |
| --- | --- |
| Australien, Mitte | 20.37.26.134 |
| Australien, Mitte 2 | 20.39.99.177 |
| Australien (Osten) | 40.82.217.84 |
| Australien, Südosten | 20.40.161.39 |
| Brasilien, Süden | 191.233.25.183 |
| Kanada, Mitte | 40.82.188.208 |
| Kanada, Osten | 40.80.255.12 |
| Indien, Mitte | 40.81.249.251, 104.211.98.159 |
| USA (Mitte) | 40.67.188.68 |
| USA, Mitte (EUAP) | 40.89.56.69 |
| China, Osten 2 | 139.217.184.92 |
| China, Norden 2 | 139.217.60.6 |
| Asien, Osten | 20.189.74.103 |
| East US | 52.224.146.56 |
| USA (Ost 2) | 52.232.230.201 |
| USA, Osten 2 (EUAP) | 52.253.226.110 |
| Frankreich, Mitte | 40.66.57.91 |
| Frankreich, Süden | 40.82.236.24 |
| Japan, Osten | 20.43.89.90 |
| Japan, Westen | 40.81.184.86 |
| Korea, Mitte | 40.82.156.149 |
| Korea, Süden | 40.80.234.9 |
| USA Nord Mitte | 40.81.45.254 |
| Nordeuropa | 52.142.91.221 |
| Südafrika, Norden | 102.133.129.138 |
| Südafrika, Westen | 102.133.0.97 |
| USA Süd Mitte | 20.45.3.60 |
| Asien, Südosten | 40.119.203.252 |
| Indien (Süden) | 40.81.72.110, 104.211.224.189 |
| UK, Süden | 40.81.154.254 |
| UK, Westen | 40.81.122.39 |
| USDoD, Mitte | 52.182.33.66 |
| USDoD, Osten | 52.181.33.69 |
| US Gov Arizona | 52.244.33.193 |
| US Gov Texas | 52.243.157.34 |
| US Government, Virginia | 52.227.228.88 |
| USA, Westen-Mitte | 52.159.55.120 |
| Europa, Westen | 51.145.176.215 |
| Indien, Westen | 40.81.88.112, 104.211.160.120 |
| USA (Westen) | 13.64.38.225 |
| USA, Westen 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Systemüberwachungsadressen

| Region | Adressen |
| --- | --- |
| Australien, Mitte | 191.239.64.128 |
| Australien, Mitte 2 | 191.239.64.128 |
| Australien (Osten) | 191.239.64.128 |
| Australien, Südosten | 191.239.160.47 |
| Brasilien Süd | 23.98.145.105 |
| Kanada, Mitte | 168.61.212.201 |
| Kanada, Osten | 168.61.212.201 |
| Indien, Mitte | 23.99.5.162 |
| USA (Mitte) | 168.61.212.201 |
| USA, Mitte (EUAP) | 168.61.212.201 |
| China, Osten 2 | 40.73.96.39 |
| China, Norden 2 | 40.73.33.105 |
| Asien, Osten | 168.63.212.33 |
| East US | 137.116.81.189 |
| USA (Ost) 2 | 137.116.81.189 |
| USA, Osten 2 (EUAP) | 137.116.81.189 |
| Frankreich, Mitte | 23.97.212.5 |
| Frankreich, Süden | 23.97.212.5 |
| Japan, Osten | 138.91.19.129 |
| Japan, Westen | 138.91.19.129 |
| Korea, Mitte | 138.91.19.129 |
| Korea, Süden | 138.91.19.129 |
| USA Nord Mitte | 23.96.212.108 |
| Nordeuropa | 191.235.212.69 
| Südafrika, Norden | 104.211.224.189 |
| Südafrika, Westen | 104.211.224.189 |
| USA Süd Mitte | 23.98.145.105 |
| Indien (Süden) | 23.99.5.162 |
| Asien, Südosten | 168.63.173.234 |
| UK, Süden | 23.97.212.5 |
| UK, Westen | 23.97.212.5 |
| USDoD, Mitte | 52.238.116.34 |
| USDoD, Osten | 52.238.116.34 |
| US Gov Arizona | 52.244.48.35 |
| US Gov Texas | 52.238.116.34 |
| US Government, Virginia | 23.97.0.26 |
| USA, Westen-Mitte | 168.61.212.201 |
| Europa, Westen | 23.97.212.5 |
| Indien, Westen | 23.99.5.162 |
| USA (Westen) | 23.99.5.162 |
| USA, Westen 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Monitor-Konfigurationsendpunktadressen

| Region | Adressen |
| --- | --- |
| Australien, Mitte | 52.148.86.165 |
| Australien, Mitte 2 | 52.148.86.165 |
| Australien, Osten | 52.148.86.165 |
| Australien, Südosten | 52.148.86.165 |
| Brasilien, Süden | 13.68.89.19 |
| Kanada, Mitte | 13.90.43.231 |
| Kanada, Osten | 13.90.43.231 |
| Indien, Mitte | 13.71.25.187 |
| USA, Mitte | 52.173.95.68 |
| USA, Mitte (EUAP) | 13.90.43.231 |
| Asien, Osten | 13.75.117.221 |
| USA, Osten | 13.90.43.231 |
| USA, Osten 2 | 13.68.89.19 |    
| USA, Osten 2 (EUAP) | 13.68.89.19 |
| Frankreich, Mitte | 52.174.4.112 |
| Frankreich, Süden | 52.174.4.112 |
| Japan, Osten | 13.75.117.221 |
| Japan, Westen | 13.75.117.221 |
| Korea, Mitte | 13.75.117.221 |
| Korea, Süden | 13.75.117.221 |
| USA, Norden-Mitte | 52.162.240.236 |
| Europa, Norden | 52.169.237.246 |
| Südafrika, Norden | 13.71.25.187 |
| Südafrika, Westen | 13.71.25.187 |
| USA, Süden-Mitte | 13.84.173.99 |
| Indien, Süden | 13.71.25.187 |
| Asien, Südosten | 52.148.86.165 |
| UK, Süden | 52.174.4.112 |
| UK, Westen | 52.169.237.246 |
| USA, Westen-Mitte | 52.161.31.69 |
| Europa, Westen | 52.174.4.112 |
| Indien, Westen | 13.71.25.187 |
| USA, Westen | 40.78.70.148 |
| USA, Westen 2 | 52.151.20.103 |

## <a name="disable-access-to-azure-data-explorer-from-the-public-ip"></a>Deaktivieren des Zugriffs auf Azure Data Explorer über die öffentliche IP-Adresse

Wenn Sie den Zugriff auf Azure Data Explorer über die öffentliche IP-Adresse vollständig deaktivieren möchten, erstellen Sie eine weitere Eingangsregel in der NSG. Diese Regel muss eine niedrigere [Priorität](/azure/virtual-network/security-overview#security-rules) haben (eine höhere Zahl). 

| **Verwenden Sie**   | **Quelle** | **Quelldiensttag** | **Quellportbereiche**  | **Ziel** | **Zielportbereiche** | **Protokoll ** | **Aktion** | **Priorität ** |
| ---   | --- | --- | ---  | --- | --- | --- | --- | --- |
| Deaktivieren des Zugriffs über das Internet | Diensttag | Internet | *  | VirtualNetwork | * | Any | Verweigern | höhere Zahl als die oben aufgeführten Regeln |

Diese Regel ermöglicht es Ihnen, nur über die folgenden DNS-Einträge (die der privaten IP-Adresse für jeden Dienst zugeordnet sind) eine Verbindung mit dem Azure Data Explorer-Cluster herzustellen:
* `private-[clustername].[geo-region].kusto.windows.net` (Engine)
* `private-ingest-[clustername].[geo-region].kusto.windows.net` (Datenverwaltung)

## <a name="expressroute-setup"></a>ExpressRoute-Setup

Mithilfe von ExpressRoute können Sie ein lokales Netzwerk mit dem virtuellen Azure-Netzwerk verbinden. Ein gängiges Setup ist das Ankündigen der Standardroute (0.0.0.0/0) über die BGP-Sitzung (Border Gateway Protocol). Dadurch wird der Datenverkehr aus dem virtuellen Netzwerk zwangsläufig an das lokale Netzwerk des Kunden weitergeleitet, das den Datenverkehr möglicherweise abbricht und so ausgehende Datenflüsse unterbrochen werden. Um dieses Standardverhalten außer Kraft zu setzen, kann eine [benutzerdefinierte Route (User Defined Route, UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) konfiguriert werden, und der nächste Hop ist dann *Internet*. Da die UDR Vorrang vor BGP hat, wird der Datenverkehr an das Internet geleitet.

## <a name="securing-outbound-traffic-with-firewall"></a>Sichern von ausgehendem Datenverkehr mit Firewall

Wenn Sie ausgehenden Datenverkehr mithilfe von [Azure Firewall](/azure/firewall/overview) oder einem virtuellen Gerät zum Einschränken von Domänennamen sichern möchten, müssen die folgenden vollqualifizierten Domänennamen (FQDN) in der Firewall zulässig sein.

```
prod.warmpath.msftcloudes.com:443
gcs.prod.monitoring.core.windows.net:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Sie müssen außerdem die [Routingtabelle](/azure/virtual-network/virtual-networks-udr-overview) für das Subnetz mit den [Verwaltungsadressen](#azure-data-explorer-management-ip-addresses) und [Systemüberwachungsadressen](#health-monitoring-addresses) mit *Internet* als nächstem Hop definieren, um Probleme mit asymmetrischen Routen zu vermeiden.

Beispielsweise müssen für die Region **USA, Westen** die folgenden UDRs definiert werden:

| Name | Adresspräfix | Nächster Hop |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Bereitstellen eines Azure Data Explorer-Clusters in Ihrem VNET mithilfe einer Azure Resource Manager-Vorlage

Verwenden Sie zum Bereitstellen eines Azure Data Explorer-Clusters in Ihrem virtuellen Netzwerk die [entsprechende Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/101-kusto-vnet/).

Mit dieser Vorlage werden der Cluster, das virtuelle Netzwerk, das Subnetz, die Netzwerksicherheitsgruppe und die öffentlichen IP-Adressen erstellt.
