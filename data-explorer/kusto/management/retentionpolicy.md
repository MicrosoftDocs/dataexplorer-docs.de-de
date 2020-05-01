---
title: 'Kusto-Beibehaltungs Richtlinie steuert, wie Daten entfernt werden: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Aufbewahrungs Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 5254f2daee767f51111f2ac3d1be07b7f2bb09f4
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617390"
---
# <a name="retention-policy"></a>Aufbewahrungsrichtlinie

Die Beibehaltungs Richtlinie steuert den Mechanismus, mit dem Daten automatisch aus Tabellen entfernt werden.
Eine solche Entfernung ist in der Regel nützlich für Daten, die kontinuierlich in eine Tabelle fließen, deren Relevanz Alters basiert ist. Beispielsweise kann die Aufbewahrungs Richtlinie für eine Tabelle verwendet werden, die Diagnose Ereignisse enthält, die nach zwei Wochen möglicherweise uninteressant werden.

Die Aufbewahrungs Richtlinie kann für eine bestimmte Tabelle oder für eine gesamte Datenbank konfiguriert werden (in diesem Fall gilt sie für alle Tabellen in der Datenbank, die die Richtlinie nicht außer Kraft setzen).

Das Einrichten einer Aufbewahrungs Richtlinie ist wichtig für Cluster, die kontinuierlich Daten erfassen, wodurch die Kosten eingeschränkt werden.

Daten, die sich außerhalb der Aufbewahrungs Richtlinie befinden, sind zum Entfernen berechtigt. Kusto garantiert nicht, wann das Entfernen erfolgt (sodass Daten möglicherweise "Linger" sind, auch wenn die Aufbewahrungs Richtlinie ausgelöst wurde).

Die Aufbewahrungs Richtlinie ist in der Regel so festgelegt, dass das Alter der Daten seit der Erfassung eingeschränkt wird (siehe " [Software Update Period](#the-policy-object)" weiter unten).

> [!NOTE]
> * Der Lösch Zeitpunkt ist unpräzise. Das System stellt sicher, dass die Daten nicht gelöscht werden, bevor der Grenzwert überschritten wird. das Löschen erfolgt jedoch nicht unmittelbar nach diesem Punkt.
> * Ein vorläufiger Lösch Zeitraum von 0 kann als Teil einer Aufbewahrungs Richtlinie auf Tabellenebene (aber nicht als Teil einer Aufbewahrungs Richtlinie auf Datenbankebene) festgelegt werden.
>   * Wenn dies der Fall ist, werden die erfassten Daten nicht in die Quell Tabelle übernommen, sodass die Daten nicht persistent gespeichert werden müssen.
>   * Eine solche Konfiguration ist besonders nützlich, wenn die Daten in eine Tabelle aufgenommen werden.
>   Eine transaktionale [Update Richtlinie](updatepolicy.md) wird verwendet, um Sie zu transformieren und die Ausgabe in eine andere Tabelle umzuleiten.

## <a name="the-policy-object"></a>Das Policy-Objekt

Eine Beibehaltungs Richtlinie umfasst die folgenden Eigenschaften:

* **Softwaretest Zeitraum**:
    * Eine Zeitspanne, für die sichergestellt ist, dass die Daten für die Abfrage verfügbar gehalten werden, gemessen seit der erfassten Zeit.
    * Der Standardwert lautet `100 years`.
    * Wenn Sie den vorläufig Lösch Zeitraum einer Tabelle oder Datenbank ändern, gilt der neue Wert sowohl für vorhandene als auch für neue Daten.
* Wieder **Herstellbarkeit**:
    * Daten Wiederherstell barkeit (aktiviert/deaktiviert), nachdem die Daten gelöscht wurden
    * Der Standardwert lautet `enabled`.
    * Wenn diese Einstellung `enabled`auf festgelegt ist, können die Daten nach dem Löschvorgang 14 Tage lang wieder hergestellt werden.

## <a name="control-commands"></a>Steuerungsbefehle

* Verwenden Sie [. zeigen Sie die Richtlinien Beibehaltung](../management/retention-policy.md) an, um die aktuelle Aufbewahrungs Richtlinie für eine Datenbank oder eine Tabelle anzuzeigen.
* Ändern Sie die aktuelle Aufbewahrungs Richtlinie einer Datenbank oder Tabelle mithilfe von [. Alter Policy Retention](../management/retention-policy.md) .

## <a name="defaults"></a>Standardeinstellungen

