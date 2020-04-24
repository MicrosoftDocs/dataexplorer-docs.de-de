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
ms.openlocfilehash: 73b65cc59ff98bc658c542d690812ccf5ad3895a
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108455"
---
# <a name="cluster-follower-commands"></a>Cluster-Follower-Befehle

Die Kontroll Befehle zum Verwalten der Follower-Cluster Konfiguration sind unten aufgeführt. Diese Befehle werden synchron ausgeführt, aber bei der nächsten periodischen Schema Aktualisierung angewendet. Daher kann es einige Minuten dauern, bis die neue Konfiguration angewendet wird.

Die Follower-Befehle enthalten [Befehle auf Datenbankebene](#database-level-commands) und [Befehle auf Tabellenebene](#table-level-commands).

## <a name="database-level-commands"></a>Befehle auf Datenbankebene

### <a name="show-follower-database"></a>. Show Follower Database

Zeigt eine Datenbank (oder Datenbanken) an, auf die von einem anderen Spitzencluster aus, auf dem mindestens eine außer Kraft setzung auf Datenbankebene konfiguriert ist.


**Syntax**

`.show` `follower` `database` *DatabaseName*

`.show``follower` `,`DatabaseName1... *DatabaseName1* `databases` `(` `,` *Databasenamen*`)`

**Ausgabe** 

| Output-Parameter                     | type    | BESCHREIBUNG                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | Der Name der Datenbank, die befolgt wird.                                                                           |
| LeaderClusterMetadataPath            | String  | Der Pfad zum Metadatencontainer des leaderclusters.                                                               |
| Cachingpolicyoverride                | String  | Eine überschreibungscachingrichtlinie für die Datenbank, die als JSON oder NULL serialisiert wird.                                         |
| Authorizedprincipalsoverride         | String  | Eine Überschreibungs Auflistung von autorisierten Prinzipale für die Datenbank, die als JSON oder NULL serialisiert werden.                    |
| Authorizedprincipalsmodificationkind | String  | Die Änderungsart, die mithilfe von authorizedprincipalsoverride `union` ( `replace``none`oder) angewendet werden soll.                  |
| Cachingpoliciesmodificationkind      | String  | Die Änderungsart, die mithilfe von Daten Bank Überschreibungen (`none`oder `union` `replace`) über schreibungen auf Tabellenebene angewendet werden soll. |
| Isautoprefetchenabled                | Boolean | Gibt an, ob neue Daten bei jeder Schema Aktualisierung vorab abgerufen werden.        |
| TableMetadataOverrides               | String  | Eine JSON-Serialisierung von Eigenschafts Überschreibungen auf Tabellenebene (sofern diese definiert sind).                                      |

### <a name="alter-follower-database-policy-caching"></a>. Alter Follower-Daten Bank Richtlinien Caching

Ändert eine Richtlinie für die Zwischenspeicherung von Follower-Datenbanken, um die für die Quelldatenbank im übergeordneten Cluster festgelegte zu überschreiben Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.



**Hinweise**

* Der Standard `modification kind` Wert für das zwischen `union`Speichern von Richtlinien ist. Um den `modification kind` zu ändern, verwenden Sie den Befehl [. Alter Follower Database Caching-Policies-Change-Kind](#alter-follower-database-caching-policies-modification-kind) .
* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, `.show` können Sie die folgenden Befehle ausführen:
    * [. Anzeigen der Daten Bank Richtlinien Beibehaltung](../management/retention-policy.md#show-retention-policy)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Wenn Sie die Außerkraftsetzungs Einstellungen für die Follower-Datenbank anzeigen, nachdem die Änderung vorgenommen wurde, können Sie die [. Show Follower-Datenbank](#show-follower-database) verwenden

**Syntax**

`.alter``follower` `hot` *DatabaseName* `policy` *HotDataSpan* DatabaseName `=` hotdataspan `caching` `database`



**Beispiel**



```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. Löschen der Zwischenspeicherung von Daten Bank Richtlinien

Löscht eine Daten Bank Überschreibungs Richtlinie für die Überschreibung. Dies bewirkt, dass die Richtlinie, die in der Quelldatenbank im Spitzencluster festgelegt ist, gültig ist.
Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich. 

**Hinweise**

* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, `.show` können Sie die folgenden Befehle ausführen:
    * [. Anzeigen der Daten Bank Richtlinien Beibehaltung](../management/retention-policy.md#show-retention-policy)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.delete` `follower` `database` *DatabaseName* `policy` `caching`

**Beispiel**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>. Hinzufügen von Follower-Daten Bank Prinzipale

Fügt der Follower-Datenbanksammlung von autorisierte Prinzipale überschreiben autorisierte Prinzipale hinzu. Hierfür ist die [databaseadmin-Berechtigung](../management/access-control/role-based-authorization.md)erforderlich.



**Hinweise**

* Der Standard `modification kind` Wert für solche autorisierten Prinzipale `none`ist. So ändern Sie `modification kind` die Verwendung von [Alter Follower Database Principals-Change-Kind](#alter-follower-database-principals-modification-kind)
* Die effektive Auflistung von Prinzipale können Sie nach der Änderung mithilfe der `.show` folgenden Befehle anzeigen:
    * [. Anzeigen von Daten Bank Prinzipale](../management/security-roles.md#managing-database-security-roles)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.add``follower` `admins` |  `(` *principal1* *DatabaseName* `users`DatabaseName | (`viewers`)-Rolle`,`PRINCIPAL1... | `monitors` `database` `,` *principaln* `)` [`'`*Notes*`'`]




**Beispiel**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

```kusto

```

### <a name="drop-follower-database-principals"></a>. Drop Follower Database Prinzipale

Löscht autorisierte Prinzipale aus der Follower-Datenbanksammlung von autorisierten Prinzipalen außer Kraft setzen.
Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

**Hinweise**

* Die effektive Auflistung von Prinzipale können Sie nach der Änderung mithilfe der `.show` folgenden Befehle anzeigen:
    * [. Anzeigen von Daten Bank Prinzipale](../management/security-roles.md#managing-database-security-roles)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.drop``follower` `admins` |  `(` *principal1* *DatabaseName*  | DatabaseName`monitors`( | ) PRINCIPAL1`,`...`users``viewers` `database` `,` *Prinzipal* Name`)`

**Beispiel**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. Alter Follower Database Principals-modifizierungsart

Ändert die Art der Änderungs Änderung der Follower-Datenbank für autorisierte Prinzipale. Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

**Hinweise**

* Die effektive Auflistung von Prinzipale können Sie nach der Änderung mithilfe der `.show` folgenden Befehle anzeigen:
    * [. Anzeigen von Daten Bank Prinzipale](../management/security-roles.md#managing-database-security-roles)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.alter``follower` `principals-modification-kind` `none` *DatabaseName* 
 DatabaseName = (`union`) | `replace` |  `database`



**Beispiel**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. Alter Follower Database Caching-Policies-Änderungs Art

Ändert die Art der Änderungs Änderung für die Follower-Datenbank und die Tabellen Zwischenspeicherung Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich.

**Hinweise**

* Die effektive Erfassung von Cache Richtlinien auf Datenbankebene/Tabellenebene nach der Änderung kann mithilfe der Standard `.show` Befehle ausgeführt werden:
    * [. Anzeigen von Tabellen Details](show-tables-command.md)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.alter``follower` `none` |  *DatabaseName* `caching-policies-modification-kind`  | DatabaseName =`replace`(`union`) `database`



**Beispiel**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```



## <a name="table-level-commands"></a>Befehle auf Tabellenebene

### <a name="alter-follower-table-policy-caching"></a>. Alter Follower Table Policy Caching

Ändert eine Cache Richtlinie auf Tabellenebene für die Follower-Datenbank, um die Richtlinie zu überschreiben, die für die Quelldatenbank im Anbieter Cluster festgelegt wurde.
Hierfür sind [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md)erforderlich. 



**Hinweise**

* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, `.show` können Sie die folgenden Befehle ausführen:
    * [. Anzeigen der Daten Bank Richtlinien Beibehaltung](../management/retention-policy.md#show-retention-policy)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**





`.alter``follower` `policy` `caching` `hot` `=` *HotDataSpan* *TableName* *DatabaseName* DatabaseName-Tabelle, TableName hotdataspan `database`

`.alter``follower` `(` *TableName1*`,` *DatabaseName* DatabaseName-Tabellen TableName1... `database` `,` *TableNameN* `)` `policy` `caching` `hot` *HotDataSpan* hotdataspan `=`

**Beispiel**



```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>. Löschen der Zwischenspeicherung von Hash Tabellen Richtlinien

Löscht eine über schreibende zwischen Speicherungs Richtlinie auf Tabellenebene für die Follower-Datenbank, sodass die Richtlinie für die Quelldatenbank im Spitzencluster auf die effektive festgelegt ist. Erfordert [databaseadmin-Berechtigungen](../management/access-control/role-based-authorization.md). 

**Hinweise**

* Wenn Sie die Richtlinie oder wirksame Richtlinien nach der Änderung anzeigen, `.show` können Sie die folgenden Befehle ausführen:
    * [. Anzeigen der Daten Bank Richtlinien Beibehaltung](../management/retention-policy.md#show-retention-policy)
    * [. Anzeigen von Daten Bank Details](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Anzeigen der Außerkraftsetzungs Einstellungen für die Follower-Datenbank nach der Änderung mithilfe von [. Show Follower Database](#show-follower-database)

**Syntax**

`.delete``follower` `table` *TableName* `policy` *DatabaseName* DatabaseName (Tabellenname) `database``caching`

`.delete``follower` `(` *DatabaseName* `tables` `,`DatabaseName TableName1... *TableName1* `database` `,` *TableNameN* `)` `policy``caching`

**Beispiel**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>Beispielkonfiguration

Im folgenden finden Sie Beispiel Schritte zum Konfigurieren einer Follower-Datenbank.

In diesem Beispiel:

* In unserem Follower- `MyFollowerCluster` Cluster wird die Datenbank `MyDatabase` des übergeordneten Clusters ( `MyLeaderCluster`) nachverfolgt.
    * `MyDatabase`hat `N` Tabellen: `MyTable1`, `MyTable2`, `MyTable3`,... `MyTableN` (`N` > 3).
    * Auf `MyLeaderCluster`:
    
    | `MyTable1`Caching-Richtlinie | `MyTable2`Caching-Richtlinie | `MyTable3`... `MyTableN` Caching-Richtlinie   | `MyDatabase`Autorisierte Prinzipale                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | Hot Data span =`7d`      | Hot Data span =`30d`     | Hot Data span =`365d`                   | *Viewers* = Viewer`aadgroup=scubadivers@contoso.com`; *Administratoren* = `aaduser=jack@contoso.com` |
     
    * `MyFollowerCluster` Wir wünschen Folgendes:
    
    | `MyTable1`Caching-Richtlinie | `MyTable2`Caching-Richtlinie | `MyTable3`... `MyTableN` Caching-Richtlinie   | `MyDatabase`Autorisierte Prinzipale                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | Hot Data span =`1d`      | Hot Data span =`3d`      | Hot Data Span = `0d` (Nothing wird zwischengespeichert) | *Admins*`aaduser=jack@contoso.com` *Viewers* Administratoren = , Viewer = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster` Und `MyLeaderCluster` müssen sich in der gleichen Region befinden.

### <a name="steps-to-execute"></a>Auszuführende Schritte

*Voraussetzung:* Richten Sie `MyFollowerCluster` den Cluster ein `MyDatabase` , der `MyLeaderCluster`der Datenbank aus dem Cluster

> [!NOTE]
> Der Prinzipal, auf dem die Steuerbefehle ausgeführt werden `DatabaseAdmin` , wird `MyDatabase`als in der Datenbank erwartet.

#### <a name="show-the-current-configuration"></a>Aktuelle Konfiguration anzeigen

Sehen Sie sich die aktuelle Konfiguration an `MyDatabase` , nach der die `MyFollowerCluster`folgenden Schritte ausgeführt werden:

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Column                              | Value                                                    |
|-------------------------------------|----------------------------------------------------------|
|DatabaseName                         | MyDatabase                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|Cachingpolicyoverride                | ungültig                                                     |
|Authorizedprincipalsoverride         | []                                                       |
|Authorizedprincipalsmodificationkind | Keine                                                     |
|Isautoprefetchenabled                | False                                                    |
|TableMetadataOverrides               |                                                          |
|Cachingpoliciesmodificationkind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>Autorisierte Prinzipale außer Kraft setzen

Ersetzen Sie die Auflistung der autorisierten Prinzipale `MyDatabase` für `MyFollowerCluster` auf durch eine Sammlung, die nur einen Aad-Benutzer als Datenbankadministrator enthält, und einen Aad-Benutzer als Datenbank-Viewer:

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

Nur die beiden spezifischen Prinzipale sind autorisiert, auf `MyDatabase` zuzugreifen.`MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| Role-Eigenschaft                       | Principaltype | Principaldisplayname                        | Principalobjectid                    | Principalfqn                                                                      | Notizen |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| Datenbank mydatabase admin  | Aad-Benutzer      | Jack Kusto (UPN: jack@contoso.com)       | 12345678-ABCD-efef-1234-350 BF 486087b | aaduser = 87654321-ABCD-efef -1234-350bf 486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| Datenbank-mydatabase-Viewer | Aad-Benutzer      | Jill Kusto (UPN: jack@contoso.com)       | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789-ABCD-efef -1234-350bf 486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

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

Ersetzen Sie `MyDatabase` die Auflistung von Cache Richtlinien auf `MyFollowerCluster` Datenbank-und Tabellenebene für ein, indem Sie alle Tabellen so festlegen, dass Ihre Daten *nicht* zwischengespeichert `MyTable1`werden `MyTable2` , und zwar mit zwei bestimmten Tabellen, die Ihre `1d` Daten `3d`für die Zeiträume von bzw. zwischengespeichert werden:

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

Nur für die beiden spezifischen Tabellen werden Daten zwischengespeichert, und der Rest der Tabellen hat einen heißen Daten Zeitraum `0d`von:

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

Sehen Sie sich die aktuelle `MyDatabase` Konfiguration an `MyFollowerCluster`, in der befolgt wird:

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Column                              | Value                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|Cachingpolicyoverride                | {"Datahotspan": {"Value": "00:00:00"}, "indexhotspan": {"Value": "00:00:00"}}                                                                                                        |
|Authorizedprincipalsoverride         | [{"Principal": {"FullyQualifiedName": "aaduser = 87654321-ABCD-efef -1234-350bf 486087b",...}, {"Principal": {"FullyQualifiedName": "aaduser = 54321789-ABCD-efef -1234-350bf 486087b",...}] |
|Authorizedprincipalsmodificationkind | Replace                                                                                                                                                                         |
|Isautoprefetchenabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"Mytarge\": {"cachingpolicyoverride": {"datahotspan": {"Value": "3.00:00:00"}...}, "mySourceTable": {"cachingpolicyoverride": {"datahotspan": {"Value": "1,00:00:00"},...}}}       |
|Cachingpoliciesmodificationkind      | Replace                                                                                                                                                                         |