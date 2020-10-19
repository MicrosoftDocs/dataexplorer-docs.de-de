---
title: 'Refactoring von Kusto-Explorer-Code: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird das Refactoring von Code im Kusto-Explorer in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 2e73e10bd62f9767bfe578eadc747372cd604362
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92175666"
---
# <a name="kusto-explorer-code-refactoring"></a>Refactoring von Kusto-Explorer-Code

Ähnlich wie bei anderen IDEs bietet Kusto. Explorer mehrere Features für das Bearbeiten und Refactoring von kql-Abfragen.

## <a name="rename-variable-or-column-name"></a>Variablen-oder Spaltennamen umbenennen

`Ctrl` + `R` `Ctrl` + `R` Wenn Sie im Abfrage-Editor-Fenster auf klicken, können Sie das derzeit ausgewählte Symbol umbenennen.

Sehen Sie sich die folgende Momentaufnahme an, die die-

![Animiertes GIF, das eine Variable anzeigt, die im Abfrage-Editor-Fenster umbenannt wird. Drei vorkommen werden gleichzeitig durch den neuen Namen ersetzt.](./Images/kusto-explorer-refactor/ke-refactor-rename.gif "umbenennen-umbenennen")

## <a name="extract-scalars-as-let-expressions"></a>Extrahieren von skalaren als `let` Ausdrücke

Sie können das aktuell ausgewählte Literale als Ausdruck herauf Stufen, `let` indem Sie auf klicken `Alt` + `Ctrl` + `M` 

![Animiertes GIF. Der Abfrage-Editor-Zeiger beginnt bei einem literalen Ausdruck. Daraufhin wird eine Let-Anweisung angezeigt, die diesen Literalwert auf eine neue Variable festlegt.](./Images/kusto-explorer-refactor/ke-extract-as-let-literal.gif "Extract-as-Let-Literale")

## <a name="extract-tabular-statements-as-let-expressions"></a>Extrahieren von tabellarischen Anweisungen als `let` Ausdrücke

Sie können auch tabellarische Ausdrücke als Anweisungen herauf Stufen, `let` indem Sie den zugehörigen Text auswählen und dann auf klicken `Alt` + `Ctrl` + `M` . 

![Animiertes GIF. Im Abfrage-Editor ist ein tabellarischer Ausdruck ausgewählt. Daraufhin wird eine Let-Anweisung angezeigt, in der der tabellarische Ausdruck auf eine neue Variable festgelegt wird.](./Images/kusto-explorer-refactor/ke-extract-as-let-tabular.gif "Extract-as-Let-tabellarisch")
