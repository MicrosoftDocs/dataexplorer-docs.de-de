---
title: series_exp_smoothing_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion series_exp_smoothing_fl () in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: eabdb80852d2a81f996d3e722dda7df969a914be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321810"
---
# <a name="series_exp_smoothing_fl"></a>series_exp_smoothing_fl()

Wendet einen grundlegenden exponentiellen Glättungs Filter für eine Reihe an.

Die Funktion `series_exp_smoothing_fl()` nimmt einen Ausdruck, der ein dynamisches numerisches Array enthält, als Eingabe und wendet einen [einfachen exponentiellen Glättungs](https://en.wikipedia.org/wiki/Exponential_smoothing#Basic_(simple)_exponential_smoothing_(Holt_linear)) Filter an.

> [!NOTE]
> Diese Funktion ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`series_exp_smoothing_fl(`*y_series* `, [` *Alpha*`])`
  
## <a name="arguments"></a>Argumente

* *y_series*: dynamische Array Zelle numerischer Werte.
* *Alpha*: ein optionaler realer Wert im Bereich [0-1], der die Gewichtung des letzten Punkts und die Gewichtung der vorherigen Punkte (d. h. `1-alpha` ) angibt. Der Standardwert ist 0,5.

## <a name="usage"></a>Verwendung

`series_exp_smoothing_fl()` ist eine benutzerdefinierte Funktion. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe einer [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_exp_smoothing_fl = (y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
;
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für persistente Verwendung [`.create function`](../management/create-function.md) . Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Basic exponential smoothing for a series")
series_exp_smoothing_fl(y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

---

:::image type="content" source="images/series-exp-smoothing-fl/exp-smoothing-demo.png" alt-text="Diagramm, das exponentielle Glättung künstlicher Reihen anzeigt" border="false":::
