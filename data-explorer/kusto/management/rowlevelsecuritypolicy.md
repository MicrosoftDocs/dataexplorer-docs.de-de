---
title: 'Sicherheit auf Zeilenebene (Vorschau): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird Sicherheit auf Zeilenebene (Vorschauversion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 2d535e47f5b05c1c45ef2cf1993681aa8aa2133d
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863301"
---
# <a name="row-level-security-preview"></a>Sicherheit auf Zeilenebene (Vorschau)

Verwenden Sie die Gruppenmitgliedschaft oder den Ausführungs Kontext, um den Zugriff auf Zeilen in einer Datenbanktabelle zu steuern.

Sicherheit auf Zeilenebene (RLS) vereinfacht das Entwerfen und Programmieren der Sicherheit in Ihrer Anwendung, da Sie Einschränkungen für den Daten Zeilen Zugriff anwenden lassen. Beschränken Sie z. b. den Benutzer Zugriff auf die für Ihre Abteilung relevanten Zeilen, oder schränken Sie den Zugriff auf die für das Unternehmen relevanten Daten ein.

Die Zugriffs Einschränkungs Logik befindet sich auf der Datenbankebene und nicht auf der Ebene der Daten auf einer anderen Anwendungsebene. Das Datenbanksystem wendet die Zugriffsbeschränkungen bei jedem Versuch eines Datenzugriffs auf eine beliebige Ebene an. Dadurch bietet Ihr Sicherheitssystem eine geringere Angriffsfläche und ist zuverlässiger und robuster.

Mit RLS können Sie den Zugriff auf andere Anwendungen und/oder Benutzer nur auf einen bestimmten Teil einer Tabelle gewähren. Auf diese Weise können Sie beispielsweise folgende Vorgänge durchführen:

* Gewähren von Zugriff nur für Zeilen, die bestimmte Kriterien erfüllen
* Anonymisieren von Daten in einigen Spalten
* Beide oben genannten

Weitere Informationen finden Sie unter [Steuern von Befehlen zum Verwalten der Sicherheit auf Zeilenebene-Richtlinie](../management/row-level-security-policy.md).

> [!Note]
> Die RLS-Richtlinie, die Sie für die Produktionsdatenbank konfigurieren, wird auch in den Datenbanken der Follower wirksam. Sie können für die Produktions-und die Follower-Datenbank keine unterschiedlichen RLS-Richtlinien konfigurieren

## <a name="limitations"></a>Einschränkungen

Es gibt keine Beschränkung für die Anzahl der Tabellen, für die Sicherheit auf Zeilenebene Richtlinie konfiguriert werden kann.

Die RLS-Richtlinie kann für eine Tabelle nicht aktiviert werden:
* , Für den [Continuous Data-Export](../management/data-export/continuous-data-export.md) konfiguriert ist.
* Auf die durch eine Abfrage einer [Update Richtlinie](./updatepolicy.md)verwiesen wird.
* Für welche [Eingeschränkte Ansichts Zugriffs Richtlinie](./restrictedviewaccesspolicy.md) konfiguriert ist.

## <a name="examples"></a>Beispiele

### <a name="limiting-access-to-sales-table"></a>Einschränken des Zugriffs auf die Sales-Tabelle

In einer Tabelle mit dem Namen `Sales` enthält jede Zeile Details zu einem Verkauf. Eine der Spalten enthält den Namen des Vertriebsmitarbeiters. Anstatt den Vertriebsmitarbeitern den Zugriff auf alle Datensätze in zu gewähren `Sales` , können Sie eine Sicherheit auf Zeilenebene Richtlinie in dieser Tabelle aktivieren, damit nur Datensätze zurückgegeben werden, bei denen der Vertriebsmitarbeiter der aktuelle Benutzer ist:

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

Sie können auch die Kreditkartennummer maskieren:

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

Wenn jeder Vertriebsmitarbeiter alle Verkäufe eines bestimmten Lands anzeigen soll, können Sie eine Abfrage wie die folgende definieren:

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

Wenn Sie über eine Aad-Gruppe verfügen, die die Vorgesetzten der Vertriebsmitarbeiter enthält, sollten Sie möglicherweise auf alle Zeilen zugreifen. Dies kann durch die folgende Abfrage in der Sicherheit auf Zeilenebene Richtlinie erreicht werden:

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

### <a name="exposing-different-data-to-members-of-different-aad-groups"></a>Verfügbar machen verschiedener Daten für Mitglieder von verschiedenen Aad-Gruppen

