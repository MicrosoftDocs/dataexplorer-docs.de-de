---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 12/22/2020
ms.author: orspodek
ms.openlocfilehash: bd12845234ec57c1393935b7366b8d2e387b3890
ms.sourcegitcommit: d19b4214625eeb1ec7aec4fd6c92007a07c76ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/22/2020
ms.locfileid: "99554825"
---
> [!NOTE]
> * Systemeigenschaften werden nicht für komprimierte Daten unterstützt.
> * Bei Tabellendaten werden Systemeigenschaften nur für Ereignismeldungen mit einem Datensatz unterstützt.
> * Bei JSON-Daten werden Systemeigenschaften auch für Ereignismeldungen mit mehreren Datensätzen unterstützt. In solchen Fällen werden die Systemeigenschaften nur dem ersten Datensatz der Ereignismeldung hinzugefügt. 
> * Für die `csv`-Zuordnung werden Eigenschaften in der in der Tabelle [Systemeigenschaften](../ingest-data-event-hub-overview.md#system-properties) aufgeführten Reihenfolge am Anfang des Datensatzes hinzugefügt.
> * Für die `json`-Zuordnung werden Eigenschaften entsprechend den Eigenschaftsnamen in der Tabelle [Systemeigenschaften](../ingest-data-event-hub-overview.md#system-properties) hinzugefügt.
