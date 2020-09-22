---
title: predict_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "predict_fl ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2020
ms.openlocfilehash: 29db6fd462311ab30b5c477d27b04606ecfd2915
ms.sourcegitcommit: 5aba5f694420ade57ef24b96699d9b026cdae582
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998958"
---
# <a name="predict_fl"></a>predict_fl()

Die Funktion `predict_fl()` prognostiziert mithilfe eines vorhandenen trainierten Machine Learning-Modells. Dieses Modell wurde mit [scikit-Learn](https://scikit-learn.org/stable/)erstellt, in eine Zeichenfolge serialisiert und in einer standardmäßigen Azure-Daten-Explorer Tabelle gespeichert.

> [!NOTE]
> * `predict_fl()` ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md).
> * Diese Funktion enthält Inline-python und erfordert [die Aktivierung des python ()-Plug](../query/pythonplugin.md#enable-the-plugin) -ins auf dem Cluster. Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`T | invoke predict_fl(`*models_tbl* `,` *MODEL_NAME* `,` *features_cols* `,` *pred_col*`)`

## <a name="arguments"></a>Argumente

* *models_tbl*: der Name der Tabelle mit allen serialisierten Modellen. Diese Tabelle muss die folgenden Spalten enthalten:
    * *Name*: der Modellname
    * Zeit *Stempel*: Zeit des Modell Trainings
    * *Model*: Zeichen folgen Darstellung des serialisierten Modells
* *MODEL_NAME*: der Name des zu verwendenden Modells.
* *features_cols*: dynamisches Array, das die Namen der Features-Spalten enthält, die vom Modell für die Vorhersage verwendet werden.
* *pred_col*: der Name der Spalte, in der die Vorhersagen gespeichert werden.

## <a name="usage"></a>Verwendung

`predict_fl()` ist eine benutzerdefinierte [Tabellen Funktion](../query/functions/user-defined-functions.md#tabular-function) , die mit dem [Aufruf Operator](../query/invokeoperator.md)angewendet werden soll. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Für die Ad-hoc-Verwendung Betten Sie den Code mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let predict_fl=(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    'clf1 = pickle.loads(bmodel)\n'
    'df1 = df[features_cols]\n'
    'predictions = clf1.predict(df1)\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples
    | evaluate python(typeof(*), code, kwargs)
};
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=false
| invoke predict_fl(ML_Models, 'Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für die permanente Verwendung die [. Create-Funktion](../management/create-function.md). Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "Predict using ML model, build by Scikit-learn")
predict_fl(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    'clf1 = pickle.loads(bmodel)\n'
    'df1 = df[features_cols]\n'
    'predictions = clf1.predict(df1)\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=false
| invoke predict_fl(ML_Models, 'Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

---

Konfusions Matrix:
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Occupancy   pred_Occupancy  n
TRUE        TRUE            3006
FALSE       TRUE            112
TRUE        FALSE           15
FALSE       FALSE           9284
```
