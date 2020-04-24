---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: de8a37b1b27dc1bff377c6212c0bfea0a13c7de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492686"
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