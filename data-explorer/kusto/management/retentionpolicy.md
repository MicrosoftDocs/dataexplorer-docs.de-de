---
title: 'Kusto-Beibehaltungs Richtlinie steuert, wie Daten entfernt werden: Azure Daten-Explorer'
description: In diesem Artikel werden die Aufbewahrungs Richtlinien in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 37d82807751a604d88eda7de75fb4978efc0ce1b
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321266"
---
# <a name="retention-policy"></a>Aufbewahrungsrichtlinie

Die Beibehaltungs Richtlinie steuert den Mechanismus, mit dem Daten automatisch aus Tabellen oder [materialisierten Sichten](materialized-views/materialized-view-overview.md)entfernt werden. Es ist hilfreich, Daten zu entfernen, die kontinuierlich in eine Tabelle fließen und deren Relevanz Alters basiert ist. Die Richtlinie kann z. b. für eine Tabelle verwendet werden, die Diagnose Ereignisse enthält, die nach zwei Wochen möglicherweise uninteressant werden.

Die Aufbewahrungs Richtlinie kann für eine bestimmte Tabelle oder materialisierte Sicht oder für eine gesamte Datenbank konfiguriert werden. Die Richtlinie gilt dann für alle Tabellen in der Datenbank, die Sie nicht außer Kraft setzen.

Das Einrichten einer Aufbewahrungs Richtlinie ist wichtig für Cluster, die kontinuierlich Daten erfassen, wodurch die Kosten eingeschränkt werden.

Daten, die sich außerhalb der Aufbewahrungs Richtlinie befinden, sind zum Entfernen berechtigt. Es gibt keine besondere Garantie, wenn das Entfernen erfolgt. Daten können auch dann "Linger" sein, wenn die Aufbewahrungs Richtlinie ausgelöst wird.

