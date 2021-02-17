---
title: Zulassen von mandantenübergreifenden Abfragen und Befehlen in Azure Data Explorer
description: Hier erfahren Sie, wie Sie Abfragen oder Befehle aus mehreren Mandanten in Azure Data Explorer zulassen.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: reference
ms.date: 01/06/2021
ms.openlocfilehash: e675fd0a3a50fa1959e4fb3a6a660546adcb4a36
ms.sourcegitcommit: ec290d02974b4bb28e44c8c4a981fb32b1f945a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98124405"
---
# <a name="allow-cross-tenant-queries-and-commands"></a>Zulassen von mandantenübergreifenden Abfragen und Befehlen 

Mehrere Mandanten können Abfragen und Befehle in einem einzelnen Azure Data Explorer-Cluster ausführen. In diesem Artikel erfahren Sie, wie Sie einem Cluster Zugriff auf Prinzipale eines anderen Mandanten gewähren.

Clusterbesitzer können ihren Cluster vor Abfragen und Befehlen von anderen Mandanten schützen. Dies erfolgt durch die Verwaltung der Clustereigenschaft `TrustedExternalTenants`. Die Eigenschaft `trustedExternalTenants` definiert explizit, welche Mandanten Abfragen und Befehle für den Cluster ausführen dürfen. Weitere Informationen finden Sie unter [Anforderungstext](/rest/api/azurerekusto/clusters/createorupdate#request-body).

> [!NOTE]
> Der Prinzipal, der Abfragen oder Befehle ausführen wird, muss zudem über eine entsprechende Datenbankrolle verfügen. Weitere Informationen finden Sie unter [Rollenbasierte Autorisierung in Kusto](./kusto/management/access-control/role-based-authorization.md). Die Überprüfung korrekter Rollen erfolgt nach der Überprüfung vertrauenswürdiger externer Mandanten.

## <a name="syntax"></a>Syntax

**Definieren bestimmter Mandanten**

`trustedExternalTenants``: [ {"`*value*`": "`*tenantId1*`" }, { "`*value*`": "`*tenantId2*`" }, ... ]`

**Zulassen aller Mandanten**

Das Array „trustedExternalTenants“ unterstützt außerdem die Notation mit Sternchen (*) für alle Mandanten, mit der Abfragen und Befehle von allen Mandanten zugelassen werden. 

`trustedExternalTenants``: [ { "`*value*`": "`*`" }]`

> [!NOTE]
> Der Standardwert für `trustedExternalTenants` ist „alle Mandanten“: `[ { "value": "*" }]`. Wenn das Array externer Mandanten bei der Clustererstellung nicht definiert wurde, kann es mit einem Clusteraktualisierungsvorgang überschrieben werden. Ein leeres Array wird nicht akzeptiert.

## <a name="example"></a>Beispiel

Aktualisieren Sie den Cluster mithilfe des folgenden Vorgangs:

```http
PATCH https://management.azure.com/subscriptions/12345678-1234-1234-1234-123456789098/resourceGroups/kustorgtest/providers/Microsoft.Kusto/clusters/kustoclustertest?api-version=2020-09-18
```

### <a name="request-body-specific-tenants"></a>Anforderungstext für spezifische Mandanten

```json
{
              "properties": { 
                           "trustedExternalTenants": [
                                         { "value": "tenantId1" }, 
                                         { "value": "tenantId2" }, 
                                         ...
                           ]
              }
}
```

### <a name="request-body-all-tenants"></a>Anforderungstext für alle Mandanten

```json
{
              "properties": { 
                           "trustedExternalTenants": [  { "value": "*" }  ]
              }
}

```
