---
title: Ui-Tiefenverknüpfungen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden die Tiefenverknüpfungen der Benutzeroberfläche in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: c9535ced274ccdbe38f8d9a765e98d11e7b381e5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523680"
---
# <a name="ui-deep-links"></a>UI-Tiefenverknüpfungen

Die REST-API bietet eine Deep `GET` Link-Funktionalität, mit der HTTP-Anforderungen den Aufrufer an ein UI-Tool umleiten können. Sie können z. B. einen URI erstellen, der das Werkzeug Kusto.Explorer öffnet, es automatisch für einen bestimmten Cluster und eine bestimmte Datenbank konfiguriert, eine bestimmte Abfrage ausführt und deren Ergebnisse dem Benutzer anzeigt.

Die UI-Tiefenverknüpfungen REST-API:

* Cluster (obligatorisch) wird häufig implizit als der Dienst definiert, der die REST-API implementiert, `uri`kann jedoch durch Angabe des URI-Abfrageparameters überschrieben werden.

* Die Datenbank (optional) wird als erstes und einziges Fragment des URI-Pfads angegeben. Die Datenbank ist für Abfragen obligatorisch und für Steuerbefehle optional.

* Der Abfrage- oder Steuerungsbefehl (optional) wird `query`mithilfe des URI-Abfrageparameters oder des URI-Abfrageparameters `querysrc` (der auf eine Webressource verweist, die die Abfrage enthält) angegeben.
  `query`kann im Text der Abfrage oder des Steuerbefehls selbst verwendet werden (kodiert mit der HTTP-Abfrageparametercodierung). Alternativ kann es in der base64-Codierung des gzip des Abfrage- oder Steuerbefehlstextes verwendet werden (so dass lange Abfragen so komprimiert werden können, dass sie den standardmäßigen URI-Längenbeschränkungen des Browsers entsprechen).

* Der Name der Clusterverbindung (optional) wird mithilfe `name`des URI-Abfrageparameters angegeben.

* Das UI-Tool wird `web` mithilfe des optionalen URI-Abfrageparameters angegeben.
  `web=0`zeigt die Desktopanwendung Kusto.Explorer an. `web=1`zeigt die Kusto.WebExplorer-Webanwendung an.
  `web=2`ist die alte Version von Kusto.WebExplorer (basierend auf Application Insights Analytics). `web=3`ist der Kusto.WebExplorer mit einem leeren Profil (es sind keine zuvor geöffneten Registerkarten oder Cluster verfügbar). Zuletzt kann `web` der Abfrageparameter `saw=1` ersetzt werden, um die SAW-Version von Kusto.Explorer anzugeben.

Hier sind einige Beispiele für Links:

* `https://help.kusto.windows.net/`: Wenn ein Benutzer-Agent (z. `GET /` B. ein Browser) eine Anforderung ausgibt, `help` wird sie an das Standard-UI-Tool umgeleitet, das für die Abfrage des Clusters konfiguriert ist.
* `https://help.kusto.windows.net/Samples`: Wenn ein Benutzer-Agent (z. `GET /Samples` B. ein Browser) eine Anforderung ausgibt, `help` wird `Samples` er an das Standard-UI-Tool umgeleitet, das für die Abfrage der Clusterdatenbank konfiguriert ist.
* `http://help.kusto.windows.net/Samples?query=StormEvents`: Wenn ein Benutzer (z. `GET /Samples?query=StormEvents` B. ein Browser) eine Anforderung ausstellt, wird `help` `Samples` er an das `StormEvents` Standard-UI-Tool umgeleitet, das für die Abfrage der Clusterdatenbank konfiguriert ist, und die Abfrage.

> [!NOTE]
> Die Deep Link-URIs erfordern keine Authentifizierung, da die Authentifizierung vom UI-Tool ausgeführt wird, das für die Umleitung verwendet wird.
> Jeder `Authorization` HTTP-Header wird, sofern angegeben, ignoriert.

> [!IMPORTANT]
> Aus Sicherheitsgründen führen UI-Tools keine Steuerbefehle `query` `querysrc` automatisch aus, auch wenn oder dies in der Tiefenverknüpfung angegeben ist.

## <a name="deep-linking-to-kustoexplorer"></a>Tiefenverknüpfung mit Kusto.Explorer

Diese REST-API führt eine Umleitung durch, die das Kusto.Explorer-Desktopclienttool mit speziell gestalteten Startparametern installiert und ausführt, die eine Verbindung zu einem bestimmten Kusto-Modulcluster öffnen und eine Abfrage für diesen Cluster ausführen.

* Pfad: `/` [*Datenbankname*']
* Verb:`GET`
* Abfragezeichenfolge:`web=0`

> [!NOTE]
> Eine Beschreibung der UMleitungs-URI-Syntax zum Starten von Kusto.Explorer finden Sie unter [Deep-linking with Kusto.Explorer.](../../tools/kusto-explorer.md#deep-linking-queries)

## <a name="deep-linking-to-kustowebexplorer"></a>Tiefenverknüpfung mit Kusto.WebExplorer

Diese REST-API führt eine Umleitung zu Kusto.WebExplorer, einer Webanwendung, durch.

* Pfad: `/` [*Datenbankname*']
* Verb:`GET`
* Abfragezeichenfolge:`web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>Angeben des Abfrage- oder Steuerbefehls im URI

Wenn der PARAMETER `query` URI-Abfragezeichenfolge angegeben wird, muss er gemäß den HTML-Regeln der URI-Abfragezeichenfolge codiert werden. Alternativ kann der Text des Abfrage- oder Steuerbefehls durch gzip komprimiert und dann über base64-Codierung codiert werden. Auf diese Weise können Sie längere Abfragen oder Steuerbefehle senden (da letztere Codierungsmethode zu kürzeren URIs führt).

## <a name="specifying-the-query-or-control-command-by-indirection"></a>Angeben des Abfrage- oder Steuerungsbefehls durch Indirektion

Wenn der Abfrage- oder Steuerungsbefehl sehr lang ist, überschreitet selbst die Codierung mit gzip/base64 die maximale URI-Länge des Benutzer-Agents. Alternativ wird der URI-Abfragezeichenfolgenparameter `querysrc` bereitgestellt, und sein Wert ist ein kurzer URI, der auf eine Webressource verweist, die den Abfrage- oder Steuerbefehlstext enthält.

Dies kann z. B. der URI für eine Datei sein, die von Azure Blob Storage gehostet wird.

> [!NOTE]
> Wenn sich der Deep Link mit dem Benutzeroberflächentool für Webanwendungen befindet, muss der `querysrc` Webdienst, der den Abfrage- `dataexplorer.azure.com`oder Steuerelementbefehl bereitstellt (d. h. der Dienst, der den URI bereitstellt), so konfiguriert werden, dass er CORS für unterstützt.
>
> Wenn authentifizierungs-/Autorisierungsinformationen von diesem Dienst benötigt werden, müssen diese außerdem als Teil des URI selbst bereitgestellt werden.
>
> Wenn `querysrc` beispielsweise auf ein Blob in Azure Blob Storage verweist, muss das Speicherkonto für die Unterstützung von CORS konfiguriert werden, und entweder das Blob selbst öffentlich machen (damit es ohne Sicherheitsansprüche heruntergeladen werden kann) oder dem URI eine entsprechende Azure Storage SAS hinzufügen. Die CORS-Konfiguration kann über das [Azure-Portal](https://portal.azure.com/) oder über [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)erfolgen.
> Weitere Informationen finden Sie unter [CORS-Unterstützung in Azure Storage](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

