---
title: Der Dezimaldatentyp - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Dezimaldatentyp in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62fd745c804ad4a50652e09cd722c17a4a61daac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509893"
---
# <a name="the-decimal-data-type"></a>Der Dezimaldatentyp

Der `decimal` Datentyp stellt eine 128 Bit breite Dezimalzahl dar.

Literale des `decimal` Datentyps haben die gleiche Darstellung wie . NET es `System.Data.SqlTypes.SqlDecimal`.

`decimal(1.0)`, `decimal(0.1)`, `decimal(1e5)` und sind alle `decimal`Literale vom Typ .

Es gibt mehrere spezielle Literalformen:
* `decimal(null)`: Der ist der [NULL-Wert](null-values.md).