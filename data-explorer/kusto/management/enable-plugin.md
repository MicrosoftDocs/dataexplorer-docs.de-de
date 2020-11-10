---
title: 'Plug-in-Befehl aktivieren: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Plug-in-Verwaltungs Befehl. Aktivieren Sie das Plug-in in Daten-Explorer Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: a44ebec6774374f4d38dfda3babe42f2f5e07ac6
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422071"
---
# <a name="enable-plugin"></a>. Enable Plug-in

Aktiviert ein Plug-in.

Dieser Befehl erfordert eine- `All Databases admin` Berechtigung.

## <a name="syntax"></a>Syntax

`.enable``plugin` *PluginName*

## <a name="example"></a>Beispiel

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>Nächste Schritte

* [Deaktivieren des Plug-ins](disable-plugin.md)
* [Plug-ins anzeigen](show-plugins.md)

