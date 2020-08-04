---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: orspodek
ms.openlocfilehash: d55077b5d1938caf6df49d34e68ece7e32c56f85
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350065"
---
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Bereinigen von Ressourcen über das Azure-Portal

Löschen Sie die Ressourcen im Azure-Portal, indem Sie die Schritte zum [Bereinigen von Ressourcen](../create-cluster-database-portal.md#clean-up-resources) ausführen.

### <a name="clean-up-resources-using-powershell"></a>Bereinigen von Ressourcen über PowerShell

Wenn die Cloud Shell noch geöffnet ist, muss die erste Zeile (Read-Host) nicht kopiert/ausgeführt werden.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```