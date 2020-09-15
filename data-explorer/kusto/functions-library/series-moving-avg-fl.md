---
title: series_moving_avg_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion series_moving_avg_fl () in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 24fcdb685d9238416e6652953ab47ff82f91bd72
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90075137"
---
# <a name="series_moving_avg_fl"></a>series_moving_avg_fl ()

Wendet einen Filter für den gleitenden Durchschnitt für eine Reihe an.

Die Funktion `series_moving_avg_fl()` nimmt einen Ausdruck, der ein dynamisches numerisches Array enthält, als Eingabe an und wendet einen [einfachen gleitenden Durchschnitts](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) Filter an.

> [!NOTE]
> Diese Funktion ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`series_moving_avg_fl(`*y_series* `,` *n* `, [` *zentrieren*`])`
  
## <a name="arguments"></a>Argumente

* *y_series*: dynamische Array Zelle numerischer Werte.
* *n*: die Breite des Filters für den gleitenden Durchschnitt.
* *Center*: ein optionaler boolescher Wert, der angibt, ob der gleitende Durchschnitt eine der folgenden Optionen ist:
    * wird symmetrisch in einem Fenster vor und nach dem aktuellen Punkt angewendet, oder 
    * wird auf ein Fenster vom aktuellen Punkt nach hinten angewendet. <br>
    Standardmäßig ist " *Center* " auf "false" eingestellt.

## <a name="usage"></a>Verwendung

`series_moving_avg_fl()` ist eine benutzerdefinierte Funktion. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe einer [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_fl = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_fl(num, 5, True)
| render timechart 
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für die permanente Verwendung die [. Create-Funktion](../management/create-function.md). Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_fl(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_fl(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-fl/moving-average-five-bins.png" alt-text="Diagramm, das den gleitenden Durchschnitt von 5 Containern darstellt" border="false":::
