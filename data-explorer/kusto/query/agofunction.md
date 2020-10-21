---
title: ago ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt vor () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4457f7e400922221e139011508e13d4d7e6ee551
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247050"
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