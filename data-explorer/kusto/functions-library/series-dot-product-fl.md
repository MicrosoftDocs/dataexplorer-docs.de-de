---
title: series_dot_product_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion series_dot_product_fl () in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: 9b1c6db6af651ae3665c07bda8598212883a8f6c
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92184143"
---
# <a name="series_dot_product_fl"></a>series_dot_product_fl ()

Berechnet das Punktprodukt von zwei numerischen Vektoren.

Die Funktion `series_dot_product_fl()` nimmt einen Ausdruck, der zwei dynamische numerische Arrays enthält, als Eingabe auf und berechnet das [Punktprodukt](https://en.wikipedia.org/wiki/Dot_product).

> [!NOTE]
> Diese Funktion ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`series_dot_product_fl(`*vec1* `,` *vec2*`)`
  
## <a name="arguments"></a>Argumente

* *vec1*: dynamische Array Zelle numerischer Werte.
* *vec2*: dynamische Array Zelle numerischer Werte, dieselbe Länge wie *vec1*.

## <a name="usage"></a>Verwendung

`series_dot_product_fl()` ist eine benutzerdefinierte Funktion. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe einer [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_dot_product_fl=(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
};
//
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für die permanente Verwendung die [. Create-Funktion](../management/create-function.md). Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate the dot product of 2 numerical arrays")
series_dot_product_fl(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

---

:::image type="content" source="images/series-dot-product-fl/dot-product-result.png" alt-text="Tabelle, die das Ergebnis des Punkt Produkts von zwei Vektoren mithilfe von benutzerdefinierten Funktions series_dot_product_fl in Azure anzeigt Daten-Explorer" border="false":::
