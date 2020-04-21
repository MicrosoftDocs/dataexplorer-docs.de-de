---
title: Operator für die Anzahl - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Zähloperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: c0d0286919a68b6e58065e0a6fe7e0d24b1cdd5f
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610222"
---
# <a name="count-operator"></a>count-Operator

Gibt die Anzahl der Datensätze im Eingabedatensatzsatz zurück.

**Syntax**

`T | count`

**Argumente**

*T:* Die tabellarischen Daten, deren Datensätze gezählt werden.

**Rückgabe**

Diese Funktion gibt eine Tabelle mit einem einzelnen Datensatz und einer Spalte vom Typ `long`zurück. Der Wert der einzigen Zelle ist die Anzahl von Datensätzen in *T*. 

**Beispiel**

```kusto
StormEvents | count
```
