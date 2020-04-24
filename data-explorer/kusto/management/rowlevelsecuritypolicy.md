---
title: Zeilenstufensicherheit (Vorschau) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Zeilenebenensicherheit (Vorschau) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 548b0e9e40cfd709b40e548fe7e0a8ad42aa4abc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520280"
---
# <a name="row-level-security-preview"></a>Zeilenstufensicherheit (Vorschau)

Verwenden Sie die Gruppenmitgliedschaft oder den Ausführungskontext, um den Zugriff auf Zeilen in einer Datenbanktabelle zu steuern.

Die Zeilenebenensicherheit (RLS) vereinfacht den Entwurf und die Codierung der Sicherheit in Ihrer Anwendung, indem Sie Einschränkungen für den Datenzeilenzugriff anwenden können. Beschränken Sie beispielsweise den Benutzerzugriff auf Zeilen, die für ihre Abteilung relevant sind, oder beschränken Sie den Kundenzugriff auf die für ihr Unternehmen relevanten Daten.

Die Zugriffseinschränkungslogik befindet sich in der Datenbankebene und nicht aneben von den Daten in einer anderen Anwendungsebene. Das Datenbanksystem wendet die Zugriffsbeschränkungen jedes Mal an, wenn der Datenzugriff von jeder Ebene aus versucht wird. Dadurch bietet Ihr Sicherheitssystem eine geringere Angriffsfläche und ist zuverlässiger und robuster.

Mit RLS können Sie zugriff auf andere Anwendungen und/oder Benutzer nur auf einen bestimmten Teil einer Tabelle zugreifen. Auf diese Weise können Sie beispielsweise folgende Vorgänge durchführen:

* Gewähren des Zugriffs nur auf Zeilen, die einige Kriterien erfüllen
* Anonymisieren von Daten in einigen Spalten
* Beide der oben genannten

> [!NOTE]
> Die Richtlinie für die Sicherheit auf Zeilenebene kann für eine Tabelle nicht aktiviert werden:
> * Für die [Der kontinuierliche Datenexport](../management/data-export/continuous-data-export.md) konfiguriert ist.
> * Darauf wird durch eine Abfrage einer [Aktualisierungsrichtlinie](./updatepolicy.md)verwiesen.
> * Für die [eingeschränkte Ansichtszugriffsrichtlinie](./restrictedviewaccesspolicy.md) konfiguriert ist.

Weitere Informationen zu den Steuerbefehlen zum Verwalten der Row Level Security-Richtlinie [finden Sie hier](../management/row-level-security-policy.md).

## <a name="examples"></a>Beispiele

In einer `Sales`Tabelle mit dem Namen enthält jede Zeile Details zu einem Verkauf. Eine der Spalten enthält den Namen der Verkäuferin.
Anstatt Ihren Vertriebsmitarbeitern Zugriff auf `Sales`alle Datensätze in zu gewähren, können Sie eine Richtlinie für die Sicherheit auf Zeilenebene für diese Tabelle aktivieren, um nur Datensätze zurückzugeben, bei denen die Vertriebsperson der aktuelle Benutzer ist:

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

Sie können auch die Kreditkartennummer maskieren:

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

Wenn jeder Vertriebsmitarbeiter alle Verkäufe eines bestimmten Landes anzeigen soll, können Sie eine Abfrage ähnlich der folgenden definieren:

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

Wenn Sie über eine AAD-Gruppe verfügen, die die Manager der Vertriebsmitarbeiter enthält, möchten Sie möglicherweise, dass diese Zugriff auf alle Zeilen haben. Dies kann durch die folgende Abfrage in der Richtlinie für die Sicherheit auf Zeilenebene erreicht werden:

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

Wenn Sie über mehrere AAD-Gruppen verfügen und die Mitglieder jeder Gruppe eine andere Teilmenge von Daten sehen sollen, können Sie dieser Struktur für eine RLS-Abfrage folgen (vorausgesetzt, ein Benutzer kann nur zu einer einzelnen AAD-Gruppe gehören):

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

## <a name="more-use-cases"></a>Mehr Anwendungsfälle

* Ein Callcenter-Support kann Anrufer anhand mehrerer Ziffern seiner Sozialversicherungsnummer oder Kreditkartennummer identifizieren. Diese Zahlen sollten der Unterstützenden Person nicht vollständig ausgesetzt werden. Eine RLS-Richtlinie kann auf die Tabelle angewendet werden, um alle bis auf die letzten vier Ziffern einer beliebigen Sozialversicherungs- oder Kreditkartennummer im Resultset einer Abfrage zu maskieren.
* Legen Sie eine RLS-Richtlinie fest, die personenbezogene Daten (PII) verschleiert und Entwicklern ermöglicht, Produktionsumgebungen zu Problembehandlungszwecken abzufragen, ohne gegen Compliance-Vorschriften zu verstoßen.
* Ein Krankenhaus kann eine RLS-Richtlinie festlegen, die es Pflegekräften ermöglicht, Datenzeilen nur für ihre Patienten anzuzeigen.
* Eine Bank kann eine RLS-Richtlinie festlegen, um den Zugriff auf Finanzdatenzeilen basierend auf der Geschäftsabteilung oder Rolle eines Mitarbeiters einzuschränken.
* Eine mehrinstanzenfähige Anwendung kann Daten von vielen Mandanten in einem einzelnen Tableset speichern (was sehr effizient ist). Sie würden eine RLS-Richtlinie verwenden, um eine logische Trennung der Datenzeilen jedes Mandanten von den Zeilen jedes anderen Mandanten zu erzwingen, sodass jeder Mandant nur seine Datenzeilen sehen kann.

## <a name="performance-impact-on-queries"></a>Auswirkungen auf die Leistung von Abfragen

Wenn eine RLS-Richtlinie für eine Tabelle aktiviert ist, wirkt sich dies auf Abfragen aus, die auf diese Tabelle zugreifen. Der Zugriff auf die Tabelle wird tatsächlich durch die RLS-Abfrage ersetzt, die in dieser Tabelle definiert ist. Die Auswirkungen einer RLS-Abfrage auf die Leistung bestehen normalerweise aus zwei Teilen:

* Mitgliedschaftsprüfungen in Azure Active Directory
* Die auf die Daten angewendeten Filter

Beispiel:

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

Wenn der Benutzer nicht some_group@domain.comTeil `IsRestrictedUser` von ist, wird er auf `false`ausgewertet, sodass die Abfrage, die ausgewertet wird, ähnlich wie diese ist:

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

Wenn `IsRestrictedUser` auch wertet `true`zu aus, `PartialData` wird nur die Abfrage für ausgewertet.

## <a name="performance-impact-on-ingestion"></a>Auswirkungen auf die Leistung auf die Aufnahme

Keine