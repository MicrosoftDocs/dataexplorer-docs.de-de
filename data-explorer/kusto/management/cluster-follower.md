---
title: 'Cluster-Follower-Befehle: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden Cluster-Follower-Befehle in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: efa7c3237ce85938634b1c8b31b1c3e10d01a6f8
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97514102"
---
# <a name="cluster-follower-commands"></a>Cluster-Follower-Befehle

Die Kontroll Befehle zum Verwalten der Follower-Cluster Konfiguration sind unten aufgeführt. Diese Befehle werden synchron ausgeführt, aber bei der nächsten periodischen Schema Aktualisierung angewendet. Daher kann es einige Minuten dauern, bis die neue Konfiguration angewendet wird.

Die Follower-Befehle enthalten [Befehle auf Datenbankebene](#database-level-commands) und [Befehle auf Tabellenebene](#table-level-commands).

## <a name="database-policy-overrides"></a>Über schreibungen von Daten Bank Richtlinien

In einer Datenbank, auf die ein Follower-Cluster folgt, können die Richtlinien auf Datenbankebene der zwischen [Speicherungs Richtlinie](#caching-policy) und [autorisierte Prinzipale](#authorized-principals) im Follower-Cluster überschrieben werden.

### <a name="caching-policy"></a>Caching-Richtlinie

Die standardmäßige Cache [Richtlinie](cachepolicy.md) für den Follower-Cluster besteht darin, die übergeordnete Cluster Datenbank und Cache Richtlinien auf Tabellenebene beizubehalten.

|Option             |Beschreibung                                                                                                                                                                                                           |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|**Keine** (Standard) |Dabei handelt es sich um die verwendeten Cache Richtlinien, die in der Quelldatenbank (im Anbieter Cluster) definiert sind.                                                                                                                           |
|**replace**        |Die Quelldatenbank in der Gruppenrichtlinien-Cluster Datenbank und zwischen Speicherungs Richtlinien auf Tabellenebene werden entfernt (festgelegt auf `null` ). Sie werden durch die in der Datenbank definierten und Überschreibungs Richtlinien auf Tabellenebene ersetzt, sofern definiert.|
|**union**          |Die Quelldatenbank in der übergeordneten Cluster Datenbank und die Cache Richtlinien auf Tabellenebene werden mit den in der Datenbank definierten und den Überschreibungs Richtlinien auf Tabellenebene vereinheitalten.                                              |

> [!NOTE]
>  * Wenn die Auflistung der zwischen Speicherungs Richtlinien auf Tabellen-und Tabellenebene *leer* ist, wird *alles* standardmäßig zwischengespeichert.
>  * Sie können die Außerkraftsetzungs Richtlinie auf Datenbankebene auf festlegen `0d` , sodass *nichts* standardmäßig zwischengespeichert wird.

### <a name="authorized-principals"></a>Autorisierte Prinzipale

Die standardmäßigen [autorisierten Prinzipale](access-control/index.md#authorization) behalten die Quelldatenbank der autorisierten Prinzipale des Führer Clusters bei.

|Option             |Beschreibung                                                                                                                              |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
|**Keine** (Standard) |Die verwendeten autorisierten Prinzipale sind die, die in der Quelldatenbank (im übergeordneten Cluster) definiert sind.                                         |
|**replace**        |Die Quelldatenbank in den autorisierten Prinzipale des führenden Clusters wird durch die in der überschreiben autorisierten Prinzipale definierten ersetzt.  |
|**union**          |Die Quelldatenbank in den autorisierten Prinzipale des Führer Clusters wird mit den in der überschreiben autorisierten Prinzipale definierten Berechtigungen vereinigt. |

> [!NOTE]
> Wenn die Auflistung der über schreibenden autorisierten Prinzipale *leer* ist, gibt es keine Prinzipale auf Datenbankebene.

## <a name="table-policy-overrides"></a>Über schreibungen von Tabellen Richtlinien

In einer Tabelle in einer Datenbank, auf die ein Follower-Cluster folgt, kann die Cache [Richtlinie](cachepolicy.md) auf Tabellenebene im Follower-Cluster überschrieben werden.
Der Standardwert ist die Beibehaltung der zwischen Speicherungs Richtlinie der Quell Tabelle. Wenn diese Richtlinie in der Quelldatenbank vorhanden ist, wird Sie auf dem Follower-Cluster weiterhin wirksam.
Die `replace` Option wird unterstützt. bei der Verwendung wird die zwischen Speicherungs Richtlinie der Quell Tabelle durch die als außer Kraft Setzung definierte ersetzt.

## <a name="database-level-commands"></a>Befehle auf Datenbankebene

### <a name="show-follower-database"></a>. Show Follower Database

Zeigt eine Datenbank (oder Datenbanken) an, auf die von einem anderen Spitzencluster aus, auf dem mindestens eine außer Kraft setzung auf Datenbankebene konfiguriert ist.

**Syntax**

`.show``follower` `database` *DatabaseName*

`.show``follower` `databases` `(` *DatabaseName1* `,` ...`,` *Databasenamen*`)`

**Ausgabe** 

| Ausgabeparameter                     | type    | Beschreibung                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | Der Name der Datenbank, die befolgt wird.                                                                           |
| LeaderClusterMetadataPath            | String  | Der Pfad zum Metadatencontainer des leaderclusters.                                                               |
| Cachingpolicyoverride                | String  | Eine überschreibungscachingrichtlinie für die Datenbank, die als JSON oder NULL serialisiert wird.                                         |
| Authorizedprincipalsoverride         | String  | Eine Überschreibungs Auflistung von autorisierten Prinzipale für die Datenbank, die als JSON oder NULL serialisiert werden.                    |
| Authorizedprincipalsmodificationkind | String  | Die Änderungsart, die mithilfe von authorizedprincipalsoverride (oder) angewendet werden soll `none` `union` `replace` .                  |
| Cachingpoliciesmodificationkind      | String  | Die Änderungsart, die mithilfe von Daten Bank Überschreibungen (oder) über schreibungen auf Tabellenebene angewendet `none` werden soll `union` `replace` . |
| Isautoprefetchenabled                | Boolean | Gibt an, ob neue Daten bei jeder Schema Aktualisierung vorab abgerufen werden.        |
| TableMetadataOverrides               | String  | Eine JSON-Serialisierung von Eigenschafts Überschreibungen auf Tabellenebene (sofern diese definiert sind).                                      |

### <a name="alter-follower-database-policy-caching"></a>. Alter Follower-Daten Bank Richtlinien Caching

Ändert eine Richtlinie für die Zwischenspeicherung von Follower-Datenbanken, um die für die Quelldatenbank im übergeordneten Cluster festgelegte zu überschreiben Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

**Notizen**

* Der Standardwert für das zwischen `modification kind` Speichern von Richtlinien ist `union` . Um den zu ändern, `modification kind` verwenden Sie den [`.alter follower database caching-policies-modification-kind`](#alter-follower-database-caching-policies-modification-kind) Befehl.
* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, können Sie die folgenden `.show` Befehle ausführen:
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* Wenn Sie die Außerkraftsetzungs Einstellungen für die Follower-Datenbank anzeigen, nachdem die Änderung vorgenommen wurde, können Sie mithilfe von [`.show follower database`](#show-follower-database)

**Syntax**

`.alter``follower` `database` *DatabaseName* `policy` `caching` `hot` `=` *hotdataspan*

**Beispiel**

```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. Löschen der Zwischenspeicherung von Daten Bank Richtlinien

Löscht eine Daten Bank Überschreibungs Richtlinie für die Überschreibung. Dies bewirkt, dass die Richtlinie, die in der Quelldatenbank im Spitzencluster festgelegt ist, gültig ist.
Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich. 

**Notizen**

* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, können Sie die folgenden `.show` Befehle ausführen:
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [`.show follower database`](#show-follower-database)

**Syntax**

`.delete``follower` `database` *DatabaseName* `policy``caching`

**Beispiel**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>. Hinzufügen von Follower-Daten Bank Prinzipale

Fügt der Follower-Datenbanksammlung von autorisierte Prinzipale überschreiben autorisierte Prinzipale hinzu. Hierfür ist die [databaseadmin-Berechtigung](../management/access-control/role-based-authorization.md)erforderlich.

**Notizen**

* Der Standardwert `modification kind` für solche autorisierten Prinzipale ist `none` . So ändern Sie die `modification kind` Verwendung von [Alter Follower Database Principals-Change-Kind](#alter-follower-database-principals-modification-kind)
* Die effektive Auflistung von Prinzipale können Sie nach der Änderung mithilfe der folgenden `.show` Befehle anzeigen:
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.add``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` )-Rolle `(` *PRINCIPAL1* `,` ... `,` *Prinzipal Name* `)` [ `'` *Hinweise* `'` ]

**Beispiel**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

### <a name="drop-follower-database-principals"></a>. Drop Follower Database Prinzipale

Löscht autorisierte Prinzipale aus der Follower-Datenbanksammlung von autorisierten Prinzipalen außer Kraft setzen.
Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

**Notizen**

* Die effektive Auflistung von Prinzipale können Sie nach der Änderung mithilfe der folgenden `.show` Befehle anzeigen:
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [`.show follower database`](#show-follower-database)

**Syntax**

`.drop``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) `(` *PRINCIPAL1* `,` . `,` .. *Prinzipal* Name`)`

**Beispiel**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. Alter Follower Database Principals-modifizierungsart

Ändert die Art der Änderungs Änderung der Follower-Datenbank für autorisierte Prinzipale. Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

**Notizen**

* Die effektive Auflistung von Prinzipale können Sie nach der Änderung mithilfe der folgenden `.show` Befehle anzeigen:
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.alter``follower` `database` *DatabaseName* 
 `principals-modification-kind` = ( `none`  |  `union`  |  `replace` )

**Beispiel**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. Alter Follower Database Caching-Policies-Änderungs Art

Ändert die Art der Änderungs Änderung für die Follower-Datenbank und die Tabellen Zwischenspeicherung Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

**Notizen**

* Die effektive Erfassung von Cache Richtlinien auf Datenbankebene/Tabellenebene nach der Änderung kann mithilfe der Standard Befehle ausgeführt werden `.show` :
    * [`.show tables details`](show-tables-command.md)
    * [`.show database details`](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [`.show follower database`](#show-follower-database)

**Syntax**

`.alter``follower` `database` *DatabaseName* `caching-policies-modification-kind` = ( `none`  |  `union`  |  `replace` )

**Beispiel**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```

### <a name="alter-follower-database-prefetch-extents"></a>. Alter Follower Database Prefetch-Blöcke

Der Follower-Cluster kann warten, bis neue Daten aus dem zugrunde liegenden Speicher in den SSD (Cache) der Knoten abgerufen werden, bevor diese Daten abgefragt werden können.

Mit dem folgenden Befehl wird die Konfiguration der Follower-Datenbank für das vorab Abrufen neuer Blöcke bei jeder Schema Aktualisierung geändert. Für diesen Befehl sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

> [!WARNING]
> * Diese Einstellung kann die Aktualität von Daten in der Follower-Datenbank beeinträchtigen.
> * Die Standardkonfiguration ist `false` , und es wird empfohlen, den Standardwert zu verwenden.
> * Wenn Sie die Einstellung in ändern, sollten Sie `true` die Auswirkungen auf die Aktualität für einen Zeitraum nach der Konfigurationsänderung genau auswerten.

**Syntax**

`.alter``follower` `database` *DatabaseName* `prefetch-extents` = ( `true`  |  `false` )

**Beispiel**

<!-- csl -->
```
.alter follower database MyDB prefetch-extents = false
```

## <a name="table-level-commands"></a>Befehle auf Tabellenebene

### <a name="alter-follower-table-policy-caching"></a>. Alter Follower Table Policy Caching

Ändert eine Cache Richtlinie auf Tabellenebene für die Follower-Datenbank, um die Richtlinie zu überschreiben, die für die Quelldatenbank im Anbieter Cluster festgelegt wurde.
Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich. 

**Notizen**

* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, können Sie die folgenden `.show` Befehle ausführen:
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [`.show follower database`](#show-follower-database)

**Syntax**

`.alter``follower` `database` *DatabaseName* -Tabelle, *TableName* `policy` `caching` `hot` `=` *hotdataspan*

`.alter``follower` `database` *DatabaseName* -Tabellen `(` *TableName1* `,` ... `,` *TableNameN* `)` `policy` `caching` `hot` `=` *Hotdataspan*

**Beispiel**

```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>. Löschen der Zwischenspeicherung von Hash Tabellen Richtlinien

Löscht eine über schreibende zwischen Speicherungs Richtlinie auf Tabellenebene für die Follower-Datenbank, sodass die Richtlinie für die Quelldatenbank im Spitzencluster auf die effektive festgelegt ist. Erfordert [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md). 

**Notizen**

* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, können Sie die folgenden `.show` Befehle ausführen:
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [`.show follower database`](#show-follower-database)

**Syntax**

`.delete``follower` `database` *DatabaseName* ( `table` *Tabellenname* `policy` )`caching`

`.delete``follower` `database` *DatabaseName* `tables` `(` *TableName1* `,` ... `,` *TableNameN* `)` `policy``caching`

**Beispiel**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>Beispielkonfiguration

Im folgenden finden Sie Beispiel Schritte zum Konfigurieren einer Follower-Datenbank.

In diesem Beispiel:

* In unserem Follower-Cluster wird die Datenbank des übergeordneten Clusters () `MyFollowerCluster` nachverfolgt `MyDatabase` `MyLeaderCluster` .
    * `MyDatabase` hat `N` Tabellen: `MyTable1` , `MyTable2` , `MyTable3` ,... `MyTableN` ( `N` > 3).
    * Auf `MyLeaderCluster`:
    
    | `MyTable1` Caching-Richtlinie | `MyTable2` Caching-Richtlinie | `MyTable3`...`MyTableN` Caching-Richtlinie   | `MyDatabase` Autorisierte Prinzipale                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | Hot Data span = `7d`      | Hot Data span = `30d`     | Hot Data span = `365d`                   | *Viewer*  =  `aadgroup=scubadivers@contoso.com` ; *Administratoren* = `aaduser=jack@contoso.com` |
     
    * `MyFollowerCluster`Wir wünschen Folgendes:
    
    | `MyTable1` Caching-Richtlinie | `MyTable2` Caching-Richtlinie | `MyTable3`...`MyTableN` Caching-Richtlinie   | `MyDatabase` Autorisierte Prinzipale                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | Hot Data span = `1d`      | Hot Data span = `3d`      | Hot Data Span = `0d` (Nothing wird zwischengespeichert) | *Administratoren*  =  `aaduser=jack@contoso.com` , *Viewer* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster`Und `MyLeaderCluster` müssen sich in der gleichen Region befinden.

### <a name="steps-to-execute"></a>Auszuführende Schritte

*Voraussetzung:* Richten `MyFollowerCluster` Sie den Cluster ein, `MyDatabase` der der Datenbank aus dem Cluster `MyLeaderCluster`

> [!NOTE]
> Der Prinzipal, auf dem die Steuerbefehle ausgeführt werden, wird als `DatabaseAdmin` in der Datenbank erwartet `MyDatabase` .

#### <a name="show-the-current-configuration"></a>Aktuelle Konfiguration anzeigen

Sehen Sie sich die aktuelle Konfiguration an, nach der die folgenden Schritte ausgeführt `MyDatabase` werden `MyFollowerCluster` :

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Column                              | Wert                                                    |
|-------------------------------------|----------------------------------------------------------|
|DatabaseName                         | MyDatabase                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|Cachingpolicyoverride                | NULL                                                     |
|Authorizedprincipalsoverride         | []                                                       |
|Authorizedprincipalsmodificationkind | Keine                                                     |
|Isautoprefetchenabled                | Falsch                                                    |
|TableMetadataOverrides               |                                                          |
|Cachingpoliciesmodificationkind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>Autorisierte Prinzipale außer Kraft setzen

Ersetzen Sie die Auflistung der autorisierten Prinzipale für `MyDatabase` auf `MyFollowerCluster` durch eine Sammlung, die nur einen Aad-Benutzer als Datenbankadministrator enthält, und einen Aad-Benutzer als Datenbank-Viewer:

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

Nur die beiden spezifischen Prinzipale sind autorisiert, auf zuzugreifen. `MyDatabase``MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| Rolle                       | Principaltype | Principaldisplayname                        | Principalobjectid                    | Principalfqn                                                                      | Notizen |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| Datenbank mydatabase admin  | Aad-Benutzer      | Jack Kusto (UPN: jack@contoso.com )       | 12345678-ABCD-efef-1234-350 BF 486087b | aaduser = 87654321-ABCD-efef -1234-350bf 486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| Datenbank-mydatabase-Viewer | Aad-Benutzer      | Jill Kusto (UPN: jack@contoso.com )       | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789-ABCD-efef -1234-350bf 486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| aaduser = 87654321-ABCD-efef -1234-350bf 486087b; 55555555-4444-3333-2222-2d7cd011db47 |
| aaduser = 54321789-ABCD-efef -1234-350bf 486087b; 55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>Überschreiben von Cache Richtlinien

Ersetzen Sie die Auflistung von Cache Richtlinien auf Datenbank-und Tabellenebene für ein `MyDatabase` `MyFollowerCluster` , indem Sie alle Tabellen so festlegen, dass Ihre Daten *nicht* zwischengespeichert werden, und zwar mit zwei bestimmten Tabellen, `MyTable1` `MyTable2` die Ihre Daten für die Zeiträume von bzw. zwischengespeichert werden `1d` `3d` :

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

Nur für die beiden spezifischen Tabellen werden Daten zwischengespeichert, und der Rest der Tabellen hat einen heißen Daten Zeitraum von `0d` :

```kusto
.show tables details
| summarize TableNames = make_list(TableName) by CachingPolicy
```
| Cachingpolicy                                                                | TableNames                  |
|------------------------------------------------------------------------------|-----------------------------|
| {"Datahotspan": {"Value": "1,00:00:00"}, "indexhotspan": {"Value": "1,00:00:00"}} | ["MyTable1"]                |
| {"Datahotspan": {"Value": "3.00:00:00"}, "indexhotspan": {"Value": "3.00:00:00"}} | ["MyTable2"]                |
| {"Datahotspan": {"Value": "0.00:00:00"}, "indexhotspan": {"Value": "0.00:00:00"}} | ["MyTable3",..., "MyTableN"] |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(TableMetadataOverrides)
| project TableMetadataOverrides
```

| TableMetadataOverrides                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|
| {"MyTable1": {"cachingpolicyoverride": {"datahotspan": {"Value": "1,00:00:00"}, "indexhotspan": {"Value": "1,00:00:00"}}}} |
| {"MyTable2": {"cachingpolicyoverride": {"datahotspan": {"Value": "3.00:00:00"}, "indexhotspan": {"Value": "3.00:00:00"}}}} |

#### <a name="summary"></a>Zusammenfassung

Sehen Sie sich die aktuelle Konfiguration `MyDatabase` an, in der befolgt wird `MyFollowerCluster` :

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Column                              | Wert                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|Cachingpolicyoverride                | {"Datahotspan": {"Value": "00:00:00"}, "indexhotspan": {"Value": "00:00:00"}}                                                                                                        |
|Authorizedprincipalsoverride         | [{"Principal": {"FullyQualifiedName": "aaduser = 87654321-ABCD-efef -1234-350bf 486087b",...}, {"Principal": {"FullyQualifiedName": "aaduser = 54321789-ABCD-efef -1234-350bf 486087b",...}] |
|Authorizedprincipalsmodificationkind | Replace                                                                                                                                                                         |
|Isautoprefetchenabled                | Falsch                                                                                                                                                                           |
|TableMetadataOverrides               | {"Mytarge\": {"cachingpolicyoverride": {"datahotspan": {"Value": "3.00:00:00"}...}, "mySourceTable": {"cachingpolicyoverride": {"datahotspan": {"Value": "1,00:00:00"},...}}}       |
|Cachingpoliciesmodificationkind      | Replace                                                                                                                                                                         |
