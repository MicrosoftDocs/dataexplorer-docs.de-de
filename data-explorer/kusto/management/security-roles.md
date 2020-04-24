---
title: Verwaltung von Sicherheitsrollen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Verwaltung von Sicherheitsrollen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea56911ff2ad320d1070da2a4b92d94f060273cf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520093"
---
# <a name="security-roles-management"></a>Verwaltung von Sicherheitsrollen

> [!IMPORTANT]
> Bevor Sie die Autorisierungsregeln für Ihre Kusto-Cluster ändern, lesen Sie Folgendes: [Kusto-Zugriffssteuerungsübersichtssteuerungsautorisierung](../management/access-control/index.md) 
> [role based authorization](../management/access-control/role-based-authorization.md) 

In diesem Artikel werden die Steuerungsbefehle beschrieben, die zum Verwalten von Sicherheitsrollen verwendet werden.
Sicherheitsrollen definieren, welche Sicherheitsprinzipale (Benutzer und Anwendungen) über Berechtigungen zum Betrieb einer gesicherten Ressource wie einer Datenbank oder einer Tabelle verfügen und welche Vorgänge zulässig sind. Prinzipale mit der `database viewer` Sicherheitsrolle für eine bestimmte Datenbank können z. B. alle Entitäten dieser Datenbank abfragen und anzeigen (mit Ausnahme eingeschränkter Tabellen).

Die Sicherheitsrolle kann Sicherheitsprinzipalen oder Sicherheitsgruppen zugeordnet werden (die andere Sicherheitsprinzipale oder andere Sicherheitsgruppen haben können). Wenn ein Sicherheitsprinzipal versucht, einen Vorgang für eine gesicherte Ressource auszuführen, überprüft das System, ob dem Prinzipal mindestens eine Sicherheitsrolle zugeordnet ist, die Berechtigungen zum Ausführen dieses Vorgangs für die Ressource erteilt. Dies wird als **Autorisierungsprüfung**bezeichnet. Wenn die Autorisierungsprüfung fehlschlägt, wird der Vorgang abgebrochen.

**Syntax**

Syntax von Sicherheitsrollenverwaltungsbefehlen:

*Verb* *SecurableObjectType* *SecurableObjectName-Rolle* *Role* `(` [ *ListOfPrincipals* `)` [*Beschreibung*]]

* *Verb* gibt die Art der `.show` `.add`auszuführenden Aktion an: , , `.drop`, und `.set`.

    |*Verb* |Beschreibung                                  |
    |-------|---------------------------------------------|
    |`.show`|Gibt den aktuellen Wert oder die aktuellen Werte zurück.         |
    |`.add` |Fügt der Rolle einen oder mehrere Prinzipale hinzu.     |
    |`.drop`|Entfernt einen oder mehrere Prinzipale aus der Rolle.|
    |`.set` |Legt die Rolle auf die spezifische Liste der Prinzipale fest, wodurch alle vorherigen (falls vorhanden) entfernt werden.|

* *SecurableObjectType* ist die Art des Objekts, dessen Rolle angegeben ist.

    |*SecurableObjectType*|Beschreibung|
    |---------------------|-----------|
    |`database`|Die angegebene Datenbank|
    |`table`|Die angegebene Tabelle|

* *SecurableObjectName* ist der Name des Objekts.

* *Rolle* ist der Name der entsprechenden Rolle.

    |*Rolle*      |Beschreibung|
    |------------|-----------|
    |`principals`|Kann nur als Teil `.show` eines Verbs erscheinen; gibt die Liste der Prinzipale zurück, die sich auf das sicherungsfähige Objekt auswirken können.|
    |`admins`    |Behalten Sie die Kontrolle über das sicherungsfähige Objekt, einschließlich der Möglichkeit, es anzuzeigen, zu ändern und das Objekt und alle Unterobjekte zu entfernen.|
    |`users`     |Kann das sicherungsfähige Objekt anzeigen und darunter neue Objekte erstellen.|
    |`viewers`   |Kann das sicherungsfähige Objekt anzeigen.|
    |`unrestrictedviewers`|Nur auf Datenbankebene können eingeschränkte Tabellen angezeigt werden (die `viewers` `users`nicht "normal" und "normal" verfügbar gemacht werden).|
    |`ingestors` |Nur auf Datenbankebene erlauben Sie die Datenerfassung in alle Tabellen.|
    |`monitors`  ||

* *ListOfPrincipals* ist eine optionale, durch Kommas getrennte Liste von `string`Sicherheitsprinzipal-IDs (Werte vom Typ ).

* *Beschreibung* ist ein optionaler Wert des Typs, `string` der zusammen mit der Zuordnung für zukünftige Überwachungszwecke gespeichert wird.

## <a name="example"></a>Beispiel

