---
title: 'Löschen von kontinuierlichen Daten Exporten: Azure-Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie den fortlaufenden Datenexport in Azure Daten-Explorer löschen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: f08d8c99c242aa63e3847d3965bf9511967b60e1
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149405"
---
# <a name="drop-continuous-export"></a>Fortlaufenden Export löschen

Löscht einen fortlaufenden Exportauftrag.

## <a name="syntax"></a>Syntax

`.drop` `continuous-export` *ContinuousExportName*

## <a name="properties"></a>Eigenschaften

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Name des fortlaufenden Exports |

## <a name="output"></a>Output

Die verbleibenden kontinuierlichen Exporte in der Datenbank (nach dem Löschen). Ausgabe Schema wie im [Befehl Befehl zum fortlaufenden Export anzeigen](show-continuous-export.md).
