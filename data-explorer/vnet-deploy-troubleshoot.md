---
title: Behandlung von Problemen im Zusammenhang mit dem Zugriff, der Erfassung und dem Betrieb Ihres Azure Data Explorer-Clusters in Ihrem virtuellen Netzwerk
description: Behandlung von Problemen im Zusammenhang mit der Konnektivität, der Erfassung, dem Erstellen und dem Betrieb Ihres Azure Data Explorer-Clusters in Ihrem virtuellen Netzwerk
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494002"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Behandlung von Problemen im Zusammenhang mit dem Zugriff, der Erfassung und dem Betrieb Ihres Azure Data Explorer-Clusters in Ihrem virtuellen Netzwerk

In diesem Abschnitt erfahren Sie, wie Sie Konnektivitäts-, Betriebs- und Clustererstellungsprobleme bei einem Cluster beheben können, der in Ihrem [virtuellen Netzwerk](/azure/virtual-network/virtual-networks-overview) bereitgestellt wird.

## <a name="access-issues"></a>Zugriffsprobleme

Wenn beim Zugriff auf den Cluster über den öffentlichen („cluster.region.kusto.windows.net“) oder privaten („private-cluster.region.kusto.windows.net“) Endpunkt ein Problem auftritt und Sie vermuten, dass er im Zusammenhang mit dem Einrichten des virtuellen Netzwerks steht, führen Sie die folgenden Schritte zur Problembehandlung aus.

### <a name="check-tcp-connectivity"></a>Überprüfen der TCP-Konnektivität

