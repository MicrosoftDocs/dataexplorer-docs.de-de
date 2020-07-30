---
title: current_principal_details ()-Azure Daten-Explorer
description: In diesem Artikel wird current_principal_details () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: cf10fcfd66b7a9240746a904e4979c8091c3518b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348682"
---
# <a name="current_principal_details"></a>current_principal_details()

Gibt Details des Prinzipals zurück, der die Abfrage ausgeführt hat.

## <a name="syntax"></a>Syntax

`current_principal_details()`

## <a name="returns"></a>Gibt zurück

Die Details des aktuellen Prinzipals als `dynamic` .

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName": " user@fabrikam.com ",<br>  "Identityprovider": " https://sts.windows.net ",<br>  "Authority": "72F 988bf -86bb f 1-41af-91ab-2d7cd011db47",<br>  "MFA": "true",<br>  "Type": "aaduser",<br>  "Display Name": "James Smith (UPN: user@fabrikam.com )",<br>  "ObjectID": "346e950 e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "Vollständig": NULL,<br>  "Notizen": NULL<br>}|
