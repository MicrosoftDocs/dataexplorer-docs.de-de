---
title: Sichern Ihres Clusters in Azure Data Explorer mithilfe von Disk Encryption Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie Cluster mithilfe von Disk Encryption in Azure Data Explorer im Azure-Portal sichern.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 4f0ed42727759dae54f75fcb33ba1f024c218a4a
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515901"
---
# <a name="secure-your-cluster-using-disk-encryption-in-azure-data-explorer---azure-portal"></a>Sichern Ihres Clusters in Azure Data Explorer mithilfe von Disk Encryption Azure-Portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) unterstützt Sie beim Schutz Ihrer Daten gemäß den Sicherheits- und Complianceanforderungen Ihrer Organisation. Der Dienst bietet Volumeverschlüsselung für das Betriebssystem und die Datenträger Ihrer virtuellen Clustercomputer. Er ist außerdem mit [Azure Key Vault](/azure/key-vault/) verknüpft, um die Schlüssel und Geheimnisse für die Datenträgerverschlüsselung steuern und verwalten zu können. So wird sichergestellt, dass alle Daten auf den Datenträgern von virtuellen Computern verschlüsselt sind. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Aktivieren der Verschlüsselung ruhender Daten im Azure-Portal
  
Sie können die Datenträgerverschlüsselung in Ihrem Cluster über die Einstellungen für die Clustersicherheit aktivieren. Die [Verschlüsselung ruhender Daten](/azure/security/fundamentals/encryption-atrest) in Ihrem Cluster bietet Schutz für (ruhende) gespeicherte Daten. 

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Data Explorer-Clusterressource. Wählen Sie unter der Überschrift **Einstellungen** die Option **Sicherheit** aus. 

    ![Verschlüsselung ruhender Schlüssel aktivieren](media/manage-cluster-security/security-encryption-at-rest.png)

1. Klicken Sie im Fenster **Sicherheit** neben der Sicherheitseinstellung **Datenträgerverschlüsselung** auf **Ein**. 

1. Wählen Sie **Speichern** aus.
 
> [!NOTE]
> Wählen Sie **Aus** aus, um die Verschlüsselung zu deaktivieren, nachdem sie aktiviert wurde.

## <a name="azure-data-explorer-stores-data-within-a-region"></a>Azure Data Explorer speichert Daten innerhalb einer Region.

Jeder Azure Data Explorer-Cluster wird auf dedizierten Ressourcen in einer einzelnen Region ausgeführt. Alle Daten werden innerhalb der Region gespeichert. 

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen der Clusterintegrität](check-cluster-health.md)
