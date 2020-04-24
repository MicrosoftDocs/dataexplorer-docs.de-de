---
title: Nicht unterstützte skalare Datentypen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt nicht unterstützte skalare Datentypen in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 76a548abdf05cec57e4d0558e5d98ab0f7cbdc3e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509587"
---
# <a name="unsupported-scalar-data-types"></a>Nicht unterstützte skalare Datentypen

Alle nicht dokumentierten **skalaren Datentypen** werden in Kusto als nicht unterstützt betrachtet.

Zu den nicht unterstützten Typen gehören die folgenden. Einige Typen wurden zuvor unterstützt:

| type       | Weitere(r) Name(n)   | Entsprechender .NET-Typ              | Speichertyp (interner Name)|
| ---------- | -------------------- | --------------------------------- | ----------------------------|
| `float`    |                      | `System.Single`                   | `R32`                       |
| `int16`    |                      | `System.Int16`                    | `I16`                       |
| `uint16`   |                      | `System.UInt16`                   | `UI16`                      |
| `uint32`   | `uint`               | `System.UInt32`                   | `UI32`                      |
| `uint64`   | `ulong`              | `System.UInt64`                   | `UI64`                      |
| `uint8`    | `byte`               | `System.Byte`                     | `UI8`                       |