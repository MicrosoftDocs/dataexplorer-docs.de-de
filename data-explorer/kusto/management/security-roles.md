---
title: 'Verwaltung von Sicherheitsrollen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Verwaltung von Sicherheitsrollen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4bda122b589e3ba297b3e7c350d15687da6ee123
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057001"
---
# <a name="security-roles-management"></a>Verwaltung von Sicherheitsrollen

> [!IMPORTANT]
> Lesen Sie vor dem Ändern der Autorisierungs Regeln für Ihren Kusto-Cluster die folgenden Informationen: [Übersicht über](../management/access-control/index.md)die  
>  [rollenbasierte Autorisierung](../management/access-control/role-based-authorization.md) mit der Kusto-Zugriffs Steuerung. 

In diesem Artikel werden die Steuerungsbefehle beschrieben, die zum Verwalten von Sicherheitsrollen verwendet werden.
Sicherheitsrollen definieren, welche Sicherheits Prinzipale (Benutzer und Anwendungen) über Berechtigungen für eine gesicherte Ressource verfügen, wie z. b. eine Datenbank oder eine Tabelle, und welche Vorgänge zulässig sind. Prinzipale, die über die `database viewer` Sicherheitsrolle für eine bestimmte Datenbank verfügen, können z. b. alle Entitäten der Datenbank Abfragen und anzeigen (mit Ausnahme von eingeschränkten Tabellen).

Die Sicherheitsrolle kann Sicherheits Prinzipale oder Sicherheitsgruppen zugeordnet werden (die andere Sicherheits Prinzipale oder andere Sicherheitsgruppen haben können). Wenn ein Sicherheits Prinzipal versucht, einen Vorgang für eine gesicherte Ressource auszuführen, prüft das System, ob der Prinzipal mindestens einer Sicherheitsrolle zugeordnet ist, die Berechtigungen zum Ausführen dieses Vorgangs für die Ressource erteilt. Dies wird als **Autorisierungs Überprüfung**bezeichnet. Wenn die Autorisierungs Überprüfung fehlschlägt, wird der Vorgang abgebrochen.

**Syntax**

Syntax der Befehle für die Verwaltung von Sicherheitsrollen:

*Verb* *Sichersecurableobjecttype* *securableobjectname* *Role* [ `(` *listOf Principals* `)` [*Description*]]

* *Verb* gibt die Art der auszuführenden Aktion an: `.show` , `.add` , `.drop` und `.set` .

    |*Verb* |BESCHREIBUNG                                  |
    |-------|---------------------------------------------|
    |`.show`|Gibt den aktuellen Wert oder die Werte zurück.         |
    |`.add` |Fügt der Rolle einen oder mehrere Prinzipale hinzu.     |
    |`.drop`|Entfernt einen oder mehrere Prinzipale aus der Rolle.|
    |`.set` |Legt die Rolle auf die spezifische Liste von Prinzipale fest, wobei alle vorherigen entfernt werden (sofern vorhanden).|

* *Securableobjecttype* ist die Art des Objekts, dessen Rolle angegeben wird.

    |*Securableobjecttype*|BESCHREIBUNG|
    |---------------------|-----------|
    |`database`|Die angegebene Datenbank.|
    |`table`|Die angegebene Tabelle|
    |`materialized-view`| Die angegebene [materialisierte Sicht](materialized-views/materialized-view-overview.md) .| 

* *Securableobjectname* ist der Name des Objekts.

* *Role* ist der Name der relevanten Rolle.

    |*Rolle*      |BESCHREIBUNG|
    |------------|-----------|
    |`principals`|Kann nur als Teil eines Verbs angezeigt werden `.show` . gibt die Liste der Prinzipale zurück, die das Sicherungs fähige Objekt beeinflussen können.|
    |`admins`    |Sie haben die Kontrolle über das Sicherungs fähige Objekt, einschließlich der Fähigkeit, es anzuzeigen, zu ändern und das-Objekt und alle untergeordneten Objekte zu entfernen.|
    |`users`     |Kann das Sicherungs fähige Objekt anzeigen und darunter neue Objekte erstellen.|
    |`viewers`   |Kann das Sicherungs fähige Objekt anzeigen.|
    |`unrestrictedviewers`|Nur auf Datenbankebene ermöglicht die Anzeige von eingeschränkten Tabellen (die nicht für "Normal" und nicht verfügbar sind `viewers` `users` ).|
    |`ingestors` |Nur auf Datenbankebene lassen Sie die Datenerfassung in alle Tabellen zu.|
    |`monitors`  ||

* *Listoberprincipals* ist eine optionale, durch Kommas getrennte Liste von Sicherheits Prinzipal bezeichnerwerten (Werte vom Typ `string` ).

* *Description* ist ein optionaler Wert des Typs, der `string` für zukünftige Überwachungszwecke zusammen mit der Zuordnung gespeichert wird.

## <a name="example"></a>Beispiel

Der folgende Steuerelement Befehl listet alle Sicherheits Prinzipale auf, die Zugriff auf die-Tabelle `StormEvents` in der-Datenbank haben:

```kusto
.show table StormEvents principals
```

Im folgenden finden Sie mögliche Ergebnisse dieses Befehls:

|Rolle |Principaltype |Principaldisplayname |Principalobjectid |Principalfqn 
|---|---|---|---|---
|Daten Bank apsty-Administrator |Azure AD Benutzer |Mark Smith |cd709aed-a26c-e3953dec735e |aaduser =msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>Verwalten von Daten Bank Sicherheitsrollen

