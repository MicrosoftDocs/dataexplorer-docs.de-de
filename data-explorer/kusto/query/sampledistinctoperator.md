---
title: 'Beispiel: unterschiedlicher Operator: Azure Daten-Explorer'
description: In diesem Artikel wird ein Beispiel für einen unterschiedlichen Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd8385a5dc8f959e1fe195bfe333a6868f55cb4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242734"
---
# <a name="sample-distinct-operator"></a>sample-distinct-Operator

Gibt eine einzelne Spalte zurück, die maximal die angegebene Anzahl von unterschiedlichen Werten für die angeforderte Spalte enthält. 

der standardmäßige (und derzeit einzige) Wert des Operators versucht, eine Antwort so schnell wie möglich zurückzugeben (anstatt eine faire Stichprobe zu erstellen).

```kusto
T | sample-distinct 5 of DeviceId
```

## <a name="syntax"></a>Syntax

*T* - `| sample-distinct` *Werte* Spalten `of` *Name*

## <a name="arguments"></a>Argumente
* *Anzahlwerte*: die Anzahl der unterschiedlichen Werte von *T* , die zurückgegeben werden sollen. Sie können einen beliebigen numerischen Ausdruck angeben.

**Tipps**

 Kann nützlich sein, um ein Beispiel für eine Population durch Einfügen `sample-distinct` in eine Let-Anweisung zu erhalten, und später mithilfe des `in` Operators Filtern (siehe Beispiel). 

 Wenn Sie die obersten Werte anstelle eines Beispiels verwenden möchten, können Sie den [Top-Hitters](tophittersoperator.md) -Operator verwenden. 

 Wenn Sie Stichproben von Daten Zeilen (anstelle von Werten einer bestimmten Spalte) durchführen möchten, lesen Sie den [Beispiel Operator](sampleoperator.md) .

## <a name="examples"></a>Beispiele  

10 unterschiedliche Werte aus einer Population

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

Sie erfassen eine Beispielpopulation und führen weitere Berechnungen durch und wissen dabei, dass die Zusammenfassung die Abfragegrenzwerte nicht überschreitet. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```
