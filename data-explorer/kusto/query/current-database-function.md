---
title: current_database ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird current_database () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d68c35547c840cc1e16224c376e90dfabec296d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348699"
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