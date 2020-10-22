---
title: Übersicht über die Azure Data Explorer-API – Azure Data Explorer
description: Dieser Artikel enthält eine Beschreibung der API in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2020
ms.openlocfilehash: b352ed44e7beb487588cf94f37d280eb6b51cdf9
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337464"
---
# <a name="azure-data-explorer-api-overview"></a>Übersicht über die Azure Data Explorer-API

Der Azure Data Explorer-Dienst unterstützt folgende Kommunikationsendpunkte:

1. Einen [REST-API](#rest-api)-Endpunkt zum Abfragen und Verwalten der Daten in Azure Data Explorer.
   Dieser Endpunkt unterstützt die [Kusto-Abfragesprache](../query/index.md) für Abfragen sowie [Steuerungsbefehle](../management/index.md).
1. Einen [MS-TDS](#ms-tds)-Endpunkt, der einen Teil des TDS-Protokolls (Tabular Data Stream) von Microsoft implementiert, das von den Microsoft SQL Server-Produkten verwendet wird.
   Dieser Endpunkt ist nützlich für Tools, die mit einem SQL Server-Endpunkt für Abfragen kommunizieren können.
1. Einen [Azure Resource Manager](/azure/role-based-access-control/resource-provider-operations#microsoftkusto)-Endpunkt (ARM), der von Azure-Diensten standardmäßig verwendet wird. Der Endpunkt wird zum Verwalten von Ressourcen wie Azure Data Explorer-Clustern verwendet.

## <a name="rest-api"></a>REST-API

Für die Kommunikation mit einem Azure Data Explorer-Dienst wird in erster Linie die REST-API des Diensts verwendet. Dieser vollständig dokumentierte Endpunkt bietet Aufrufern folgende Möglichkeiten:

* Daten abfragen
* Abfragen und Ändern von Metadaten
* Erfassen von Daten
* Abfragen des Integritätsstatus des Diensts
* Ressourcen verwalten

Die verschiedenen Azure Data Explorer-Dienste kommunizieren untereinander über die gleiche öffentlich verfügbare REST-API.

Darüber hinaus steht eine Reihe von [Clientbibliotheken](client-libraries.md) zur Verfügung, die die Verwendung des Diensts ermöglichen, ohne sich mit dem REST-API-Protokoll zu befassen.

## <a name="ms-tds"></a>MS-TDS

Azure Data Explorer unterstützt zudem das Microsoft SQL Server-Kommunikationsprotokoll (MS-TDS) und ermöglicht eingeschränkt die Ausführung von T-SQL-Abfragen. Mit diesem Protokoll können Benutzer Abfragen in Azure Data Explorer mit einer vertrauten Abfragesyntax (T-SQL) und vertrauten Datenbankclient-Tools wie LINQPad, sqlcmd, Tableau, Excel oder Power BI ausführen.

Weitere Informationen finden Sie unter [MS-TDS (T-SQL-Unterstützung)](tds/index.md).

## <a name="client-libraries"></a>Clientbibliotheken 

Azure Data Explorer bietet eine Reihe von Clientbibliotheken, die die oben genannten Endpunkte nutzen, um den programmgesteuerten Zugriff zu vereinfachen:

* .NET SDK
* Python SDK
* R
* Java-SDK
* Node SDK
* Go SDK
* PowerShell

### <a name="net-framework-libraries"></a>.NET Framework-Bibliotheken

Die .NET Framework-Bibliotheken sind die empfohlene Methode zum programmgesteuerten Aufrufen von Azure Data Explorer-Funktionen.
Es stehen verschiedene Bibliotheken zur Verfügung:

* [Kusto.Data (Kusto-Clientbibliothek)](./netfx/about-kusto-data.md): Kann zum Abfragen von Daten und Metadaten sowie zum Ändern dieser Daten verwendet werden. 
   Sie basiert auf der Kusto-REST-API und sendet HTTPS-Anforderungen an den Kusto-Zielcluster.
* [Kusto.Ingest (Kusto-Erfassungsbibliothek)](netfx/about-kusto-ingest.md): Verwendet `Kusto.Data` und erweitert diese Bibliothek, um die Datenerfassung zu vereinfachen.

Die oben genannten Bibliotheken verwenden Azure-APIs (beispielsweise die Azure Storage-API und die Azure Active Directory-API).

### <a name="python-libraries"></a>Python-Bibliotheken

Azure Data Explorer stellt eine Python-Clientbibliothek bereit, die Aufrufern das Senden von Datenabfragen und Steuerungsbefehlen ermöglicht.
Weitere Informationen finden Sie unter [Azure Data Explorer Python SDK](python/kusto-python-client-library.md).

### <a name="r-library"></a>R-Bibliothek

Azure Data Explorer stellt eine R-Clientbibliothek bereit, die Aufrufern das Senden von Datenabfragen und Steuerungsbefehlen ermöglicht.
Weitere Informationen finden Sie unter [Azure Data Explorer R SDK](r/kusto-r-client-library.md).

### <a name="java-sdk"></a>Java-SDK

Die Java-Clientbibliothek ermöglicht das Abfragen von Azure Data Explorer-Clustern mithilfe von Java. Weitere Informationen finden Sie unter [Azure Data Explorer Java SDK](java/kusto-java-client-library.md).

### <a name="node-sdk"></a>Node SDK

Das Azure Data Explorer Node SDK ist mit Node LTS (derzeit v6.14) kompatibel und wird mit ES6 erstellt.
Weitere Informationen finden Sie unter [Azure Data Explorer Node SDK](node/kusto-node-client-library.md).

### <a name="go-sdk"></a>Go SDK

Die Azure Data Explorer-Go-Clientbibliothek ermöglicht die Abfrage, Steuerung und Erfassung in Azure Data Explorer-Clustern mithilfe von Go. Weitere Informationen finden Sie unter [Azure Data Explorer Golang SDK](golang/kusto-golang-client-library.md).

### <a name="powershell"></a>PowerShell

Die .NET Framework-Bibliotheken für Azure Data Explorer können in PowerShell-Skripts verwendet werden. Weitere Informationen finden Sie unter [Verwenden der .NET-Client Bibliotheken von PowerShell](powershell/powershell.md).

## <a name="monaco-ide-integration"></a>Monaco-IDE-Integration

Das Paket `monaco-kusto` unterstützt die Integration mit dem Monaco-Webeditor.
Der von Microsoft entwickelte Monaco-Editor bildet die Grundlage für Visual Studio Code.
Weitere Informationen finden Sie unter [Monaco-kusto](monaco/monaco-kusto.md).