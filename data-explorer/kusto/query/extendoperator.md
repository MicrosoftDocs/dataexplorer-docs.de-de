---
title: 'Erweitern des Operators: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Erweiterungs Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32100f6668c2fb20ae715b985b0bf3612e13e69b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348155"
---
# <a name="extend-operator"></a>extend-Operator

Erstellen Sie berechnete Spalten, und fügen Sie Sie an das Resultset an.

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>Syntax

*T* `| extend` [*ColumnName*  |  `(` *ColumnName*[ `,` ...] `)` `=` ] *Ausdruck* [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: das tabellarische Eingabe-Resultset.
* *ColumnName:* Optionale. Der Name der Spalte, die hinzugefügt oder aktualisiert werden soll. Wenn keine Angabe erfolgt, wird der Name generiert. Wenn *Expression* mehr als eine Spalte zurückgibt, kann eine Liste mit Spaltennamen in Klammern angegeben werden. In diesem Fall erhalten die Ausgabespalten des *Ausdrucks*die angegebenen Namen, wobei die restlichen Ausgabespalten gelöscht werden, sofern vorhanden. Wenn keine Liste der Spaltennamen angegeben wird, werden die Ausgabespalten aller *Ausdrücke*mit generierten Namen der Ausgabe hinzugefügt.
* *Ausdruck:* Eine Berechnung der Spalten der Eingabe.

## <a name="returns"></a>Rückgabe

Eine Kopie des tabellarischen Eingabe-Resultsets, z. b.:
1. Die von notierten Spaltennamen `extend` , die bereits in der Eingabe vorhanden sind, werden entfernt und als neue berechnete Werte angehängt.
2. Von notierte Spaltennamen `extend` , die in der Eingabe nicht vorhanden sind, werden als neue berechnete Werte angefügt.

**Tipps**

* Der- `extend` Operator Fügt dem eingaberesultset eine neue Spalte hinzu, die **keinen** Index hat. Wenn die neue Spalte in den meisten Fällen genauso festgelegt ist, wie eine vorhandene Tabellenspalte, die über einen Index verfügt, kann der vorhandene Index von Kusto automatisch verwendet werden. In einigen komplexen Szenarios erfolgt diese Weitergabe jedoch nicht. Wenn das Ziel darin besteht, eine Spalte umzubenennen, verwenden Sie stattdessen den- [ `project-rename` Operator](projectrenameoperator.md) .

## <a name="example"></a>Beispiel

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

Sie können die [series_stats](series-statsfunction.md) -Funktion verwenden, um mehrere Spalten zurückzugeben.