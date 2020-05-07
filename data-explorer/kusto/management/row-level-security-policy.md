---
title: 'Rowlevelsecurity-Richtlinie (Vorschau): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die rowlevelsecurity-Richtlinie (Vorschauversion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: f8f6f820090bde91b9ed6479e0677a893a682983
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617373"
---
# <a name="rowlevelsecurity-policy-preview"></a>Rowlevelsecurity-Richtlinie (Vorschau)

In diesem Artikel werden Befehle beschrieben, die zum Konfigurieren einer [row_level_security Richtlinie](rowlevelsecuritypolicy.md) für Datenbanktabellen verwendet werden.

## <a name="displaying-the-policy"></a>Anzeigen der Richtlinie

Verwenden Sie den folgenden Befehl, um die Richtlinie anzuzeigen:

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>Konfigurieren der Richtlinie

Aktivieren Sie row_level_security Richtlinie für eine Tabelle:

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

Deaktivieren Sie row_level_security Richtlinie für eine Tabelle:

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

Auch wenn die Richtlinie deaktiviert ist, können Sie erzwingen, dass Sie auf eine einzelne Abfrage angewendet wird. Geben Sie vor der Abfrage die folgende Zeile ein:

`set query_force_row_level_security;`

Dies ist nützlich, wenn Sie verschiedene Abfragen für row_level_security ausprobieren möchten, aber nicht möchten, dass es für Benutzer tatsächlich wirksam wird. Wenn Sie in der Abfrage sicher sind, aktivieren Sie die Richtlinie.

> [!NOTE]
> Die folgenden Einschränkungen gelten für `query`:
>
> * Die Abfrage sollte genau das gleiche Schema wie die Tabelle ergeben, in der die Richtlinie definiert ist. Das heißt, das Ergebnis der Abfrage sollte genau die gleichen Spalten wie die ursprüngliche Tabelle in derselben Reihenfolge mit denselben Namen und Typen zurückgeben.
> * In der Abfrage können nur die folgenden Operatoren `extend`verwendet `where`werden `project`: `project-away`, `project-rename`, `project-reorder` , `union`, und.
> * Die Abfrage kann nicht auf Tabellen verweisen, die nicht auf die Tabellen verweisen, für die die Richtlinie definiert ist.
> * Bei der Abfrage kann es sich um eine der folgenden oder um eine Kombination der folgenden Elemente handeln:
>    * Abfrage (z. b `<table_name> | extend CreditCardNumber = "****"`.)
>    * Function (z. b `AnonymizeSensitiveData`.)
>    * Datdatababel (z. `datatable(Col1:datetime, Col2:string) [...]`b.)

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

**Leistungs Hinweis**: `UserCanSeeFullNumbers` wird zuerst ausgewertet, und dann wird entweder `AllData` oder `PartialData` ausgewertet, aber nicht beides, was das erwartete Ergebnis ist.
Weitere Informationen zu den Auswirkungen von RLS auf die Leistung finden Sie [hier](rowlevelsecuritypolicy.md#performance-impact-on-queries).

## <a name="deleting-the-policy"></a>Löschen der Richtlinie

```kusto
.delete table <table_name> policy row_level_security
```