Wenn Sie über mehrere Aad-Gruppen verfügen und möchten, dass die Mitglieder jeder Gruppe eine andere Teilmenge der Daten sehen, können Sie dieser Struktur für eine RLS-Abfrage folgen (vorausgesetzt, dass ein Benutzer nur zu einer einzigen Aad-Gruppe gehören kann):

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

### <a name="applying-the-same-rls-function-on-multiple-tables"></a>Anwenden derselben RLS-Funktion auf mehrere Tabellen

Definieren Sie zunächst eine Funktion, die den Tabellennamen als Zeichen folgen Parameter empfängt und mithilfe des-Operators auf die Tabelle verweist `table()` . Beispiel:

```
.create-or-alter function RLSForCustomersTables(TableName: string) {
    table(TableName)
    | ...
}
```

Konfigurieren Sie anschließend RLS auf folgende Weise für mehrere Tabellen:

```
.alter table Customers1 policy row_level_security enable "RLSForCustomersTables('Customers1')"
.alter table Customers2 policy row_level_security enable "RLSForCustomersTables('Customers2')"
.alter table Customers3 policy row_level_security enable "RLSForCustomersTables('Customers3')"
```

## <a name="more-use-cases"></a>Weitere Anwendungsfälle

* Ein Supportmitarbeiter von Callcenter kann Anrufer anhand mehrerer Ziffern seiner Sozialversicherungsnummer oder Kreditkartennummer identifizieren. Diese Zahlen sollten dem Supportmitarbeiter nicht vollständig verfügbar gemacht werden. Eine RLS-Richtlinie kann auf die Tabelle angewendet werden, um alle bis auf die letzten vier Ziffern einer Sozialversicherungsnummer oder Kreditkartennummer im Resultset einer beliebigen Abfrage zu maskieren.
* Legen Sie eine RLS-Richtlinie fest, die personenbezogene Informationen (PII) maskiert. Dadurch können Entwickler Produktionsumgebungen zu Problem Behandlungszwecken Abfragen, ohne gegen Konformitäts Bestimmungen verstoßen zu müssen.
* Ein Krankenhaus kann eine RLS-Richtlinie festlegen, mit der Krankenschwestern nur Daten Zeilen für Ihre Patienten anzeigen können.
* Eine Bank kann eine RLS-Richtlinie festlegen, um den Zugriff auf Finanzdaten Zeilen basierend auf der Geschäftsabteilung oder-Rolle eines Mitarbeiters einzuschränken.
* Eine mehr Instanzen fähige Anwendung kann Daten aus vielen Mandanten in einem einzelnen tableset speichern (was sehr effizient ist). Sie verwenden eine RLS-Richtlinie, um eine logische Trennung der Daten Zeilen jedes Mandanten aus den Zeilen jedes anderen Mandanten zu erzwingen, sodass jeder Mandant nur seine Daten Zeilen sehen kann.

## <a name="performance-impact-on-queries"></a>Auswirkungen auf die Leistung von Abfragen

Wenn eine RLS-Richtlinie für eine Tabelle aktiviert ist, wirkt sich dies auf die Leistung von Abfragen aus, die auf diese Tabelle zugreifen. Der Zugriff auf die Tabelle wird tatsächlich durch die RLS-Abfrage ersetzt, die für diese Tabelle definiert ist. Die Auswirkungen einer RLS-Abfrage auf die Leistung bestehen normalerweise aus zwei Teilen:

* Mitgliedschafts Prüfungen in Azure Active Directory
* Die auf die Daten angewendeten Filter.

Beispiel:

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

Wenn der Benutzer nicht Teil von ist some_group@domain.com , `IsRestrictedUser` wird ausgewertet `false` , sodass die auszuwertende Abfrage in etwa wie folgt ausgewertet wird:

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

Entsprechend `IsRestrictedUser` `true` wird nur die Abfrage für ausgewertet, wenn als `PartialData` ausgewertet wird.

### <a name="improve-query-performance-when-rls-is-used"></a>Verbessern der Abfrageleistung bei Verwendung von RLS

* Wenn ein Filter auf eine Spalte mit hoher Kardinalität (z. b. DeviceID) angewendet wird, können Sie die [Partitionierungs Richtlinie](./partitioningpolicy.md) oder [Richtlinie für die Zeilen Reihenfolge](./roworderpolicy.md) verwenden.
* Wenn ein Filter auf eine Spalte mit niedriger mittlere Kardinalität angewendet wird – sollten Sie ggf. die [Richtlinie zur Zeilen Reihenfolge](./roworderpolicy.md) verwenden.

## <a name="performance-impact-on-ingestion"></a>Leistungseinbußen bei der Erfassung

Bei der Erfassung gibt es keine Auswirkungen auf die Leistung.
