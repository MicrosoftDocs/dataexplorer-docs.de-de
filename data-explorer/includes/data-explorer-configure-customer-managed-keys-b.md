---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492842"
---
## <a name="create-a-new-key-vault"></a>Erstellen eines neuen Schlüsseltresors

Rufen Sie zum Erstellen eines neuen Schlüsseltresors mit PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) auf. In der Key Vault-Instanz, die Sie zum Speichern der kundenseitig verwalteten Schlüssel für die Azure Data Explorer-Verschlüsselung verwenden, müssen zwei Einstellungen zum Schutz der Schlüssel aktiviert sein: **Vorläufiges Löschen** und **Nicht bereinigen**. Ersetzen Sie im Beispiel unten die Platzhalterwerte in Klammern durch Ihre eigenen Werte.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurieren der Zugriffsrichtlinie für den Schlüsseltresor

Konfigurieren Sie als Nächstes die Zugriffsrichtlinie für die Key Vault-Instanz, sodass der Cluster die Berechtigung für den Zugriff darauf erhält. In diesem Schritt verwenden Sie die systemseitig zugewiesene verwaltete Identität, die Sie zuvor dem Cluster zugewiesen haben. Rufen Sie zum Festlegen der Zugriffsrichtlinie für den Schlüsseltresor [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) auf. Ersetzen Sie die Platzhalterwerte in Klammern durch Ihre eigenen Werte, und verwenden Sie die in den vorherigen Beispielen definierten Variablen.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Erstellen eines neuen Schlüssels

Als Nächstes erstellen Sie einen Schlüssel im Schlüsseltresor. Rufen Sie zum Erstellen eines neuen Schlüssels [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) auf. Ersetzen Sie die Platzhalterwerte in Klammern durch Ihre eigenen Werte, und verwenden Sie die in den vorherigen Beispielen definierten Variablen.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
