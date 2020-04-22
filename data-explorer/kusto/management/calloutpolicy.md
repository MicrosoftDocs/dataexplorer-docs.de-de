---
title: Callout-Richtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Callout-Richtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: df57c4901cef74d574108d2c6e75672d1faba75c
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744518"
---
# <a name="callout-policy"></a>Aufrufrichtlinie

## <a name="overview"></a>Übersicht

Azure Data Explorer-Cluster können in vielen verschiedenen Szenarien mit externen Diensten kommunizieren.
Clusteradministratoren können die zulässigen Domänen für externe Anrufe verwalten, indem sie die Legendenrichtlinie des Clusters aktualisieren.

Legendenrichtlinien werden auf Clusterebene verwaltet und in die folgenden Typen eingeteilt:
* `kusto`- steuert Azure Data Explorer-clusterübergreifende Abfragen.
* `sql`- steuert das [SQL-Plugin](../query/sqlrequestplugin.md).


* `webapi`- steuert andere externe Webaufrufe.
* `sandbox_artifacts`- steuert sandkastened plugins ([python](../query/pythonplugin.md) | [R](../query/rplugin.md)).

Die Legendenrichtlinie besteht aus den folgenden Komponenten:
* **CalloutType** definiert den Typ der Legenden und kann einer der folgenden sein: kusto, sql oder webapi
* **CalloutUriRegex** gibt den zulässigen Regex der Domäne des Callouts an.
* **CanCall** gibt an, ob die Legenden externe Anrufe zulässt.

## <a name="predefined-callout-policies"></a>Vordefinierte Callout-Richtlinien

Es gibt eine Reihe vordefinierter Legendenrichtlinien, die in allen Azure Data Explorer-Clustern unveränderlich vorkonfiguriert sind, um Callouts für die Auswahl von Diensten zu erleichtern.

|Dienst      |Cloud        |Bezeichnung  |Zulässige Domänen |
|-------------|-------------|-------------|-------------|
|Kusto |Öffentliches Azure |Clusterübergreifende Abfragen |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |Schwarzwald |Clusterübergreifende Abfragen |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |Fairfax |Clusterübergreifende Abfragen |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |Mooncake |Clusterübergreifende Abfragen |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |Öffentliches Azure |SQL-Anforderungen |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |Schwarzwald |SQL-Anforderungen |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |Fairfax |SQL-Anforderungen |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |Mooncake |SQL-Anforderungen |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|Baselining-Service |Öffentliches Azure |Baselining-Anforderungen |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |


## <a name="control-commands"></a>Steuerungsbefehle

Die Befehle erfordern [AllDatabasesAdmin-Berechtigungen.](access-control/role-based-authorization.md)

**Alle konfigurierten Legendenrichtlinien anzeigen**

```kusto
.show cluster policy callout
```

**Ändern der beendenden Callout-Richtlinien**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**Hinzufügen einer Reihe zulässiger Legenden**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**Löschen aller nicht unveränderlichen Legendenrichtlinien**

```kusto
.delete cluster policy callout
```
