---
title: Beispiel-getrennter Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Beispieloperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6d6c77aef3a7e2c6d99af792062d9f1a6215f51
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663636"
---
# <a name="sample-distinct-operator"></a>sample-distinct-Operator

Gibt eine einzelne Spalte zurück, die maximal die angegebene Anzahl von unterschiedlichen Werten für die angeforderte Spalte enthält. 

Die Standardgeschmacksart (und derzeit nur) des Operators versucht, so schnell wie möglich eine Antwort zurückzugeben (anstatt zu versuchen, eine faire Probe zu erstellen)

```kusto
T | sample-distinct 5 of DeviceId
```

**Syntax**

*T* `| sample-distinct` *NumberOfValues* `of` *ColumnName*

**Argumente**
* *NumberOfValues*: Die Anzahl der zu gebenden Werte von *T.* Sie können einen beliebigen numerischen Ausdruck angeben.

**Tipps**

 Kann praktisch sein, um `sample-distinct` eine Grundgesamtheit auszuprobieren, `in` indem eine let-Anweisung und ein neuerer Filter mithilfe des Operators (siehe Beispiel) 

 Wenn Sie die Top-Werte und nicht nur eine Stichprobe wünschen, können Sie den [Top-Hitters-Operator](tophittersoperator.md) 

 Wenn Sie Datenzeilen (anstelle von Werten einer bestimmten Spalte) ablesen möchten, verweisen Sie auf den [Beispieloperator](sampleoperator.md)

**Beispiele**  

Erhalten Sie 10 unterschiedliche Werte von einer Bevölkerung

```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

Sie erfassen eine Beispielpopulation und führen weitere Berechnungen durch und wissen dabei, dass die Zusammenfassung die Abfragegrenzwerte nicht überschreitet. 

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```