Die Aufbewahrungs Richtlinie wird am häufigsten festgelegt, um das Alter der Daten seit der Erfassung einzuschränken. Weitere Informationen finden Sie unter [Software Update Period](#the-policy-object).

> [!NOTE]
> * Der Lösch Zeitpunkt ist unpräzise. Das System stellt sicher, dass die Daten nicht gelöscht werden, bevor der Grenzwert überschritten wird. das Löschen erfolgt jedoch nicht unmittelbar nach diesem Punkt.
> * Ein vorläufiger Lösch Zeitraum von 0 kann als Teil einer Aufbewahrungs Richtlinie auf Tabellenebene festgelegt werden, jedoch nicht als Teil einer Aufbewahrungs Richtlinie auf Datenbankebene.
>   * Wenn dies der Fall ist, werden die erfassten Daten nicht in die Quell Tabelle übernommen, sodass die Daten nicht persistent gespeichert werden müssen. Daher `Recoverability` kann nur auf festgelegt werden `Disabled` .
>   * Eine solche Konfiguration ist besonders nützlich, wenn die Daten in eine Tabelle aufgenommen werden.
> Eine transaktionale [Update Richtlinie](updatepolicy.md) wird verwendet, um Sie zu transformieren und die Ausgabe in eine andere Tabelle umzuleiten.

## <a name="the-policy-object"></a>Das Policy-Objekt

Eine Beibehaltungs Richtlinie umfasst die folgenden Eigenschaften:

* **Softwaretest Zeitraum**:
    * Die Zeitspanne, für die sichergestellt ist, dass die Daten für die Abfrage verfügbar gehalten werden. Der Zeitraum wird beginnend mit dem Zeitpunkt gemessen, zu dem die Daten erfasst wurden.
    * Wird standardmäßig auf `100 years` festgelegt.
    * Wenn Sie den vorläufig Lösch Zeitraum einer Tabelle oder Datenbank ändern, gilt der neue Wert sowohl für vorhandene als auch für neue Daten.
* Wieder **Herstellbarkeit**:
    * Daten Wiederherstell barkeit (aktiviert/deaktiviert), nachdem die Daten gelöscht wurden.
    * Wird standardmäßig auf `Enabled` festgelegt.
    * Wenn diese Einstellung auf festgelegt `Enabled` ist, können die Daten 14 Tage nach dem vorläufig gelöschten löschen wieder hergestellt werden.

## <a name="control-commands"></a>Steuerungsbefehle

* Verwenden [`.show policy retention`](../management/retention-policy.md) Sie, um die aktuelle Aufbewahrungs Richtlinie für eine Datenbank, eine Tabelle oder eine [materialisierte Sicht](materialized-views/materialized-view-overview.md)anzuzeigen.
* Verwenden [`.alter policy retention`](../management/retention-policy.md) Sie, um die aktuelle Aufbewahrungs Richtlinie einer Datenbank, Tabelle oder [materialisierten Sicht](materialized-views/materialized-view-overview.md)zu ändern.

## <a name="defaults"></a>Standardeinstellungen

Wenn eine Datenbank oder eine Tabelle erstellt wird, wird standardmäßig keine Beibehaltungs Richtlinie definiert. Normalerweise wird die Datenbank erstellt, und die Beibehaltungs Richtlinie wird vom Ersteller gemäß den bekannten Anforderungen sofort festgelegt.
Wenn Sie einen [ `.show` Befehl](../management/retention-policy.md) für die Aufbewahrungs Richtlinie einer Datenbank oder Tabelle ausführen, für die die Richtlinie nicht festgelegt wurde, wird `Policy` als angezeigt `null` .

Die standardmäßige Aufbewahrungs Richtlinie mit den oben erwähnten Standardwerten kann mithilfe des folgenden Befehls angewendet werden.

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
.alter materialized-view ViewName policy retention "{}"
```

Der Befehl führt dazu, dass das folgende Richtlinien Objekt auf die Datenbank oder Tabelle angewendet wird.

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

Das Löschen der Aufbewahrungs Richtlinie einer Datenbank oder Tabelle kann mithilfe des folgenden Befehls erfolgen.

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Beispiele

Für einen Cluster, der über eine Datenbank `MyDatabase` mit dem Namen mit den Tabellen `MyTable1` , `MyTable2` und verfügt `MySpecialTable` .

### <a name="soft-delete-period-of-seven-days-and-recoverability-disabled"></a>Vorläufiger Lösch Zeitraum von sieben Tagen und wieder Herstellbarkeit deaktiviert

Legen Sie für alle Tabellen in der Datenbank einen vorläufig Lösch Zeitraum von sieben Tagen und die Wiederherstellbarkeit fest.

* *Option 1 (empfohlen)*: Legen Sie eine Aufbewahrungs Richtlinie auf Datenbankebene fest, und vergewissern Sie sich, dass keine Richtlinien auf Tabellenebene festgelegt sind.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge materialized-view ViewName policy retention softdelete = 7d 
```

* *Option 2*: Legen Sie für jede Tabelle eine Beibehaltungs Richtlinie auf Tabellenebene fest, wobei ein vorläufiger Lösch Zeitraum von sieben Tagen und die Wiederherstellbarkeit deaktiviert ist.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

### <a name="soft-delete-period-of-seven-days-and-recoverability-enabled"></a>Vorläufiger Lösch Zeitraum von sieben Tagen und wieder Herstellbarkeit aktiviert

* Legen Sie Tabellen fest `MyTable1` , und legen `MyTable2` Sie einen vorläufigen Lösch Zeitraum von sieben Tagen und die Wiederherstellbarkeit aktiviert fest.
* Legen `MySpecialTable` Sie fest, dass ein vorläufiger Lösch Zeitraum von 14 Tagen und die Wiederherstellbarkeit deaktiviert ist.

* *Option 1 (empfohlen)*: Legen Sie eine Aufbewahrungs Richtlinie auf Datenbankebene fest, und legen Sie eine Aufbewahrungs Richtlinie auf Tabellenebene fest.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Option 2*: Legen Sie für jede Tabelle eine Beibehaltungs Richtlinie auf Tabellenebene fest, die über die relevante vorläufige löschfrist und die Wiederherstellbarkeit verfügt.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

### <a name="soft-delete-period-of-seven-days-and-myspecialtable-keeps-its-data-indefinitely"></a>Vorläufiger Lösch Zeitraum von sieben Tagen und `MySpecialTable` speichert seine Daten unbegrenzt

Legen Sie Tabellen fest `MyTable1` , und legen `MyTable2` Sie einen vorläufigen Lösch Zeitraum von sieben Tagen fest, und `MySpecialTable` halten Sie die Daten unbegrenzt beibehalten.

* *Option 1*: Legen Sie eine Aufbewahrungs Richtlinie auf Datenbankebene fest, und legen Sie eine Beibehaltungs Richtlinie auf Tabellenebene fest, wobei ein vorläufiger Lösch Zeitraum von 100 Jahren, die Standard Aufbewahrungs Richtlinie für, festgelegt ist `MySpecialTable` .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Option 2*: Legen Sie für Tabellen `MyTable1` und `MyTable2` eine Beibehaltungs Richtlinie auf Tabellenebene fest, und überprüfen Sie, ob die Richtlinie auf Datenbankebene und auf Tabellenebene für `MySpecialTable` nicht festgelegt ist.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Option 3*: Legen Sie für Tabellen `MyTable1` und `MyTable2` eine Beibehaltungs Richtlinie auf Tabellenebene fest. Legen Sie für Tabelle `MySpecialTable` eine Beibehaltungs Richtlinie auf Tabellenebene mit einem vorläufigen Lösch Zeitraum von 100 Jahren, der Standard Aufbewahrungs Richtlinie, fest.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
