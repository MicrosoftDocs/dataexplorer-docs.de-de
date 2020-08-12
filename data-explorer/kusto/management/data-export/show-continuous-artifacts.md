---
title: 'Fortlaufende Datenexport Artefakte anzeigen: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie fortlaufende Datenexport Artefakte in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 68b33ffc46df1e3bc4f63f8f7e9c86786116308b
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149496"
---
# <a name="show-continuous-export-artifacts"></a>Fortlaufende Export Artefakte anzeigen

Gibt alle Artefakte zurück, die vom fortlaufenden Export in allen Ausführungen exportiert werden. Filtern Sie die Ergebnisse nach der timestamp-Spalte im Befehl, um nur die relevanten Datensätze anzuzeigen. Der Verlauf der exportierten Artefakte wird 14 Tage lang aufbewahrt. 

## <a name="syntax"></a>Syntax

`.show` `continuous-export` *ContinuousExportName* `exported-artifacts`

## <a name="properties"></a>Eigenschaften

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Name des fortlaufenden Exports. |

## <a name="output"></a>Output

| Output-Parameter  | type     | BESCHREIBUNG                            |
|-------------------|----------|----------------------------------------|
| Timestamp         | Datetime | Zeitstempel der fortlaufenden Export Laufzeit |
| Externaltablename | String   | Name der externen Tabelle             |
| `Path`              | String   | Ausgabepfad                            |
| Numrecords        | long     | Anzahl der in den Pfad exportierten Datensätze     |

## <a name="example"></a>Beispiel

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Timestamp                   | Externaltablename | `Path`             | Numrecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31:30.2634216 | Externalblob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |
