---
title: 'extend-Operator: Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der extend-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7ead6313128b99357dd61f18f55c5d5543943a05
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513351"
---
# <a name="extend-operator"></a>extend-Operator

Erstellt berechnete Spalten und fügt diese an das Resultset an.

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>Syntax

*T* `| extend` [*ColumnName* | `(`*ColumnName*[`,` ...]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>Argumente

* *T*: Das tabellarische Eingaberesultset.
* *ColumnName:* Optional. Der Name der hinzuzufügenden oder zu aktualisierenden Spalte. Wenn keine Angabe erfolgt, wird der Name generiert. Wenn *Expression* mehrere Spalten zurückgibt, kann eine Liste mit Spaltennamen in Klammern angegeben werden. In diesem Fall erhalten die Ausgabespalten von *Expression* die angegebenen Namen, wobei die restlichen Ausgabespalten gelöscht werden, sofern vorhanden. Wenn keine Liste der Spaltennamen angegeben wird, werden alle Ausgabespalten von *Expression* mit generierten Namen der Ausgabe hinzugefügt.
* *Ausdruck:* Eine Berechnung der Spalten der Eingabe.

## <a name="returns"></a>Gibt zurück

Eine Kopie des tabellarischen Eingaberesultsets, für die Folgendes gilt:
1. Mit `extend` notierte Spaltennamen, die bereits in der Eingabe vorhanden sind, werden entfernt und als ihre neu berechneten Werte angefügt.
2. Mit `extend` notierte Spaltennamen, die nicht in der Eingabe vorhanden sind, werden als ihre neu berechneten Werte angefügt.

**Tipps**

* Der `extend`-Operator fügt dem Eingaberesultset eine neue Spalte hinzu, die **nicht** über einen Index verfügt. Wenn die neue Spalte genau so wie eine vorhandene Tabellenspalte festgelegt ist, die über einen Index verfügt, kann Kusto in den meisten Fällen automatisch den vorhandenen Index verwenden. In einigen komplexen Szenarien erfolgt diese Weitergabe jedoch nicht. Wenn das Ziel darin besteht, eine Spalte umzubenennen, verwenden Sie in solchen Fällen stattdessen den [`project-rename`-Operator](projectrenameoperator.md).

## <a name="example"></a>Beispiel

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

Sie können die [series_stats](series-statsfunction.md)--Funktion verwenden, um mehrere Spalten zurückzugeben.