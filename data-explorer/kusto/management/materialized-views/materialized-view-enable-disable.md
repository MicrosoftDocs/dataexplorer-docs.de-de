---
title: 'Aktivieren und Deaktivieren von Befehlen der materialisierten Ansicht: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie materialisierte Ansichts Befehle in Azure Daten-Explorer aktiviert oder deaktiviert werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: bb1fab3f211de4b33ca0dd2cee6a8cfa0cc796a9
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452679"
---
# <a name="disable--enable-materialized-view"></a>.disable | .enable materialized-view

Eine materialisierte Sicht kann auf eine der folgenden Arten deaktiviert werden:

* **Automatisches deaktivieren durch das System:**  Die materialisierte Sicht wird automatisch deaktiviert, wenn die Materialisierung mit einem permanenten Fehler fehlschlägt. Dieser Vorgang kann in den folgenden Instanzen erfolgen: 
    * Schema Änderungen, die mit der Sicht Definition inkonsistent sind.  
    * Änderungen an der Quell Tabelle, die dazu führen, dass die materialisierte Sicht Abfrage semantisch ungültig ist. 
* **Explizites Deaktivieren der materialisierten Ansicht:**  Wenn sich die materialisierte Ansicht negativ auf die Integrität des Clusters auswirkt (z. b., wenn zu viel CPU beansprucht wird), deaktivieren Sie die Sicht mithilfe des folgenden [Befehls](#syntax) .

> [!NOTE]
> * Wenn eine materialisierte Sicht deaktiviert ist, wird die Materialisierung angehalten und nutzt keine Ressourcen aus dem Cluster. Das Abfragen der materialisierten Sicht ist auch dann möglich, wenn Sie deaktiviert ist, aber die Leistung kann gering sein. Die Leistung für eine deaktivierte materialisierte Sicht hängt von der Anzahl der Datensätze ab, die seit der Deaktivierung in der Quell Tabelle erfasst wurden. 
> * Sie können eine materialisierte Sicht aktivieren, die zuvor deaktiviert wurde. Wenn die materialisierte Sicht erneut aktiviert ist, wird Sie von dem Zeitpunkt, an dem Sie ausgelassen wurde, fortgesetzt, und es werden keine Datensätze ausgelassen. Wenn die Ansicht für einen längeren Zeitraum deaktiviert wurde, kann es sehr lange dauern, bis die Ansicht wiederholt wird.

Die Deaktivierung einer Ansicht wird nur empfohlen, wenn Sie vermuten, dass die Ansicht die Integrität Ihres Clusters beeinträchtigt.

## <a name="syntax"></a>Syntax

`.enable` | `disable``materialized-view` *Materializedviewname*

## <a name="properties"></a>Eigenschaften

|Eigenschaft|type|BESCHREIBUNG
|----------------|-------|---|
|Materializedviewname|String|Der Name der materialisierten Sicht.|

## <a name="example"></a>Beispiel

```kusto
.enable materialized-view ViewName

.disable materialized-view ViewName
```