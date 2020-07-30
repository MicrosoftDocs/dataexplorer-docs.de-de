---
title: ago ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt vor () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f3d58a7b29731125407eec7429967d1f90f5b7e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349770"
---
# <a name="ago"></a>ago()

Subtrahiert den angegebenen Zeitraum von der aktuellen UTC-Uhrzeit.

```kusto
ago(1h)
ago(1d)
```

Wie `now()`kann diese Funktion mehrmals in einer Anweisung verwendet werden, und die UTC-Uhrzeit, auf die verwiesen wird, ist für alle Instanziierungen identisch.

## <a name="syntax"></a>Syntax

`ago(`*a_timespan*`)`

## <a name="arguments"></a>Argumente

* *a_timespan:* Intervall, das von der aktuellen UTC-Uhrzeit (`now()`) subtrahiert werden soll.

## <a name="returns"></a>Rückgabe

`now() - a_timespan`

## <a name="example"></a>Beispiel

Alle Zeilen mit einem Zeitstempel der letzten Stunde:

```kusto
T | where Timestamp > ago(1h)
```