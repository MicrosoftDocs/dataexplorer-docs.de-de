---
title: current_principal_is_member_of() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden current_principal_is_member_of() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6d33fd19181e1eba93b54f684864fed062701307
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663847"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

Überprüft die Gruppenmitgliedschaft oder die Hauptidentität des aktuellen Prinzipals, der die Abfrage ausführt.

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

* *Liste der Ausdrücke* - eine durch Kommas getrennte Liste von Zeichenfolgenliteralen, wobei jedes Literal eine QQN-Zeichenfolge (Principal Fully-Qualified Name) ist, die wie folgt gebildet wurde:  
*PrinciplaType*`=`*PrincipalId*`;`*TenantId*

| PrincipalType   | FQN-Präfix  |
|-----------------|-------------|
| AAD-Benutzer        | `aaduser=`  |
| AAD-Gruppe       | `aadgroup=` |
| AAD-Anwendung | `aadapp=`   |

**Rückgabe**

Die Funktion gibt Folgendes zurück:
* `true`: Wenn der aktuelle Prinzipal, der die Abfrage ausführt, für mindestens ein Eingabeargument erfolgreich abgeglichen wurde.
* `false`: Wenn der aktuelle Prinzipal, der `aadgroup=` die Abfrage ausführt, kein Mitglied `aaduser=` `aadapp=` von FQN-Argumenten war und keinem der oder FQN-Argumente entspricht.
* `(null)`: Wenn der aktuelle Prinzipal, der `aadgroup=` die Abfrage ausführt, kein Mitglied `aaduser=` `aadapp=` von FQN-Argumenten war und keinem der oder FQN-Argumente entspricht, und mindestens ein FQN-Argument nicht erfolgreich aufgelöst wurde (wurde in AAD nicht vorgegeben). 

> [!NOTE]
> Da die Funktion einen Drei-Status-Wert (`true`, `false`, und `null`) zurückgibt, ist es wichtig, sich nur auf positive Rückgabewerte zu verlassen, um eine erfolgreiche Mitgliedschaft zu bestätigen. Mit anderen Worten, die folgenden Ausdrücke sind NICHT identisch:
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

Verwenden von dynamischen Arrays anstelle von Multple-Argumenten:

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

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end