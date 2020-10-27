---
title: Löschen von Daten aus dem Azure-Daten-Explorer
description: In diesem Artikel werden die Löschszenarien in Azure Data Explorer beschrieben, einschließlich dem Bereinigen, dem Ablegen von Blöcken und von aufbewahrungsbasierten Löschvorgängen.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 962735c666fa94a0b86cfae8775f712ee42e6d33
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342840"
---
# <a name="delete-data-from-azure-data-explorer"></a>Löschen von Daten aus dem Azure-Daten-Explorer

Azure Data Explorer unterstützt verschiedene in diesem Artikel beschriebene Löschszenarien. 

## <a name="delete-data-using-the-retention-policy"></a>Löschen von Daten mithilfe der Aufbewahrungsrichtlinie

Azure Data Explorer löscht automatisch Daten auf der Grundlage der [Aufbewahrungsrichtlinie](kusto/management/retentionpolicy.md). Diese Methode ist die effizienteste und benutzerfreundliche Möglichkeit zum Löschen von Daten. Legen Sie die Aufbewahrungsrichtlinie auf Datenbank- oder Tabellenebene fest.

Stellen Sie sich eine Datenbank oder Tabelle vor, für die eine Beibehaltungsdauer von 90 Tagen festgelegt ist. Wenn nur die Daten von 60 Tagen erforderlich sind, löschen Sie die älteren Daten wie folgt:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Löschen von Daten durch Ablegen von Blöcken

[Der Block (Daten-Shard)](kusto/management/extents-overview.md) ist die interne Struktur, in der Daten gespeichert werden. Jeder Block kann bis zu Millionen von Datensätzen enthalten. Blöcke können einzeln oder als Gruppe mit [Befehlen zum Ablegen von Blöcken](./kusto/management/drop-extents.md) gelöscht werden.

### <a name="examples"></a>Beispiele

Sie können alle Zeilen einer Tabelle oder nur einen bestimmten Block löschen.

* Löschen aller Zeilen in einer Tabelle:

    ```kusto
    .drop extents from TestTable
    ```

* Löschen eines bestimmten Blocks:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Löschen einzelner Zeilen mithilfe der Datenbereinigung

[Die Datenbereinigung](kusto/concepts/data-purge.md) kann zum Löschen einzelner Zeilen verwendet werden. Die Löschung erfolgt nicht sofort und erfordert erhebliche Systemressourcen. Daher ist sie nur für Complianceszenarien zu empfehlen.