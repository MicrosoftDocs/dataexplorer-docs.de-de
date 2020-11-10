---
title: 'Plug-in-Befehle anzeigen von Plug-ins: Daten-Explorer Azure'
description: Dieser Artikel beschreibt Plug-in-Verwaltungs Befehle in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1db761d8c290c7aaea452cd0cb3d85f2f648221c
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422079"
---
# <a name="show-plugins"></a>Plug-ins anzeigen


Listet alle Plug-ins des Clusters auf.

## <a name="syntax"></a>Syntax

`.show` `plugins`

## <a name="output"></a>Output

Gibt eine Tabelle zurück, die die folgenden Felder enthält:
* **PluginName** : Name des Plug-ins
* **Isaktiviert** : ein boolescher Wert, der angibt, ob das Plug-in aktiviert ist.

## <a name="example"></a>Beispiel

<!-- csl -->
```kusto
.show plugins
``` 

| PluginName | IsEnabled |
|---|---|
| autocluster | false |
| basket      | true  |

## <a name="next-steps"></a>Nächste Schritte

* [Deaktivieren des Plug-ins](disable-plugin.md)
* [. Enable Plug-in](enable-plugin.md)
