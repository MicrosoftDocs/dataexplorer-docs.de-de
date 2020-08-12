---
title: 'Fortlaufenden Datenexport anzeigen: Azure-Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie Eigenschaften für den kontinuierlichen Datenexport in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: db7583c055468808ade1166c0bfee90859399d32
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149426"
---
# <a name="show-continuous-export"></a>Fortlaufenden Export anzeigen

Gibt die Eigenschaften des kontinuierlichen Exports von *continuousexportname*zurück. 

## <a name="syntax"></a>Syntax

`.show` `continuous-export` *ContinuousExportName*

## <a name="properties"></a>Eigenschaften

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Name des fortlaufenden Exports. |

`.show` `continuous-exports`

Gibt alle kontinuierlichen Exporte in der Datenbank zurück. 

## <a name="output"></a>Output

| Output-Parameter    | type     | BESCHREIBUNG                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| Cursor scopedtables  | String   | Liste der explizit Bereichs bezogenen Tabellen (Fakten Tabellen) (JSON-serialisiert)               |
| Export Properties    | String   | Export Eigenschaften (JSON-Serialisierung)                                     |
| Exportedto          | Datetime | Der letzte Zeitpunkt, zu dem die Daten erfolgreich exportiert wurden.       |
| Externaltablename   | String   | Name der externen Tabelle                                              |
| Forcedlatency       | TimeSpan | Erzwungene Latenz (null, wenn nicht angegeben)                                   |
| Intervalbetweaufausführungen | TimeSpan | Intervall zwischen Ausführungen                                                   |
| IsDisabled          | Boolean  | True, wenn der fortlaufende Export deaktiviert ist.                               |
| IsRunning           | Boolean  | True, wenn der fortlaufende Export gerade ausgeführt wird.                      |
| Lastrauunresult       | String   | Die Ergebnisse der letzten fortlaufenden Export Laufzeit ( `Completed` oder `Failed` ) |
| Lastrauuntime         | Datetime | Der Zeitpunkt der letzten Ausführung des fortlaufenden Exports (Startzeit)           |
| Name                | String   | Name des fortlaufenden Exports                                           |
| Abfrage               | String   | Exportieren einer Abfrage                                                            |
| Startcursor         | String   | Startpunkt der ersten Ausführung dieses fortlaufenden Exports         |

