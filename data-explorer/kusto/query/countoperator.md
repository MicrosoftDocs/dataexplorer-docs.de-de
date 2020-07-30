---
title: 'count-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den count-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 969efc142f1cd823b319a5c98494542fb2603f24
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348733"
---
# <a name="count-operator"></a>count-Operator

Gibt die Anzahl der Datensätze in der Eingabedaten Satz Gruppe zurück.

## <a name="syntax"></a>Syntax

`T | count`

## <a name="arguments"></a>Argumente

*T:* Die tabellarischen Daten, deren Datensätze gezählt werden.

## <a name="returns"></a>Rückgabe

Diese Funktion gibt eine Tabelle mit einem einzelnen Datensatz und einer Spalte vom Typ `long`zurück. Der Wert der einzigen Zelle ist die Anzahl von Datensätzen in *T*. 

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
