---
title: series_moving_avg_udf ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion series_moving_avg_udf () in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 526aa1c505dafa681665cccb0c8a8e1d56f80def
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557864"
---
# <a name="series_moving_avg_udf"></a>series_moving_avg_udf ()

Wendet einen Filter für den gleitenden Durchschnitt für eine Reihe an.

Die Funktion `series_moving_avg_udf()` nimmt einen Ausdruck, der ein dynamisches numerisches Array enthält, als Eingabe an und wendet einen [einfachen gleitenden Durchschnitts](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) Filter an.

> [!NOTE]
> Diese Funktion ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`series_moving_avg_udf(`*y_series* `,` *n* `, [` *zentrieren*`])`
  
## <a name="arguments"></a>Argumente

* *y_series*: dynamische Array Zelle numerischer Werte.
* *n*: die Breite des Filters für den gleitenden Durchschnitt.
* *Center*: ein optionaler boolescher Wert, der angibt, ob der gleitende Durchschnitt eine der folgenden Optionen ist:
    * wird symmetrisch in einem Fenster vor und nach dem aktuellen Punkt angewendet, oder 
    * wird auf ein Fenster vom aktuellen Punkt nach hinten angewendet. <br>
    Standardmäßig ist " *Center* " auf "false" eingestellt.

## <a name="usage"></a>Verbrauch

* `series_moving_avg_udf()` ist eine benutzerdefinierte Funktion. Sie können entweder den Code in die Abfrage einbetten oder ihn in der Datenbank installieren:
    * Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe einer [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.
    * Speichern Sie die Funktion für die wiederkehrende Verwendung mithilfe der [. Create-Funktion](../management/create-function.md). <br>
        Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

# <a name="ad-hoc-usage"></a>[Ad-hoc-Verwendung](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_udf = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

# <a name="persistent-usage"></a>[Persistente Verwendung](#tab/persistent)

* **Einmalige Installation**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_udf(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

* **Verwendung**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-udf/moving-average-five-bins.png" alt-text="Diagramm, das den gleitenden Durchschnitt von 5 Containern darstellt" border="false":::
