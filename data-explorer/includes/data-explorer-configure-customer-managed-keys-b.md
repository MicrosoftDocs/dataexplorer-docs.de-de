---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 6e9c489850d77155ca4832275883c7749edd6284
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "98571759"
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

Konfigurieren Sie als Nächstes die Zugriffsrichtlinie für die Key Vault-Instanz, sodass der Cluster die Berechtigung für den Zugriff darauf erhält. In diesem Schritt verwenden Sie entweder die systemseitig zugewiesene oder die benutzerseitig zugewiesene verwaltete Identität, die Sie zuvor dem Cluster zugewiesen haben. Rufen Sie zum Festlegen der Zugriffsrichtlinie für den Schlüsseltresor [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) auf. Ersetzen Sie die Platzhalterwerte in Klammern durch Ihre eigenen Werte, und verwenden Sie die in den vorherigen Beispielen definierten Variablen.

Verwenden Sie für die systemseitig zugewiesene Identität die Prinzipal-ID (principalId) des Clusters:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Verwenden Sie für die benutzerseitig zugewiesene Identität die Prinzipal-ID (principalId) der Identität:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $userIdentity.Properties.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Erstellen eines neuen Schlüssels

Als Nächstes erstellen Sie einen Schlüssel im Schlüsseltresor. Rufen Sie zum Erstellen eines neuen Schlüssels [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) auf. Ersetzen Sie die Platzhalterwerte in Klammern durch Ihre eigenen Werte, und verwenden Sie die in den vorherigen Beispielen definierten Variablen.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
