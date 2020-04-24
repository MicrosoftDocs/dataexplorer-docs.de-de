---
title: Aufbewahrungsrichtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Aufbewahrungsrichtlinie in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 81e08b6e007a6e3c669e7138e1d36ae1e701d442
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520314"
---
# <a name="retention-policy"></a>Aufbewahrungsrichtlinie

Die Aufbewahrungsrichtlinie steuert den Mechanismus, mit dem Daten automatisch aus Tabellen entfernt werden.
Eine solche Entfernung ist in der Regel nützlich für Daten, die kontinuierlich in eine Tabelle fließen, deren Relevanz altersabhängig ist. Die Aufbewahrungsrichtlinie kann z. B. für eine Tabelle verwendet werden, die Diagnoseereignisse enthält, die nach zwei Wochen möglicherweise nicht mehr interesing.

Die Aufbewahrungsrichtlinie kann für eine bestimmte Tabelle oder eine ganze Datenbank konfiguriert werden (in diesem Fall gilt sie für alle Tabellen in der Datenbank, die die Richtlinie nicht überschreiben).

Das Einrichten einer Aufbewahrungsrichtlinie ist wichtig für Cluster, die kontinuierlich Daten aufzeichnen, wodurch die Kosten begrenzt werden.

Daten, die sich "außerhalb" der Aufbewahrungsrichtlinie befindet, können entfernt werden. Kusto übernimmt keine Garantie, wenn die Entfernung erfolgt (so können Daten auch dann "verweilen", wenn die Aufbewahrungsrichtlinie ausgelöst wurde).

