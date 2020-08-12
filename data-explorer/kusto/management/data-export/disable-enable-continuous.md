---
title: 'Aktivieren oder Deaktivieren des fortlaufenden Datenexports: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie den fortlaufenden Datenexport in Azure Daten-Explorer deaktivieren oder aktivieren.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b69db144474557ab8d5a8e19a7bce9bbbd5df183
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149501"
---
# <a name="disable-or-enable-continuous-export"></a>Deaktivieren oder Aktivieren von fortlaufendem Export

Deaktiviert oder aktiviert den fortlaufenden Exportauftrag. Ein deaktivierter fortlaufender Export wird nicht ausgeführt, aber der aktuelle Zustand wird persistent gespeichert und kann fortgesetzt werden, wenn der fortlaufende Export aktiviert ist. 

Wenn Sie einen fortlaufenden Export aktivieren, der für einen längeren Zeitraum deaktiviert wurde, wird der Export Vorgang fortgesetzt, wo er zuletzt beendet wurde, als der Export Vorgang deaktiviert wurde. Diese Fortsetzung führt möglicherweise zu einem Export mit langer Laufzeit, der das Ausführen anderer Exporte blockiert, wenn nicht genügend Cluster Kapazität vorhanden ist, um alle Prozesse zu verarbeiten. Fortlaufende Exporte werden von der letzten Laufzeit in aufsteigender Reihenfolge ausgeführt (der älteste Export wird zuerst ausgeführt, bis der catch-Vorgang abgeschlossen ist). 

## <a name="syntax"></a>Syntax

`.enable` `continuous-export` *ContinuousExportName* 

`.disable` `continuous-export` *ContinuousExportName* 

## <a name="properties"></a>Eigenschaften

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Name des fortlaufenden Exports |

## <a name="output"></a>Output

Das Ergebnis des [Befehls zum Anzeigen des fortlaufenden Exports](show-continuous-export.md) des geänderten fortlaufenden Exports. 
