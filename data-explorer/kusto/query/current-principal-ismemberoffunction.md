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
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 03d565c9eb61703b326a01b31bb6b1a79742f006
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766056"
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