Der folgende Befehl "Steuerelement" listet alle Sicherheitsprinzipale auf, die Zugriff auf die Tabelle `StormEvents` in der Datenbank haben:

```kusto
.show table StormEvents principals
```

Hier sind mögliche Ergebnisse aus diesem Befehl:

|Role |PrincipalType |PrincipalDisplayName |PrincipalObjectId |PrincipalFQN 
|---|---|---|---|---
|Datenbank Apsty Admin |AAD-Benutzer |Mark Smith |cd709aed-a26c-e3953dec735e |aaduser=msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>Verwalten von Datenbanksicherheitsrollen

`.set``database` *DatabaseName-Rolle* *Role* `none` [`skip-results`]

`.set``database` *DatabaseName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

`.add``database` *DatabaseName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

`.drop``database` *DatabaseName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

Der erste Befehl entfernt alle Prinzipale aus der Rolle. Die zweite entfernt alle Prinzipale aus der Rolle und legt einen neuen Satz von Prinzipalen fest. Der dritte fügt der Rolle neue Prinzipale hinzu, ohne vorhandene Prinzipale zu entfernen. Die letzte entfernt die angegebenen Prinzipale aus den Rollen und behält die anderen.

Hierbei gilt:

* *DatabaseName* ist der Name der Datenbank, deren Sicherheitsrolle geändert wird.

* *Rolle* `admins`ist: `ingestors` `monitors`, `unrestrictedviewers` `users`, `viewers`, , , oder .

* *Principal* ist ein oder mehrere Prinzipale. Informationen zum Angeben dieser Prinzipale finden Sie unter [Prinzipale und Identitätsanbieter.](./access-control/principals-and-identity-providers.md)

* `skip-results`, falls angegeben, fordert an, dass der Befehl die aktualisierte Liste der Datenbankprinzipale nicht zurückgibt.

* *Beschreibung*, falls angegeben, ist Text, der der Änderung `.show` zugeordnet und mit dem entsprechenden Befehl abgerufen wird.

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>Verwalten von Tabellensicherheitsrollen

`.set``table` *TableName-Rolle* *Role* `none` [`skip-results`]

`.set``table` *TableName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

`.add``table` *TableName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

`.drop``table` *TableName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

Der erste Befehl entfernt alle Prinzipale aus der Rolle. Die zweite entfernt alle Prinzipale aus der Rolle und legt einen neuen Satz von Prinzipalen fest. Der dritte fügt der Rolle neue Prinzipale hinzu, ohne vorhandene Prinzipale zu entfernen. Die letzte entfernt die angegebenen Prinzipale aus den Rollen und behält die anderen.

Hierbei gilt:

* *TableName* ist der Name der Tabelle, deren Sicherheitsrolle geändert wird.

* *Die* Rolle `admins` `ingestors`ist: oder .

* *Principal* ist ein oder mehrere Prinzipale. Informationen zum Angeben dieser Prinzipale finden Sie unter [Prinzipale und Identitätsanbieter.](./access-control/principals-and-identity-providers.md)

* `skip-results`, falls angegeben, fordert an, dass der Befehl die aktualisierte Liste der Tabellenprinzipale nicht zurückgibt.

* *Beschreibung*, falls angegeben, ist Text, der der Änderung `.show` zugeordnet und mit dem entsprechenden Befehl abgerufen wird.

**Beispiel**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-function-security-roles"></a>Verwalten von Funktionssicherheitsrollen

`.set``function` *FunctionName-Rolle* *Role* `none` [`skip-results`]

`.set``function` *FunktionName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

`.add``function` *FunktionName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

`.drop``function` *FunktionName* *Rollenprinzipal* `,` *Role* `(` [ *Principal*...] `)` [`skip-results`] [*Beschreibung*]

Der erste Befehl entfernt alle Prinzipale aus der Rolle. Die zweite entfernt alle Prinzipale aus der Rolle und legt einen neuen Satz von Prinzipalen fest. Der dritte fügt der Rolle neue Prinzipale hinzu, ohne vorhandene Prinzipale zu entfernen. Die letzte entfernt die angegebenen Prinzipale aus den Rollen und behält die anderen.

Hierbei gilt:

* *FunctionName* ist der Name der Funktion, deren Sicherheitsrolle geändert wird.

* *Rolle* ist `admin`immer .

* *Principal* ist ein oder mehrere Prinzipale. Informationen zum Angeben dieser Prinzipale finden Sie unter [Prinzipale und Identitätsanbieter.](./access-control/principals-and-identity-providers.md)

* `skip-results`, falls angegeben, fordert an, dass der Befehl die aktualisierte Liste der Funktionsprinzipale nicht zurückgibt.

* *Beschreibung*, falls angegeben, ist Text, der der Änderung `.show` zugeordnet und mit dem entsprechenden Befehl abgerufen wird.

**Beispiel**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

