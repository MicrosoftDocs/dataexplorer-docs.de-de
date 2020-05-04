---
title: current_principal_is_member_of ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird current_principal_is_member_of () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4b6f7d0b9ab4074f16ca00b4a3febb1a17351736
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737758"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

Überprüft die Gruppenmitgliedschaft oder die Prinzipal Identität des aktuellen Prinzipals, der die Abfrage ausführt.

```kusto
print current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

**Syntax**

`current_principal_is_member_of`(`*list of string literals*`)

**Argumente**

* eine *Liste von Ausdrücken* : eine durch Trennzeichen getrennte Liste von Zeichenfolgenliteralen, wobei jedes Literale eine voll qualifizierte fqn-Zeichenfolge (Uniform-Qualified-Name) ist, die als:  
*Principlatype*`=`*principalid*`;`*tenantid*

| Principaltype   | Voll qualifizierte Präfix  |
|-----------------|-------------|
| Aad-Benutzer        | `aaduser=`  |
| Aad-Gruppe       | `aadgroup=` |
| Aad-Anwendung | `aadapp=`   |

**Rückgabe**

Die Funktion gibt Folgendes zurück:
* `true`:, wenn der aktuelle Prinzipal, der die Abfrage ausgeführt hat, mit mindestens einem Eingabe Argument erfolgreich abgeglichen wurde.
* `false`: Wenn der aktuelle Prinzipal, der die Abfrage ausgeführt, kein `aadgroup=` Member eines fqn-Arguments war und nicht mit einem `aaduser=` der `aadapp=` -oder fqn-Argumente übereinstimmt.
* `(null)`: Wenn der aktuelle Prinzipal, der die Abfrage ausgeführt, kein `aadgroup=` Member eines fqn-Arguments war und nicht mit einem `aaduser=` der `aadapp=` fqn-Argumente oder übereinstimmt und mindestens ein fqn-Argument nicht erfolgreich aufgelöst wurde (wurde nicht in Aad vorgegeben). 

> [!NOTE]
> Da die Funktion einen Tri-State-Wert zurück`true`gibt `false`(, `null`und), ist es wichtig, sich nur auf positive Rückgabewerte zu verlassen, um eine erfolgreiche Mitgliedschaft zu bestätigen. Mit anderen Worten: die folgenden Ausdrücke sind nicht identisch:
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**Beispiel**

```kusto
print result=current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

| result |
|--------|
| (null) |

Verwenden dynamischer Arrays anstelle von mehrdimensionsargumenten:

```kusto
print result=current_principal_is_member_of(
    dynamic([
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    ]))
```

| result |
|--------|
| (null) |

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
