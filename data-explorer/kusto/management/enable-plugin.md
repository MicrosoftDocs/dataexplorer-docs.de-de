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
ms.openlocfilehash: a4da3848fa459cf5fae8a7a73f8b1f318ce7e858
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321470"
---
# <a name="enable-plugin"></a>.enable plugin

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

* [`.disable plugin`](disable-plugin.md)
* [`.show plugins`](show-plugins.md)