Wenn eine Datenbank oder eine Tabelle erstellt wird, wird standardmäßig keine Beibehaltungs Richtlinie definiert.
In den gängigen Fällen wird die Datenbank erstellt, und dann wird die Beibehaltungs Richtlinie sofort gemäß den bekannten Anforderungen vom Ersteller festgelegt.
Wenn Sie einen [Show-Befehl](../management/retention-policy.md) für die Aufbewahrungs Richtlinie einer Datenbank oder Tabelle ausführen, für die die Richtlinie `Policy` nicht fest `null`gelegt wurde, wird als angezeigt.

Die standardmäßige Aufbewahrungs Richtlinie (mit den oben erwähnten Standardwerten) kann mithilfe des folgenden Befehls angewendet werden:

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

Dies führt dazu, dass das folgende Richtlinien Objekt auf die Datenbank oder Tabelle angewendet wird:

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

Das Löschen der Aufbewahrungs Richtlinie einer Datenbank oder Tabelle kann mithilfe des folgenden Befehls erfolgen:

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Beispiele

Der Cluster verfügt über eine Datenbank mit `MyDatabase`dem Namen, `MyTable1`mit `MyTable2` Tabellen und`MySpecialTable`

**1. Legen Sie für alle Tabellen in der Datenbank einen Zeitraum von 7 Tagen mit vorläufiger Löschung fest, und deaktivieren Sie die Wiederherstellbarkeit**:

* *Option 1 (empfohlen)*: Legen Sie eine Aufbewahrungs Richtlinie auf Datenbankebene mit einem vorläufigen Lösch Zeitraum von sieben Tagen und der wieder Herstellbarkeit deaktiviert fest, und stellen Sie sicher, dass keine Richtlinien auf Tabellenebene festgelegt sind.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *Option 2*: Legen Sie für jede Tabelle eine Beibehaltungs Richtlinie auf Tabellenebene mit einem vorläufigen Lösch Zeitraum von sieben Tagen und deaktivierter wieder Herstellbarkeit fest.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. Einrichten von `MyTable1`Tabellen `MyTable2` , um einen Zeitraum von 7 Tagen mit vorläufiger Löschung und eine aktivierte Wiederherstellbarkeit zu erhalten, und auf einen vorläufigen Lösch Zeitraum von 14 Tagen und deaktivierte wieder Herstellbarkeit festgelegt `MySpecialTable` **:

* *Option 1 (empfohlen)*: Legen Sie eine Aufbewahrungs Richtlinie auf Datenbankebene mit einem vorläufigen Lösch Zeitraum von sieben Tagen und der aktivierten wieder Herstellbarkeit fest, und legen Sie eine Aufbewahrungs Richtlinie auf Tabellenebene mit einem vorläufigen Lösch Zeitraum von 14 Tagen `MySpecialTable`und der wieder Herstellbarkeit für deaktiviert fest.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Option 2*: Legen Sie für jede Tabelle eine Beibehaltungs Richtlinie auf Tabellenebene mit dem gewünschten vorläufigen Lösch Zeitraum und der gewünschten Wiederherstellbarkeit fest.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. Einrichten von `MyTable1`Tabellen `MyTable2` , um einen Zeitraum von 7 Tagen mit vorläufigem löschen zu haben `MySpecialTable` und die Daten unbegrenzt aufbewahren zu**lassen:

* *Option 1*: Legen Sie eine Aufbewahrungs Richtlinie auf Datenbankebene mit einem vorläufigen Lösch Zeitraum von sieben Tagen fest, und legen Sie eine Aufbewahrungs Richtlinie auf Tabellenebene mit einem vorläufigen Lösch Zeitraum von 100 Jahren (die Standard Aufbewahrungs Richtlinie `MySpecialTable`) für fest.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Option 2*: Legen Sie `MyTable1`für `MyTable2`Tabellen eine Beibehaltungs Richtlinie auf Tabellenebene mit dem gewünschten vorläufigen Lösch Zeitraum von sieben Tagen fest, und überprüfen Sie, ob die Richtlinie auf Datenbankebene `MySpecialTable` und auf Tabellenebene für nicht festgelegt ist.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Option 3*: Legen Sie `MyTable1`für `MyTable2`Tabellen eine Beibehaltungs Richtlinie auf Tabellenebene mit dem gewünschten vorläufigen Lösch Zeitraum von sieben Tagen fest. Legen Sie `MySpecialTable`für Tabelle eine Beibehaltungs Richtlinie auf Tabellenebene mit einem vorläufigen Lösch Zeitraum von 100 Jahren (die standardmäßige Aufbewahrungs Richtlinie) fest.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
