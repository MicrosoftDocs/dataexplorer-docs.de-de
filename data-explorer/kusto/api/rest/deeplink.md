---
title: 'Deep-Links für die Benutzeroberfläche: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt Deep-Links für die Benutzeroberfläche in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: aa47811bfe8004037cb04e642c234003087617a1
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863233"
---
# <a name="ui-deep-links"></a>Deep-Links für UI

Die Rest-API bietet eine Deep Link-Funktion, die es HTTP-Anforderungen ermöglicht, den Aufrufer `GET` zu einem UI-Tool umzuleiten Beispielsweise können Sie einen URI erstellen, der das Tool Kusto. Explorer öffnet, das Tool für einen bestimmten Cluster und eine bestimmte Datenbank automatisch konfiguriert, eine bestimmte Abfrage ausführt und die Ergebnisse für den Benutzer anzeigt.

Die Rest-API für Deep Links für die Benutzeroberfläche:

* Der Cluster (obligatorisch) wird häufig implizit definiert, wie der Dienst, der die Rest-API implementiert, kann jedoch durch Angabe des URI-Abfrage Parameters überschrieben werden `uri` .

* Die Datenbank (optional) wird als erstes und einziges Fragment des URI-Pfads angegeben. Die Datenbank ist für Abfragen und optional für Steuerungsbefehle obligatorisch.

* Der Abfrage-oder Steuerungs Befehl (optional) wird mithilfe des URI-Abfrage Parameters `query` oder des URI-Abfrage Parameters `querysrc` (der auf eine Webressource verweist, die die Abfrage enthält) angegeben.
  `query`kann im Text des Abfrage-oder Steuerelement Befehls selbst verwendet werden (codiert mithilfe der HTTP-Abfrage Parameter Codierung). Alternativ kann Sie in der Base64-Codierung der GZIP-Datei des Abfrage-oder Steuerungs Befehls Texts verwendet werden (sodass lange Abfragen komprimiert werden können, sodass Sie den standardmäßigen Browser-URI-Längen Limits entsprechen).

* Der Name der Cluster Verbindung (optional) wird mithilfe des URI-Abfrage Parameters angegeben `name` .

* Das UI-Tool wird mit dem `web` optionalen URI-Abfrage Parameter angegeben.
  `web=0`Gibt die Desktop Anwendung "Kusto. Explorer" an. `web=1`Gibt die Webanwendung "Kusto. Webexplorer" an.
  `web=2`ist die alte Version von Kusto. Webexplorer (basierend auf Application Insights Analytics). `web=3`ist Kusto. Webexplorer mit einem leeren Profil (es sind keine zuvor geöffneten Registerkarten oder Cluster verfügbar). Zuletzt kann der `web` Abfrage Parameter durch ersetzt werden, um `saw=1` die erkannte Version von Kusto. Explorer anzugeben.

Hier sind einige Beispiele für Verknüpfungen:

* `https://help.kusto.windows.net/`: Wenn ein Benutzer-Agent (z. b. ein Browser) eine `GET /` Anforderung ausgibt, wird er an das Standardbenutzer Oberflächen Tool umgeleitet, das zum Abfragen des Clusters konfiguriert ist `help` .
* `https://help.kusto.windows.net/Samples`: Wenn ein Benutzer-Agent (z. b. ein Browser) eine `GET /Samples` Anforderung ausgibt, wird er an das Standardbenutzer Oberflächen Tool umgeleitet, das zum Abfragen der `help` Cluster Datenbank konfiguriert ist `Samples` .
* `http://help.kusto.windows.net/Samples?query=StormEvents`: Wenn ein Benutzer (z. b. ein Browser) eine `GET /Samples?query=StormEvents` Anforderung ausgibt, wird er zum Standardbenutzer Oberflächen Tool umgeleitet, das zum Abfragen der `help` Cluster Datenbank konfiguriert `Samples` ist, und die `StormEvents` Abfrage ausstellen.