Der erste Schritt ist die Überprüfung der TCP-Konnektivität mithilfe von Windows oder Linux OS.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Laden Sie [TCping](https://www.elifulkerson.com/projects/tcping.php) auf den Computer herunter, der eine Verbindung mit dem Cluster herstellt.
   2. Pingen Sie das Ziel vom Quellcomputer aus mit folgendem Befehl:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Installieren Sie *netcat* auf dem Computer, der eine Verbindung mit dem Cluster herstellt.

    ```bash
    $ apt-get install netcat
     ```

   2. Pingen Sie das Ziel vom Quellcomputer aus mit folgendem Befehl:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Wenn der Test nicht erfolgreich war, setzen Sie den Vorgang mit den folgenden Schritten fort. Ist der Test aber erfolgreich, wurde das Problem nicht durch ein TCP-Konnektivitätsproblem verursacht. Wechseln Sie zu [Betriebsprobleme](#cluster-creation-and-operations-issues), um mehr zur Problembehandlung zu erfahren.

### <a name="check-the-network-security-group-nsg"></a>Überprüfen der Netzwerksicherheitsgruppe (NSG)

   Überprüfen Sie, ob es bei der [Netzwerksicherheitsgruppe](/azure/virtual-network/security-overview) (NSG), die an das Subnetz des Clusters angefügt ist, eine eingehende Regel gibt, die den Zugriff auf die IP-Adresse des Clientcomputers für Port 443 zulässt.

### <a name="check-route-table"></a>Überprüfen der Routingtabelle

   Wenn beim Subnetz des Clusters eine Tunnelerzwingung zur Firewall eingerichtet wurde (Subnetz mit einer [Routentabelle](/azure/virtual-network/virtual-networks-udr-overview), in der die Standardroute '0.0.0.0/0' enthalten ist), vergewissern Sie sich, dass es in der IP-Adresse des Computers eine Route mit [Typ des nächsten Hops](/azure/virtual-network/virtual-networks-udr-overview) zum virtuellen Netzwerk/Internet gibt. Diese Route ist erforderlich, um Probleme mit asymmetrischen Routen zu vermeiden.

## <a name="ingestion-issues"></a>Erfassungsprobleme

Wenn bei der Erfassung Probleme auftreten und Sie vermuten, dass sie im Zusammenhang mit der Einrichtung des virtuellen Netzwerks stehen, führen Sie die folgenden Schritte aus.

### <a name="check-ingestion-health"></a>Überprüfen der Erfassungsintegrität

Überprüfen Sie, ob die [Clustererfassungsmetriken](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) einen fehlerfreien Status aufweisen.

### <a name="check-security-rules-on-data-source-resources"></a>Überprüfen von Sicherheitsregeln für Datenquellenressourcen

Wenn die Metriken angeben, dass keine Ereignisse aus der Datenquelle verarbeitet wurden (Metrik *Verarbeitete Ereignisse* für Event Hubs/IoT Hubs), vergewissern Sie sich, dass die Datenquellenressourcen (Event Hub oder Speicher) den Zugriff aus dem Subnetz des Clusters in den Firewallregeln oder Dienstendpunkten zulassen.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Überprüfen von Sicherheitsregeln, die im Subnetz des Clusters konfiguriert wurden

Vergewissern Sie sich, dass die NSG-, UDR- und Firewallregeln für das Subnetz des Clusters ordnungsgemäß konfiguriert wurden. Testen Sie außerdem die Netzwerkkonnektivität bei allen abhängigen Endpunkten. 

## <a name="cluster-creation-and-operations-issues"></a>Probleme bei der Clustererstellung und dem Betrieb

Wenn bei der Clustererstellung oder dem Betrieb Probleme auftreten und Sie vermuten, dass sie im Zusammenhang mit der Einrichtung des virtuellen Netzwerks stehen, führen Sie die folgenden Schritte zur Problembehandlung aus.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnostizieren des virtuellen Netzwerks mit der REST-API

Der [ARMClient](https://chocolatey.org/packages/ARMClient) dient zum Aufrufen der REST-API mithilfe von PowerShell. 

1. Anmelden mit ARMClient

   ```powerShell
   armclient login
   ```

1. Diagnosevorgang aufrufen

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Die Antwort überprüfen

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Auf Abschluss des Vorgangs warten

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Warten Sie, bis die Eigenschaft *status* die Meldung *Completed* (Abgeschlossen) anzeigt. Dann sollte im Feld *properties* (Eigenschaften) Folgendes angezeigt werden:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Wenn die Eigenschaft *Findings* (Ergebnisse) ein leeres Ergebnis anzeigt, bedeutet dies, das alle Netzwerktests erfolgreich waren und keine Verbindungen unterbrochen sind. Wenn der Fehler *Ausgehende Abhängigkeit {dependencyName}:{port} wurde möglicherweise nicht berücksichtigt (ausgehend)* angezeigt wird, kann der Cluster die abhängigen Dienstendpunkte nicht erreichen. Fahren Sie mit den folgenden Schritten fort.

### <a name="check-network-security-group-nsg"></a>Überprüfen der Netzwerksicherheitsgruppe (NSG)

Vergewissern Sie sich, dass die [Netzwerksicherheitsgruppe](/azure/virtual-network/security-overview) entsprechend den Anleitungen in [Abhängigkeiten für die VNET-Bereitstellung](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment) ordnungsgemäß konfiguriert wurde.

### <a name="check-route-table"></a>Überprüfen der Routingtabelle

Wenn beim Subnetz des Clusters eine Tunnelerzwingung zur Firewall eingerichtet wurde (Subnetz mit einer [Routentabelle](/azure/virtual-network/virtual-networks-udr-overview), in der die Standardroute '0.0.0.0/0' enthalten ist), vergewissern Sie sich, dass es in den [Verwaltungs-IP-Adressen](vnet-deployment.md#azure-data-explorer-management-ip-addresses) und [IP-Adressen zur Systemüberwachung](vnet-deployment.md#health-monitoring-addresses) eine Route mit [Typ des nächsten Hops](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools), *Internet* und [Quelladresspräfix](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) zu *'management-ip/32'* und *'health-monitoring-ip/32'* gibt. Diese Route ist erforderlich, um Probleme mit asymmetrischen Routen zu vermeiden.

### <a name="check-firewall-rules"></a>Überprüfen von Firewallregeln

Wenn Sie den ausgehenden Datenverkehr des Tunnelsubnetzes an eine Firewall erzwingen, vergewissern Sie sich, dass alle Abhängigkeiten mit FQDN (z. B. *.blob.core.windows.net*) in der Firewallkonfiguration zulässig sind, wie in [Sichern von ausgehendem Datenverkehr mit Firewall](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall) beschrieben wird.
