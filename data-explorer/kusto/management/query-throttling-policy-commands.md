---
title: 'Befehle für die Abfrage Drosselung-Richtlinie: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Befehle für die Abfrage Drosselung-Richtlinie in Azure Daten-Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 85408aadfd15fc48f1e3e86ab01afce5dca15bce
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734001"
---
# <a name="query-throttling-policy-commands"></a>Befehle der Abfrage Drosselung-Richtlinie

Die [Abfrage](query-throttling-policy.md) Einschränkungs Richtlinie ist eine Richtlinie auf Cluster Ebene, um die Abfrage Parallelität im Cluster einzuschränken. Für diese Abfrage Drosselungs Richtlinien Befehle sind [alldatabasesadmin](../management/access-control/role-based-authorization.md) -Berechtigungen erforderlich.

## <a name="query-throttling-policy-object"></a>Richtlinien Objekt für die Abfrage Drosselung

Für einen Cluster können keine oder nur eine Abfrage einschränelung definiert werden.

Wenn für einen Cluster keine Richtlinie für die Abfrage Drosselung definiert ist, wird die Standard Richtlinie angewendet. Weitere Informationen zur Standard Richtlinie finden Sie unter [Abfrage Limits](../concepts/querylimits.md).

|Eigenschaft  |type    |BESCHREIBUNG                                                       |
|----------|--------|------------------------------------------------------------------|
|IsEnabled |`bool`  |Gibt an, ob die Richtlinie für die Abfrage Drosselung aktiviert (true) oder deaktiviert (false) ist.     |
|Maxmenge|`int`|Anzahl der gleichzeitigen Abfragen, die der Cluster ausführen kann. Die Zahl muss einen positiven Wert aufweisen. |

## `.show cluster policy querythrottling`

Gibt die [Richtlinie für die Abfrage Drosselung](query-throttling-policy.md) des Clusters zurück.

### <a name="syntax"></a>Syntax

`.show` `cluster` `policy` `querythrottling`

### <a name="returns"></a>Gibt zurück

Gibt eine Tabelle mit den folgenden Spalten zurück:

|Spalte    |Typ    |BESCHREIBUNG
|---|---|---
|PolicyName| Zeichenfolge |Der Richtlinien Name-querythrottlingpolicy
|EntityName| string |Leer
|Richtlinie    | Zeichenfolge |Ein JSON-Objekt, das die Richtlinie zur Abfrage Drosselung definiert, die als [Richtlinien Objekt für die Abfrage Drosselung](#query-throttling-policy-object) formatiert ist.

### <a name="example"></a>Beispiel

<!-- csl -->
```
.show cluster policy querythrottling 
```

Rückgabewerte:

|PolicyName|EntityName|Richtlinie|Childentities|EntityType|
|---|---|---|---|---|
|Querythrottlingpolicy||{"Isaktivierte": true, "maxmenge": 25}

## `.alter cluster policy querythrottling`

Legt die [Richtlinie für die Abfrage Drosselung](query-throttling-policy.md) der angegebenen Tabelle fest. 

### <a name="syntax"></a>Syntax

`.alter``cluster` `policy` `querythrottling` *Querythrottlingpolicyobject*

### <a name="arguments"></a>Argumente

*Querythrottlingpolicyobject* ist ein JSON-Objekt, für das ein Richtlinien Objekt für die Abfrage Drosselung definiert ist.

### <a name="returns"></a>Gibt zurück

Legt das Richtlinien Objekt für die Cluster Abfrage Einschränkung fest, wobei jede definierte aktuelle Richtlinie außer Kraft gesetzt wird. Anschließend wird die Ausgabe des entsprechenden Befehls " [. Show Cluster Policy `querythrottling` ](#show-cluster-policy-querythrottling) " zurückgegeben.

### <a name="example"></a>Beispiel

<!-- csl -->
```
.alter cluster policy querythrottling '{"IsEnabled": true, "MaxQuantity": 25}'
```

|PolicyName|EntityName|Richtlinie|Childentities|EntityType|
|---|---|---|---|---|
|Querythrottlingpolicy||{"Isaktivierte": true, "maxmenge": 25}

## `.delete cluster policy querythrottling`

Löscht das Richtlinien Objekt für die Cluster [Abfrage Drosselung](query-throttling-policy.md) .

### <a name="syntax"></a>Syntax

`.delete` `cluster` `policy` `querythrottling`
