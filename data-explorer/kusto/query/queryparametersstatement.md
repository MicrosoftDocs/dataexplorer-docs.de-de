---
title: Abfrage Parameter Deklarations Anweisung-Azure Daten-Explorer
description: Dieser Artikel beschreibt die Deklarations Anweisung der Abfrage Parameter in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 54c09908096f9df4ac8b568cd5e897c6e4ecc8c2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345962"
---
# <a name="query-parameters-declaration-statement"></a>Deklarations Anweisung der Abfrage Parameter

::: zone pivot="azuredataexplorer"

An Kusto gesendete Abfragen können einen Satz von Name-Wert-Paaren enthalten. Die Paare werden als *Abfrage Parameter*bezeichnet, sowie der Abfragetext selbst. Die Abfrage kann in einer *Abfrage Parameter Deklarations Anweisung*auf einen oder mehrere Werte verweisen, indem Sie Namen und den Typ angibt.

Abfrage Parameter haben zwei Haupt Verwendungsmöglichkeiten:

* Als Schutzmechanismus gegen Injection-Angriffe.
* Als Möglichkeit zum Parametrisieren von Abfragen.

Insbesondere-Client Anwendungen, die eine vom Benutzer bereitgestellte Eingabe in Abfragen kombinieren, die Sie dann an Kusto senden, sollten den-Mechanismus verwenden, um die Kusto-Entsprechung von [SQL-Injection](https://en.wikipedia.org/wiki/SQL_injection) -Angriffen zu schützen.

## <a name="declaring-query-parameters"></a>Deklarieren von Abfrage Parametern

Um auf Abfrage Parameter zu verweisen, muss der Abfragetext bzw. die darin verwendeten Funktionen zuerst den verwendeten Abfrage Parameter deklarieren. Für jeden Parameter stellt die Deklaration den Namen und den skalaren Typ bereit. Optional kann der-Parameter auch über einen Standardwert verfügen. Der Standardwert wird verwendet, wenn die Anforderung keinen konkreten Wert für den Parameter bereitstellt. Anschließend analysiert Kusto den Wert des Abfrage Parameters entsprechend der normalen Analyse Regeln für diesen Typ.

## <a name="syntax"></a>Syntax

`declare``query_parameters` `(` *Name1* `:` *Typ1* [ `=` *DefaultValue1*] [ `,` ...]`);`

* *Name1*: der Name eines in der Abfrage verwendeten Abfrage Parameters.
* *Typ1*: der entsprechende Typ, z `string` . b `datetime` . oder.
  Die vom Benutzer bereitgestellten Werte werden als Zeichen folgen codiert, damit Kusto die entsprechende Analysemethode auf den Abfrage Parameter anwendet, um einen stark typisierten Wert zu erhalten.
* *DefaultValue1*: ein optionaler Standardwert für den-Parameter. Dieser Wert muss ein Literalwert des entsprechenden skalaren Typs sein.

> [!NOTE]
> Wie [benutzerdefinierte Funktionen](functions/user-defined-functions.md)können Abfrage Parameter vom Typ `dynamic` keine Standardwerte aufweisen.

## <a name="examples"></a>Beispiele

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>Angeben von Abfrage Parametern in einer Client Anwendung

Die Namen und Werte von Abfrage Parametern werden `string` von der Anwendung, die die Abfrage vornimmt, als Werte bereitgestellt. Es kann kein Name wiederholt werden.

Die Interpretation der Werte erfolgt gemäß der Deklarations Anweisung der Abfrage Parameter. Jeder Wert wird so analysiert, als ob es sich um einen Literalwert im Text einer Abfrage handelt. Die Verarbeitung erfolgt entsprechend dem durch die Abfrage Parameter Deklarations Anweisung angegebenen Typ.

### <a name="rest-api"></a>REST-API

Abfrage Parameter werden von Client Anwendungen über den `properties` Slot des JSON-Objekts des Anforderungs Texts in einem geschiesteten Eigenschaften Behälter namens bereitgestellt `Parameters` . Hier ist beispielsweise der Text eines REST-API-Aufrufes für Kusto, der das Alter einiger Benutzer berechnet, vermutlich indem die Anwendung den Geburtstag des Benutzers anfordert.

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Um die Namen und Werte von Abfrage Parametern bei Verwendung der Kusto .NET-Client Bibliothek bereitzustellen, wird eine neue Instanz des `ClientRequestProperties` -Objekts erstellt und dann `HasParameter` die `SetParameter` -Methode, die-Methode und die-Methode verwendet, `ClearParameter` um Abfrage Parameter zu bearbeiten. Diese Klasse stellt eine Reihe stark typisierter über Ladungen für bereit `SetParameter` . intern generieren Sie das entsprechende Literale der Abfragesprache und senden Sie als `string` über die Rest-API, wie oben beschrieben. Der Abfragetext selbst muss immer noch [die Abfrage Parameter deklarieren](#declaring-query-parameters).

### <a name="kustoexplorer"></a>Kusto.Explorer

Um die Abfrage Parameter festzulegen, die beim Senden einer Anforderung an den Dienst gesendet werden, verwenden Sie das Symbol "Schlüsselsymbol" der **Abfrage Parameter** ( `ALT`  +  `P` ).

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
