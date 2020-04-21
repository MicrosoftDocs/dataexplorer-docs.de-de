---
title: Kusto Explorer-Codeumgestaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Kusto Explorer-Codeumgestaltung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 0a89cf9c648fc4811d56c22012cdb25d5505eb4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523986"
---
# <a name="kusto-explorer-code-refactoring"></a>Kusto Explorer Code-Umgestaltung

Ähnlich wie andere IDEs bietet Kusto.Explorer mehrere Funktionen für die Bearbeitung und Umgestaltung von KQL-Abfragen.

## <a name="rename-variable-or-column-name"></a>Variablen oder Spaltennamen umbenennen

+ `R` `Ctrl` + `R` Wenn Sie im Fenster Abfrage-Editor auf klicken, können Sie das aktuell ausgewählte Symbol umbenennen. `Ctrl`

Siehe unten Snapshot, der die Erfahrung demonstriert:

![alt text](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "Umbenennung")

## <a name="extract-scalars-as-let-expressions"></a>Extrahieren von Skalarn als `let` Ausdrücke

Sie können das aktuell `let` ausgewählte Literal `Alt` + `Ctrl` + `M`als Ausdruck heraufstufen, indem Sie auf klicken. 

![alt text](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "extract-as-let-literal")

## <a name="extract-tabular-statements-as-let-expressions"></a>Extrahieren von tabellarischen Anweisungen als `let` Ausdrücke

Sie können tabellentabellarische `let` Ausdrücke auch als Anweisungen `Alt` + `Ctrl` +heraufstufen, indem Sie den Text auswählen und dann auf `M`klicken. 

![alt text](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "extract-as-let-tabellarisch")
