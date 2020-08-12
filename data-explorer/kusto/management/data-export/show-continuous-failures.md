---
title: 'Fortlaufende Datenexport Fehler anzeigen: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie fortlaufende Datenexport Fehler in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 54c95e3beecd25b504c52c2fe06ffa51160ac8ca
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149456"
---
# <a name="show-continuous-export-failures"></a>Fortlaufende Export Fehler anzeigen

Gibt alle Fehler zurück, die als Teil des fortlaufenden Exports protokolliert wurden. Filtern Sie die Ergebnisse nach der timestamp-Spalte im Befehl, um nur den gewünschten Zeitbereich anzuzeigen. 

## <a name="syntax"></a>Syntax

`.show` `continuous-export` *ContinuousExportName* `failures`

## <a name="properties"></a>Eigenschaften

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Name des fortlaufenden Exports  |

## <a name="output"></a>Output

| Output-Parameter | type      | BESCHREIBUNG                                         |
|------------------|-----------|-----------------------------------------------------|
| Timestamp        | Datetime  | Zeitstempel des Fehlers.                           |
| OperationId      | String    | Vorgangs-ID des Fehlers.                    |
| Name             | String    | Fortlaufender Export Name.                             |
| Lastsuccess Run   | Timestamp | Die letzte erfolgreiche Ausführung des fortlaufenden Exports.   |
| Failurekind      | String    | Fehler/partialfailure. Partialfailure gibt an, dass einige Artefakte erfolgreich exportiert wurden, bevor der Fehler aufgetreten ist. |
| Details          | String    | Fehlerdetails.                              |

## <a name="example"></a>Beispiel 

```kusto
.show continuous-export MyExport failures 
```

| Timestamp                   | OperationId                          | Name     | Lastsuccess Run              | Failurekind | Details    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07:41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | Myexport | 2019-01-01 11:06:35.6308140 | Fehler     | Details... |
