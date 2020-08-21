---
title: include file
description: include file
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 08/12/2020
ms.author: orspodek
ms.reviewer: tzgitlin
ms.custom: include file
ms.openlocfilehash: f63288ad25363f1d32184e45efb21b69a894da0c
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201718"
---
|**Eigenschaft** | **Beschreibung der Eigenschaft**|
|---|---|
| `rawSizeBytes` | Größe der Rohdaten (unkomprimiert). Bei Avro/ORC/Parquet ist dies die Größe vor dem Anwenden der formatspezifischen Komprimierung. Geben Sie die ursprüngliche Datengröße an, indem Sie diese Eigenschaft auf die nicht komprimierte Datengröße in Byte festlegen.|
| `kustoTable` |  Name der vorhandenen Zieltabelle. Überschreibt die `Table`, die auf dem Blatt `Data Connection` festgelegt ist. |
| `kustoDataFormat` |  Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| `kustoIngestionMappingReference` |  Name der zu verwendenden vorhandenen Erfassungszuordnung. Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| `kustoIgnoreFirstRecord` | Wenn `true` festgelegt wird, ignoriert Kusto die erste Zeile im Blob. Verwenden Sie diese Eigenschaft in Daten in einem Tabellenformat (CSV, TSV oder ähnliche), um die Header zu ignorieren. |
| `kustoExtentTags` | Zeichenfolgendarstellung von [Tags](../kusto/management/extents-overview.md#extent-tagging), die an die resultierende Erweiterung angefügt werden. |
| `kustoCreationTime` |  Überschreibt [$IngestionTime](../kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) für das Blob und ist als ISO 8601-Zeichenfolge formatiert. Verwenden Sie dies für einen Abgleich. |