Die Aufbewahrungsrichtlinie wird am häufigsten festgelegt, um das Alter der Daten seit der Aufnahme zu begrenzen (siehe [SoftDeletePeriod](#the-policy-object), unten).

> [!NOTE]
> * Die Löschzeit ist ungenau. Das System garantiert, dass Daten nicht gelöscht werden, bevor das Limit überschritten wird, aber das Löschen erfolgt nicht unmittelbar nach diesem Punkt.
> * Ein Soft-Lösch-Zeitraum von 0 kann als Teil einer Aufbewahrungsrichtlinie auf Tabellenebene festgelegt werden (jedoch nicht als Teil einer Aufbewahrungsrichtlinie auf Datenbankebene).
>   * Wenn dies geschieht, werden die aufgenommenen Daten nicht an die Quelltabelle gebunden, sodass die Daten nicht beibehalten werden müssen.
>   * Eine solche Konfiguration ist vor allem dann nützlich, wenn die Daten in eine Tabelle aufgenommen werden.
>   Eine [Transaktionsaktualisierungsrichtlinie](updatepolicy.md) wird verwendet, um sie zu transformieren und die Ausgabe in eine andere Tabelle umzuleiten.

## <a name="the-policy-object"></a>Das Richtlinienobjekt

Eine Aufbewahrungsrichtlinie enthält die folgenden Eigenschaften:

* **SoftDeletePeriod**:
    * Eine Zeitspanne, für die sichergestellt ist, dass die Daten für Abfragen verfügbar gehalten werden, gemessen seit dem Zeitpunkt, zu dem sie aufgenommen wurden.
    * Der Standardwert lautet `100 years`.
    * Beim Ändern des Soft-Löschzeitraums einer Tabelle oder Datenbank gilt der neue Wert sowohl für vorhandene als auch für neue Daten.
* **Wiederherstellbarkeit**:
    * Datenwiederherstellbarkeit (aktiviert/deaktiviert), nachdem die Daten gelöscht wurden
    * Der Standardwert lautet `enabled`.
    * Wenn auf `enabled`gesetzt, können die Daten 14 Tage nach dem Löschen wiederhergestellt werden.

## <a name="control-commands"></a>Steuerungsbefehle

* Verwenden Sie [.show-Richtlinienaufbewahrung,](../management/retention-policy.md) um die aktuelle Aufbewahrungsrichtlinie für eine Datenbank oder Tabelle anzuzeigen.
* Verwenden Sie [.alter-Richtlinienaufbewahrung,](../management/retention-policy.md) um die aktuelle Aufbewahrungsrichtlinie einer Datenbank oder Tabelle zu ändern.

## <a name="defaults"></a>Standardeinstellungen

Wenn eine Datenbank oder Tabelle erstellt wird, ist standardmäßig keine Aufbewahrungsrichtlinie definiert.
In allgemeinen Fällen wird die Datenbank erstellt und hat dann sofort ihre Aufbewahrungsrichtlinie vom Ersteller entsprechend bekannten Anforderungen festgelegt.
Wenn Sie einen [Showbefehl](../management/retention-policy.md) für die Aufbewahrungsrichtlinie einer Datenbank oder Tabelle `Policy` ausführen, für die bzw. deren Richtlinie nicht festgelegt wurde, wird als `null`angezeigt.

Die Standardaufbewahrungsrichtlinie (mit den oben genannten Standardwerten) kann mit dem folgenden Befehl angewendet werden:

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

Dies führt zu dem folgenden Richtlinienobjekt, das auf die Datenbank oder Tabelle angewendet wird:

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

Das Löschen der Aufbewahrungsrichtlinie einer Datenbank oder Tabelle kann mit dem folgenden Befehl erfolgen:

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Beispiele

Da Ihr Cluster über `MyDatabase`eine `MyTable1`Datenbank `MyTable2` mit dem Namen , mit Tabellen und`MySpecialTable`

**1. Festlegen aller Tabellen in der Datenbank auf einen Soft-Delete-Zeitraum von 7 Tagen und deaktivierte Wiederherstellbarkeit:**

* *Option 1 (Empfohlen)*: Legen Sie eine Aufbewahrungsrichtlinie auf Datenbankebene mit einem Soft-Löschzeitraum von sieben Tagen fest, und die Wiederherstellbarkeit ist deaktiviert, und stellen Sie sicher, dass keine Richtlinien auf Tabellenebene festgelegt sind.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *Option 2*: Legen Sie für jede Tabelle eine Aufbewahrungsrichtlinie auf Tabellenebene mit einem Soft-Delete-Zeitraum von sieben Tagen fest, und die Wiederherstellbarkeit ist deaktiviert.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. Tabellen `MyTable1` `MyTable2` festlegen, dass ein Soft-Lösch-Zeitraum von 7 `MySpecialTable` Tagen und wiederherstellbare Fähigkeit aktiviert ist und ein Soft-Lösch-Zeitraum von 14 Tagen festgelegt ist und die Wiederherstellbarkeit deaktiviert ist:**

* *Option 1 (empfohlen)*: Legen Sie eine Aufbewahrungsrichtlinie auf Datenbankebene mit einem Soft-Löschzeitraum von sieben Tagen und aktivierter Wiederherstellbarkeit `MySpecialTable`fest, und legen Sie eine Aufbewahrungsrichtlinie auf Tabellenebene mit einem Soft-Lösch-Zeitraum von 14 Tagen fest, und die Wiederherstellbarkeit ist für deaktiviert.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Option 2*: Legen Sie für jede Tabelle eine Aufbewahrungsrichtlinie auf Tabellenebene mit dem gewünschten Soft-Löschzeitraum und der Wiederherstellbarkeit fest.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. Tabellen `MyTable1` `MyTable2` einstellen, einen Soft-Lösch-Zeitraum von `MySpecialTable` 7 Tagen haben und seine Daten auf unbestimmte Zeit aufbewahren:**

* *Option 1*: Legen Sie eine Aufbewahrungsrichtlinie auf Datenbankebene mit einem Soft-Löschzeitraum von sieben Tagen fest, und legen Sie eine `MySpecialTable`Aufbewahrungsrichtlinie auf Tabellenebene mit einem Soft-Lösch-Zeitraum von 100 Jahren (standardaufbewahrungsrichtlinie) für fest.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Option 2*: `MyTable1` `MyTable2`Legen Sie für Tabellen eine Aufbewahrungsrichtlinie auf Tabellenebene mit dem gewünschten Soft-Löschzeitraum von `MySpecialTable` sieben Tagen fest, und stellen Sie sicher, dass die Richtlinie auf Datenbank- und Tabellenebene nicht festgelegt ist.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Option 3*: `MyTable1` `MyTable2`Legen Sie für Tabellen eine Aufbewahrungsrichtlinie auf Tabellenebene mit dem gewünschten Soft-Delete-Zeitraum von sieben Tagen fest. Legen `MySpecialTable`Sie für Tabelle eine Aufbewahrungsrichtlinie auf Tabellenebene mit einem Soft-Delete-Zeitraum von 100 Jahren fest (Standardaufbewahrungsrichtlinie).

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```