`.set``database` *DatabaseName* - *Rolle* `none` [ `skip-results` ]

`.set``database` *DatabaseName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

`.add``database` *DatabaseName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

`.drop``database` *DatabaseName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

Mit dem ersten Befehl werden alle Prinzipale aus der Rolle entfernt. Die zweite entfernt alle Prinzipale aus der Rolle und legt einen neuen Satz von Prinzipale fest. Das dritte fügt der Rolle neue Prinzipale hinzu, ohne vorhandene Prinzipale zu entfernen. Der letzte entfernt die aufgeführten Prinzipale aus den Rollen und behält die anderen bei.

Hierbei gilt:

* *DatabaseName* ist der Name der Datenbank, deren Sicherheitsrolle geändert wird.

* *Rolle* : `admins` , `ingestors` , `monitors` , `unrestrictedviewers` , `users` oder `viewers` .

* *Prinzipal* ist ein oder mehrere Prinzipale. Informationen zur Angabe dieser Prinzipale finden Sie unter [Prinzipale und Identitäts Anbieter](./access-control/principals-and-identity-providers.md) .

* `skip-results`Wenn angegeben, wird von angefordert, dass der Befehl die aktualisierte Liste der Daten Bank Prinzipale nicht zurückgibt.

* Die *Beschreibung*ist, falls vorhanden, Text, der der Änderung zugeordnet und durch den entsprechenden Befehl abgerufen wird `.show` .

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>Verwalten von Tabellen Sicherheitsrollen

`.set``table` *TableName* - *Rolle* `none` [ `skip-results` ]

`.set``table` *TableName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

`.add``table` *TableName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

`.drop``table` *TableName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

Mit dem ersten Befehl werden alle Prinzipale aus der Rolle entfernt. Die zweite entfernt alle Prinzipale aus der Rolle und legt einen neuen Satz von Prinzipale fest. Das dritte fügt der Rolle neue Prinzipale hinzu, ohne vorhandene Prinzipale zu entfernen. Der letzte entfernt die aufgeführten Prinzipale aus den Rollen und behält die anderen bei.

Hierbei gilt:

* *TableName* ist der Name der Tabelle, deren Sicherheitsrolle geändert wird.

* Die *Rolle* ist: `admins` oder `ingestors` .

* *Prinzipal* ist ein oder mehrere Prinzipale. Informationen zur Angabe dieser Prinzipale finden Sie unter [Prinzipale und Identitäts Anbieter](./access-control/principals-and-identity-providers.md) .

* `skip-results`Wenn angegeben, wird von angefordert, dass der Befehl die aktualisierte Liste der Tabellen Prinzipale nicht zurückgibt.

* Die *Beschreibung*ist, falls vorhanden, Text, der der Änderung zugeordnet und durch den entsprechenden Befehl abgerufen wird `.show` .

**Beispiel**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-materialized-view-security-roles"></a>Verwalten von Sicherheitsrollen für materialisierte Sichten

`.show``materialized-view` *Materializedviewname*`principals`

`.set``materialized-view` *Materializedviewname* `admins` `(` *Prinzipal* `,[` *Prinzipal...*`])`

`.add``materialized-view` *Materializedviewname* `admins` `(` *Prinzipal* `,[` *Prinzipal...*`])`

`.drop``materialized-view` *Materializedviewname* `admins` `(` *Prinzipal* `,[` *Prinzipal...*`])`

Hierbei gilt:

* *Materializedviewname* ist der Name der materialisierten Sicht, deren Sicherheitsrolle geändert wird.
* *Prinzipal* ist ein oder mehrere Prinzipale. Weitere Informationen finden Sie unter [Prinzipale und Identitäts Anbieter](./access-control/principals-and-identity-providers.md)

## <a name="managing-function-security-roles"></a>Verwalten von Funktions Sicherheitsrollen

`.set``function` *FunctionName* - *Rolle* `none` [ `skip-results` ]

`.set``function` *FunctionName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

`.add``function` *FunctionName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

`.drop``function` *FunctionName* - *Rollen* `(` *Prinzipal* [ `,` *Principal*...] `)` [ `skip-results` ] [*Beschreibung*]

Mit dem ersten Befehl werden alle Prinzipale aus der Rolle entfernt. Die zweite entfernt alle Prinzipale aus der Rolle und legt einen neuen Satz von Prinzipale fest. Das dritte fügt der Rolle neue Prinzipale hinzu, ohne vorhandene Prinzipale zu entfernen. Der letzte entfernt die aufgeführten Prinzipale aus den Rollen und behält die anderen bei.

Hierbei gilt:

* *FunctionName* ist der Name der Funktion, deren Sicherheitsrolle geändert wird.

* Die *Rolle* ist immer `admin` .

* *Prinzipal* ist ein oder mehrere Prinzipale. Informationen zur Angabe dieser Prinzipale finden Sie unter [Prinzipale und Identitäts Anbieter](./access-control/principals-and-identity-providers.md) .

* `skip-results`Wenn bereitgestellt, wird angefordert, dass der Befehl die aktualisierte Liste der Funktions Prinzipale nicht zurückgibt.

* Die *Beschreibung*ist, falls vorhanden, Text, der der Änderung zugeordnet und durch den entsprechenden Befehl abgerufen wird `.show` .

**Beispiel**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

