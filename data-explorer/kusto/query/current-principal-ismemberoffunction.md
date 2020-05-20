---
title: current_principal_is_member_of ()-Azure Daten-Explorer
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
ms.openlocfilehash: fdd062abc02ff1d98ba935fc9016d0a1505e69c3
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550400"
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

* *Liste der Ausdrücke* : eine durch Trennzeichen getrennte Liste von Zeichenfolgenliteralen, wobei jedes Literale eine voll qualifizierte voll qualifizierte namens Zeichenfolge (fqn) ist:  
*Principlatype* `=` *Principalid* `;` *Tenantid*

| Principaltype   | Voll qualifizierte Präfix  |
|-----------------|-------------|
| Aad-Benutzer        | `aaduser=`  |
| Aad-Gruppe       | `aadgroup=` |
| Aad-Anwendung | `aadapp=`   |

**Rückgabe**
  
Die Funktion gibt Folgendes zurück:
* `true`:, wenn der aktuelle Prinzipal, der die Abfrage ausgeführt hat, mit mindestens einem Eingabe Argument erfolgreich abgeglichen wurde.
* `false`: Wenn der aktuelle Prinzipal, der die Abfrage ausgeführt, kein Member eines `aadgroup=` fqn-Arguments war und nicht mit einem der- `aaduser=` oder `aadapp=` fqn-Argumente übereinstimmt.
* `(null)`: Wenn der aktuelle Prinzipal, der die Abfrage ausgeführt, kein Member eines `aadgroup=` fqn-Arguments war und nicht mit einem `aaduser=` der `aadapp=` -oder fqn-Argumente übereinstimmt, und mindestens ein fqn-Argument nicht erfolgreich aufgelöst wurde (wurde nicht in Azure AD gedrückt). 

> [!NOTE]
> Da die Funktion einen Tri-State-Wert zurückgibt ( `true` , `false` und `null` ), ist es wichtig, sich nur auf positive Rückgabewerte zu verlassen, um eine erfolgreiche Mitgliedschaft zu bestätigen. Mit anderen Worten: die folgenden Ausdrücke sind nicht identisch:
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples -->
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

Verwenden von dynamischem Array anstelle mehrerer Argumente:

<!-- csl: https://help.kusto.windows.net/Samples -->
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
