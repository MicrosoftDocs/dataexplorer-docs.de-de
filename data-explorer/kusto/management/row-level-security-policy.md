---
title: RowLevelSecurity-Richtlinie (Vorschau) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt RowLevelSecurity-Richtlinie (Vorschau) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: bf8ee8bc4c43c4ed3bcd320ce5b4778f33c3cc64
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520297"
---
# <a name="rowlevelsecurity-policy-preview"></a>RowLevelSecurity-Richtlinie (Vorschau)

In diesem Artikel werden Befehle beschrieben, die zum Konfigurieren einer [row_level_security Richtlinie](rowlevelsecuritypolicy.md) für Datenbanktabellen verwendet werden.

## <a name="displaying-the-policy"></a>Anzeigen der Richtlinie

Um die Richtlinie anzuzeigen, verwenden Sie den folgenden Befehl:

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>Konfigurieren der Richtlinie

Aktivieren row_level_security Richtlinie für eine Tabelle:

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

Deaktivieren row_level_security Richtlinie für eine Tabelle:

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

Selbst wenn die Richtlinie deaktiviert ist, können Sie erzwingen, dass sie auf eine einzelne Abfrage angewendet wird. Geben Sie die folgende Zeile vor der Abfrage ein:

`set query_force_row_level_security;`

Dies ist nützlich, wenn Sie verschiedene Abfragen für row_level_security ausprobieren möchten, aber nicht möchten, dass sie tatsächlich auf Benutzer wirksam werden. Sobald Sie sich in die Abfrage verlassen, aktivieren Sie die Richtlinie.

> [!NOTE]
> Die folgenden Einschränkungen `query`gelten für:
>
> * Die Abfrage sollte genau das gleiche Schema wie die Tabelle erzeugen, für die die Richtlinie definiert ist. Das heißt, das Ergebnis der Abfrage sollte genau die gleichen Spalten wie die ursprüngliche Tabelle in der gleichen Reihenfolge mit den gleichen Namen und Typen zurückgeben.
> * Die Abfrage kann nur die `extend` `where`folgenden `project` `project-away`Operatoren `project-reorder` `union`verwenden: , , , , `project-rename`, und .
> * Die Abfrage kann nicht auf andere Tabellen als die, für die die Richtlinie definiert ist, verweisen.
> * Die Abfrage kann eine der folgenden sein, oder eine Kombination von ihnen:
>    * Abfrage (z. `<table_name> | extend CreditCardNumber = "****"`B. )
>    * Funktion (z. `AnonymizeSensitiveData`B. )
>    * Datentabelle (z. `datatable(Col1:datetime, Col2:string) [...]`B. )

> [!TIP]
> Diese Funktionen sind häufig für row_level_security Abfragen nützlich:
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

**Beispiel**

```kusto
.create-or-alter function with () TrimCreditCardNumbers() {
    let UserCanSeeFullNumbers = current_principal_is_member_of('aadgroup=super_group@domain.com');
    let AllData = Customers | where UserCanSeeFullNumbers;
    let PartialData = Customers | where not(UserCanSeeFullNumbers) | extend CreditCardNumber = "****";
    union AllData, PartialData
}

.alter table Customers policy row_level_security enable "TrimCreditCardNumbers"
```
**Leistungshinweis** `UserCanSeeFullNumbers` : wird zuerst bewertet, `AllData` `PartialData` und dann entweder oder wird ausgewertet werden, aber nicht beides, was das erwartete Ergebnis ist.
Weitere Informationen zu den Auswirkungen von RLS auf die Leistung von RLS finden Sie [hier](rowlevelsecuritypolicy.md#performance-impact-on-queries).

## <a name="deleting-the-policy"></a>Löschen der Richtlinie

```kusto
.delete table <table_name> policy row_level_security
```