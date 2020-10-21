---
title: current_database ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird current_database () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b4e0458c78023d35e002910de4c5946260b43b4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252544"
---
# <a name="current_database"></a>current_database()

Gibt den Namen der Datenbank im Bereich zurück (Datenbank, für die alle Abfrage Entitäten aufgelöst werden, wenn keine andere Datenbank angegeben ist).

## <a name="syntax"></a>Syntax

`current_database()`

## <a name="returns"></a>Rückgabe

Der Name der Datenbank im Gültigkeitsbereich als Wert des Typs `string` .

## <a name="example"></a>Beispiel

```kusto
print strcat("Database in scope: ", current_database())
```