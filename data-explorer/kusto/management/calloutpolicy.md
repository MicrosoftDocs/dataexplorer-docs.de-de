---
title: 'Legende-Richtlinie: Azure Daten-Explorer'
description: In diesem Artikel wird die Legenden Richtlinie in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 6e3bb943347e4ea794733451fcf65674e5e23ca7
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452662"
---
# <a name="callout-policy"></a>Aufrufrichtlinie

Azure Daten-Explorer-Cluster können in vielen verschiedenen Szenarien mit externen Diensten kommunizieren.
Cluster Administratoren können die autorisierten Domänen für externe Aufrufe verwalten, indem Sie die Legenden Richtlinien des Clusters aktualisieren.

Callout-Richtlinien werden auf Cluster Ebene verwaltet und in die folgenden Typen eingeteilt.
* `kusto` -Steuert Azure Daten-Explorer-Cluster übergreifende Abfragen.
* `sql` -Steuert das [SQL-Plug](../query/sqlrequestplugin.md)-in.
* `cosmosdb` -Steuert das [cosmosdb-Plug](../query/cosmosdb-plugin.md)-in.
* `sandbox_artifacts`-Steuert Sandbox-Plug-ins ([python](../query/pythonplugin.md)  |  [R](../query/rplugin.md)).
* `external_data` -Steuert den Zugriff auf externe Daten über [externe Tabellen](../query/schema-entities/externaltables.md) oder den [externaldata](../query/externaldata-operator.md) -Operator.

Die Legenden Richtlinie besteht aus folgendem:

* **Callouttype** : definiert den Typ der Legende und kann `kusto` oder sein `sql` .
* **Callouturiregex** : gibt den zulässigen regulären Ausdruck der Legenden Domäne an.
* **Cancall:** gibt an, ob die Legende zulässige externe Aufrufe ist.

## <a name="predefined-callout-policies"></a>Vordefinierte Legenden Richtlinien

Die Tabelle enthält eine Reihe vordefinierter Legenden Richtlinien, die für alle Azure Daten-Explorer-Cluster vorkonfiguriert werden, um die Auswahl von Aufrufen von Diensten zu ermöglichen.

|Dienst      |Cloud        |Bezeichnung  |Zulässige Domänen |
|-------------|-------------|-------------|-------------|
|Kusto |`Public Azure` |Cluster übergreifende Abfragen |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |`Black Forest` |Cluster übergreifende Abfragen |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |`Fairfax` |Cluster übergreifende Abfragen |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |`Mooncake` |Cluster übergreifende Abfragen |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure-DB |`Public Azure` |SQL-Anforderungen |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure-DB |`Black Forest` |SQL-Anforderungen |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure-DB |`Fairfax` |SQL-Anforderungen |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure-DB |`Mooncake` |SQL-Anforderungen |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|Baselining-Dienst |Öffentliches Azure |Baselining-Anforderungen |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |

## <a name="control-commands"></a>Steuerungsbefehle

Die Befehle erfordern [alldatabasesadmin](access-control/role-based-authorization.md) -Berechtigungen.

**Alle konfigurierten Legenden Richtlinien anzeigen**

```kusto
.show cluster policy callout
```

**Ändern von Legenden Richtlinien**

```kusto
.alter cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**Hinzufügen eines Satzes zulässiger Legenden Aufrufe**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**Alle nicht unveränderlichen Legenden Richtlinien löschen**

```kusto
.delete cluster policy callout
```
