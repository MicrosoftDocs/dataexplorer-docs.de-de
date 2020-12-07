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
ms.openlocfilehash: 1063355f880f26a321eb6416180ae9764575f0be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320892"
---
# <a name="disable-plugin"></a>.disable plugin

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

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)

