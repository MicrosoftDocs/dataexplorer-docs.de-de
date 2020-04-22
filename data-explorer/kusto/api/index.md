---
title: Übersicht über die Azure Data Explorer-API – Azure Data Explorer | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Azure Data Explorer-API in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: c791ae0be0c895a4744e761c58e7d455aa0a22b9
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610265"
---
# <a name="azure-data-explorer-api-overview"></a>Übersicht über die Azure Data Explorer-API

Der Azure Data Explorer-Dienst unterstützt folgende Kommunikationsendpunkte:

1. Einen [REST-API](#rest-api)-Endpunkt zum Abfragen und Verwalten der Daten in Azure Data Explorer.
   Dieser Endpunkt unterstützt die [Kusto-Abfragesprache](../query/index.md) für Abfragen sowie die [Steuerungsbefehle](../management/index.md).
2. Einen [MS-TDS](#ms-tds)-Endpunkt, der einen Teil des TDS-Protokolls (Tabular Data Stream) von Microsoft implementiert, das von den Microsoft SQL Server-Produkten verwendet wird.
   Dieser Endpunkt ist vor allem nützlich für bereits vorhandene Tools, die mit einem SQL Server-Endpunkt für Abfragen kommunizieren können.
3. Einen [ARM-Endpunkt (Azure Resource Manager)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto). Dieser wird von Azure-Diensten standardmäßig verwendet, um Ressourcen wie etwa Azure Data Explorer-Cluster zu verwalten.

Azure Data Explorer bietet eine Reihe von Clientbibliotheken, die die oben genannten Endpunkte nutzen, um den programmgesteuerten Zugriff zu vereinfachen:

1. .NET SDK
2. Python SDK
3. Java-SDK
4. Node SDK
5. Go SDK
6. PowerShell
7. R

## <a name="rest-api"></a>REST-API

Für die Kommunikation mit einem Azure Data Explorer-Dienst wird in erster Linie die REST-API des Diensts verwendet. Dieser vollständig dokumentierte Endpunkt bietet Aufrufern folgende Möglichkeiten:

1. Daten abfragen
2. Abfragen und Ändern von Metadaten
3. Erfassen von Daten
4. Abfragen des Integritätsstatus des Diensts
5. Ressourcen verwalten

Die verschiedenen Azure Data Explorer-Dienste kommunizieren untereinander über die gleiche öffentlich verfügbare REST-API.

Neben der Unterstützung von REST-API-Anforderungen für Azure Data Explorer steht eine Reihe von Clientbibliotheken zur Verfügung, die die Verwendung des Diensts ermöglichen, ohne sich mit dem REST-API-Protokoll zu befassen.

## <a name="ms-tds"></a>MS-TDS

Alternativ zur Verbindungsherstellung mit Azure Data Explorer und zur Abfrage der Daten unterstützt Azure Data Explorer das Microsoft SQL Server-Kommunikationsprotokoll (MS-TDS) und ermöglicht eingeschränkt die Ausführung von T-SQL-Abfragen. Dadurch können Benutzer Abfragen in Azure Data Explorer mit einer vertrauten Abfragesyntax (T-SQL) und vertrauten Datenbankclient-Tools wie LINQPad, sqlcmd, Tableau, Excel oder Power BI ausführen.

Ausführlichere Informationen zu MS-TDS finden Sie auf [dieser Seite](tds/index.md).

## <a name="net-framework-libraries"></a>.NET Framework-Bibliotheken

Die .NET Framework-Bibliotheken sind die empfohlene Methode zum programmgesteuerten Aufrufen von Azure Data Explorer-Funktionen.
Es stehen verschiedene Bibliotheken zur Verfügung:

- [**Kusto.Data (Kusto-Clientbibliothek):** ](./netfx/about-kusto-data.md) Diese Bibliothek kann zum Abfragen von Daten und Metadaten sowie zum Vornehmen von Änderungen verwendet werden.
- [**Kusto.Ingest (Kusto-Erfassungsbibliothek):** ](netfx/about-kusto-ingest.md) Diese Bibliothek nutzt die Bibliothek „Kusto.Data“ und erweitert sie, um die Datenerfassung zu vereinfachen.


Die **Kusto-Clientbibliothek** (Kusto.Data) basiert auf der Kusto-REST-API und sendet HTTPS-Anforderungen an den Kusto-Zielcluster. 

Die **Kusto-Erfassungsbibliothek** (Kusto.Ingest) nutzt „Kusto.Data“.



Alle oben genannten Bibliotheken verwenden Azure-APIs (beispielsweise die Azure Storage-API oder die Azure Active Directory-API).

## <a name="python-libraries"></a>Python-Bibliotheken

Azure Data Explorer stellt eine Python-Clientbibliothek bereit, die Aufrufern das Senden von Datenabfragen und Steuerungsbefehle ermöglicht.

## <a name="r-library"></a>R-Bibliothek

Azure Data Explorer stellt eine R-Clientbibliothek bereit, die Aufrufern das Senden von Datenabfragen und Steuerungsbefehlen ermöglicht.



## <a name="using-azure-data-explorer-from-powershell"></a>Verwenden von Azure Data Explorer über PowerShell

Die .NET Framework-Bibliotheken für Azure Data Explorer können in PowerShell-Skripts verwendet werden.
Ein Beispiel finden Sie unter [Verwenden der .NET-Clientbibliotheken über PowerShell](powershell/powershell.md).

## <a name="ide-integration"></a>IDE-Integration

Das Paket `monaco-kusto` unterstützt die Integration in den Monaco-Editor. Dabei handelt es sich um einen von Microsoft entwickelten Web-Editor, der die Grundlage für Visual Studio Code bildet.
Erfahren Sie mehr über das Paket [monaco-kusto](monaco/monaco-kusto.md).