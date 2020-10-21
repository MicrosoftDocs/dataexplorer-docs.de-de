---
title: current_principal_details ()-Azure Daten-Explorer
description: In diesem Artikel wird current_principal_details () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 387504d49b2c8be52357be74e69cdbde581c1298
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252521"
---
# <a name="current_principal_details"></a>current_principal_details()

Gibt Details des Prinzipals zurück, der die Abfrage ausgeführt hat.

## <a name="syntax"></a>Syntax

`current_principal_details()`

## <a name="returns"></a>Rückgabe

Die Details des aktuellen Prinzipals als `dynamic` .

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|T|
|---|
|{<br>  "UserPrincipalName": " user@fabrikam.com ",<br>  "Identityprovider": " https://sts.windows.net ",<br>  "Authority": "72F 988bf -86bb f 1-41af-91ab-2d7cd011db47",<br>  "MFA": "true",<br>  "Type": "aaduser",<br>  "Display Name": "James Smith (UPN: user@fabrikam.com )",<br>  "ObjectID": "346e950 e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "Vollständig": NULL,<br>  "Notizen": NULL<br>}|