> [!NOTE]
> Die Deep-Link-URIs erfordern keine Authentifizierung, da die Authentifizierung über das für die Umleitung verwendete UI-Tool ausgeführt wird.
> Jeder `Authorization` http-Header wird, falls vorhanden, ignoriert.

> [!IMPORTANT]
> Aus Sicherheitsgründen werden Steuerungsbefehle von UI-Tools nicht automatisch ausgeführt, auch wenn `query` oder `querysrc` im Deep-Link angegeben sind.

## <a name="deep-linking-to-kustoexplorer"></a>Deep-Link zu Kusto. Explorer

Diese Rest-API führt die Umleitung aus, die das Desktop Client Tool Kusto. Explorer mit speziell erstellten Start Parametern installiert und ausführt, die eine Verbindung mit einem bestimmten Kusto-Engine-Cluster herstellen und eine Abfrage für diesen Cluster ausführen.

* Pfad: `/` [*DatabaseName*']
* Ben`GET`
* Abfrage Zeichenfolge:`web=0`

> [!NOTE]
> Eine Beschreibung der Umleitungs-URI-Syntax zum Starten von Kusto. Explorer finden Sie unter [Deep-Linking with Kusto. Explorer](../../tools/kusto-explorer-using.md#deep-linking-queries) .

## <a name="deep-linking-to-kustowebexplorer"></a>Deep-Link zu Kusto. Webexplorer

Diese Rest-API führt die Umleitung zu Kusto. Webexplorer, einer Webanwendung, durch.

* Pfad: `/` [*DatabaseName*']
* Ben`GET`
* Abfrage Zeichenfolge:`web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>Angeben des Abfrage-oder Steuerungs Befehls im URI

Wenn der URI-Abfrage Zeichenfolgen-Parameter `query` angegeben wird, muss er gemäß der URI-Abfrage Zeichenfolge für HTML-Regeln codiert werden. Alternativ kann der Text des Abfrage-oder Steuerungs Befehls mit gzip komprimiert und anschließend über Base64-Codierung codiert werden. Dies ermöglicht es Ihnen, längere Abfragen oder Steuerungsbefehle zu senden (da die letztere Codierungsmethode zu kürzeren URIs führt).

## <a name="specifying-the-query-or-control-command-by-indirection"></a>Angeben des Abfrage-oder Steuerungs Befehls durch Dereferenzierung

Wenn der Abfrage-oder Steuerelement Befehl sehr lang ist, überschreitet auch das Codieren mit gzip/Base64 die maximale URI-Länge des Benutzer-Agents. Alternativ wird der URI-Abfrage Zeichenfolgen `querysrc` -Parameter bereitgestellt, und sein Wert ist ein kurzer URI, der auf eine Webressource zeigt, die den Abfrage-oder Steuerungs Befehls Text enthält.

Dies kann z. b. der URI für eine Datei sein, die von Azure BLOB Storage gehostet wird.

> [!NOTE]
> Wenn der Deep-Link zum Webanwendungs-UI-Tool gehört, muss der Webdienst, der den Abfrage-oder Steuerungs Befehl bereitstellt (d. h. der Dienst, der den URI bereitstellt), für die `querysrc` Unterstützung von cors `dataexplorer.azure.com`
>
> Wenn außerdem Authentifizierungs-/Autorisierungsinformationen für diesen Dienst erforderlich sind, muss dieser Teil des URI selbst bereitgestellt werden.
>
> Wenn z. b. `querysrc` auf ein BLOB in Azure BLOB Storage verweist, muss das Speicherkonto für die Unterstützung von cors konfiguriert werden, und entweder muss das BLOB selbst öffentlich gemacht werden (sodass es ohne Sicherheitsansprüche heruntergeladen werden kann), oder es wird eine entsprechende Azure Storage-SAS zum URI hinzugefügt. Die cors-Konfiguration kann über die [Azure-Portal](https://portal.azure.com/) oder [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)ausgeführt werden.
> Informationen finden Sie [unter cors-Unterstützung in Azure Storage](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

