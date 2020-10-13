---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe einer Azure Resource Manager-Vorlage
description: Erfahren Sie, wie sie einen Azure Data Explorer-Cluster und eine Datenbank mithilfe einer Azure Resource Manager-Vorlage erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/26/2019
ms.openlocfilehash: 46ccf006c6f2cf167953c64bcaa2f3de0fbaf4b6
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942215"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe einer Azure Resource Manager-Vorlage

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-Vorlage](create-cluster-database-resource-manager.md)

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. 

In diesem Artikel erstellen Sie einen Azure Data Explorer-Cluster und eine Datenbank mithilfe einer [Azure Resource Manager-Vorlage](/azure/azure-resource-manager/management/overview). Dieser Artikel zeigt Ihnen, wie Sie definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen. Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](/azure/azure-resource-manager/resource-group-authoring-templates). Informationen zur JSON-Syntax und zu den Eigenschaften, die in einer Vorlage verwendet werden sollten, finden Sie unter [Microsoft.Kusto-Ressourcentypen](/azure/templates/microsoft.kusto/allversions).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Azure Resource Manager-Vorlage für die Cluster- und Datenbankerstellung

In diesem Artikel verwenden Sie eine [vorhandene Schnellstartvorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Weitere Beispiele für Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Bereitstellen der Vorlage und Überprüfen der Vorlagenbereitstellung

Sie können die Azure Resource Manager-Vorlage über das [Azure-Portal](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) oder mithilfe von [PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment) bereitstellen.

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Verwenden des Azure-Portals zum Bereitstellen der Vorlage und Überprüfen der Vorlagenbereitstellung

1. Verwenden Sie zum Erstellen eines Clusters und einer Datenbank die folgende Schaltfläche, um die Bereitstellung zu starten. Klicken Sie mit der rechten Maustaste, und wählen Sie **In neuem Fenster öffnen**, damit Sie die restlichen Schritte in diesem Artikel ausführen können.

    [![Screenshot: Blaue Schaltfläche mit abgebildeten Wolken und der Bezeichnung „In Azure bereitstellen“](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Über die Schaltfläche **In Azure bereitstellen** gelangen Sie zum Azure-Portal, um ein Bereitstellungsformular auszufüllen.

    :::image type="content" source="media/create-cluster-database-resource-manager/deploy-2-azure.png" alt-text="Screenshot: Vorlage im Azure-Portal. Alle bei der Bearbeitung verwendeten Schaltflächen, Felder und Kontrollkästchen sind hervorgehoben." border="false":::

    Sie können [die Vorlage im Azure-Portal mithilfe des Formulars bearbeiten und bereitstellen](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template).

1. Geben Sie die erforderlichen Informationen in den Abschnitten **GRUNDLAGEN** und **EINSTELLUNGEN** ein. Wählen Sie eindeutige Cluster- und Datenbanknamen aus.
Die Erstellung eines Azure Data Explorer-Clusters und einer Datenbank kann einige Minuten dauern.

1. Um die Bereitstellung zu überprüfen, öffnen Sie die Ressourcengruppe im [Azure-Portal](https://portal.azure.com) und suchen den neuen Cluster und die Datenbank. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Verwenden von PowerShell zum Bereitstellen der Vorlage und Überprüfen der Vorlagenbereitstellung

#### <a name="deploy-the-template-using-powershell"></a>Bereitstellen der Vorlage mithilfe von PowerShell

1. Wählen Sie im folgenden Codeblock **Ausprobieren** aus, und folgen Sie dann den Anweisungen, um sich bei Azure Cloud Shell anzumelden.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Wählen Sie **Kopieren**, um das PowerShell-Skript zu kopieren.
1. Klicken Sie mit der rechten Maustaste auf die Shellkonsole, und wählen Sie **Einfügen** aus.
Die Erstellung eines Azure Data Explorer-Clusters und einer Datenbank kann einige Minuten dauern.

#### <a name="verify-the-deployment-using-powershell"></a>Überprüfen der Bereitstellung mithilfe von PowerShell

Verwenden Sie das folgende Azure PowerShell-Skript, um die Bereitstellung zu überprüfen.  Wenn die Cloud Shell noch geöffnet ist, muss die erste Zeile (Read-Host) nicht kopiert/ausgeführt werden. Weitere Informationen zur Verwaltung von Azure Data Explorer-Ressourcen in PowerShell finden Sie unter [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

[Erfassen von Daten im Azure Data Explorer-Cluster und in der Datenbank](ingest-data-overview.md)
