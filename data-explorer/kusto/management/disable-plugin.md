---
title: 'Deaktivieren von Plug-in-Befehlen: Azure Daten-Explorer'
description: In diesem Artikel wird das Plug-in Management Command. Deaktivieren des Plug-ins in Azure Daten-Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1dd2649d4746506f0ef2c08d4615260babd594e1
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422074"
---
# <a name="disable-plugin"></a>Deaktivieren des Plug-ins

Deaktiviert ein Plug-in.

Dieser Befehl erfordert eine- `All Databases admin` Berechtigung.

## <a name="syntax"></a>Syntax

`.disable``plugin` *PluginName*

## <a name="example"></a>Beispiel
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>Nächste Schritte

* [Plug-ins anzeigen](show-plugins.md)
* [. Enable Plug-in](enable-plugin.md)

