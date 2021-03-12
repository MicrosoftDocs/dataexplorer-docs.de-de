---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 12/22/2020
ms.author: orspodek
ms.openlocfilehash: 52a6de4c8ecb3ccbe4e315145b8ae8a3c314ebfc
ms.sourcegitcommit: 600c3430c00eb62d52540fe08dab386a860193cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102472201"
---
> [!NOTE]
> * Systemeigenschaften werden für `json` und Tabellenformate (`csv`, `tsv` usw.), aber nicht für komprimierte Daten unterstützt. Bei Verwendung eines nicht unterstützten Formats werden die Daten weiterhin erfasst, die Eigenschaften werden jedoch ignoriert.
> * Bei Tabellendaten werden Systemeigenschaften nur für Ereignismeldungen mit einem Datensatz unterstützt.
> * Bei JSON-Daten werden Systemeigenschaften auch für Ereignismeldungen mit mehreren Datensätzen unterstützt. In solchen Fällen werden die Systemeigenschaften nur dem ersten Datensatz der Ereignismeldung hinzugefügt. 
> * Für die `csv`-Zuordnung werden Eigenschaften in der in der Tabelle [Systemeigenschaften](../ingest-data-event-hub-overview.md#system-properties) aufgeführten Reihenfolge am Anfang des Datensatzes hinzugefügt.
> * Für die `json`-Zuordnung werden Eigenschaften entsprechend den Eigenschaftsnamen in der Tabelle [Systemeigenschaften](../ingest-data-event-hub-overview.md#system-properties) hinzugefügt.
