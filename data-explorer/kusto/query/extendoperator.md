---
title: Erweiterungsoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Extend-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b9b7bdb9488b0e9d1b72b3e0ab4782020c9b841
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515588"
---
# <a name="extend-operator"></a>extend-Operator

Erstellen Sie berechnete Spalten, und fügen Sie sie an das Resultset an.

```kusto
T | extend duration = endTime - startTime
```

**Syntax**

*T* `| extend` [*Spaltenname* | `(``,` *Spaltenname*[ ...] ] Ausdruck`,` [ ...] *Expression* `)` `=`

**Argumente**

* *T*: Das Eingabe-Tabellarische Resultset.
* *Spaltenname:* Optional. Der Name der spalte, die hinzugefügt oder aktualisiert werden soll. Wenn nicht angegeben, wird der Name generiert. Wenn *Ausdruck* mehr als eine Spalte zurückgibt, kann eine Liste von Spaltennamen in Klammern angegeben werden. In diesem Fall erhalten die Ausgabespalten von *Expression*die angegebenen Namen, und die restlichen Ausgabespalten werden, falls vorhanden, fallen gelassen. Wenn keine Liste der Spaltennamen angegeben ist, werden *alle*Expression-Ausgabespalten mit generierten Namen zur Ausgabe hinzugefügt.
* *Ausdruck:* Eine Berechnung über die Spalten der Eingabe.

**Rückgabe**

Eine Kopie des Eingabe-Tabellarischen Resultsets, so dass:
1. Spaltennamen, `extend` die bereits in der Eingabe vorhanden sind, werden entfernt und als neue berechnete Werte angehängt.
2. Spaltennamen, `extend` die in der Eingabe nicht vorhanden sind, werden als neue berechnete Werte angehängt.

**Tipps**

* Der `extend` Operator fügt dem Eingabeergebnissatz eine neue Spalte hinzu, die **keinen** Index hat. Wenn die neue Spalte in den meisten Fällen genau mit einer vorhandenen Tabellenspalte mit einem Index identisch ist, kann Kusto den vorhandenen Index automatisch verwenden. In einigen komplexen Szenarien wird diese Weitergabe jedoch nicht durchgeführt. Wenn das Ziel in solchen Fällen darin besteht, eine Spalte umzubenennen, verwenden Sie stattdessen den [ `project-rename` Operator.](projectrenameoperator.md)

**Beispiel**

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

Sie können die [series_stats-Funktion](series-statsfunction.md) verwenden, um mehrere Spalten zurückzugeben.