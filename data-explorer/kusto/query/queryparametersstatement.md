---
title: Abfrage Parameter Deklarations Anweisung-Azure Daten-Explorer | Microsoft-Dokumentation
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
ms.openlocfilehash: a76755d04179b3d311e79798162c61db764455d7
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737809"
---
# <a name="query-parameters-declaration-statement"></a>Deklarations Anweisung der Abfrage Parameter

::: zone pivot="azuredataexplorer"

An Kusto gesendete Abfragen können zusätzlich zum Abfragetext selbst einen Satz von Name-Wert-Paaren enthalten, die als **Abfrage Parameter**bezeichnet werden. Der Abfragetext kann dann auf einen oder mehrere Abfrage Parameterwerte verweisen, indem er die Namen und den Typ über eine **Deklarations Anweisung der Abfrage Parameter**angibt.

Abfrage Parameter haben zwei Haupt Verwendungsmöglichkeiten:

1. Als Schutzmechanismus gegen **Injection-Angriffe**.
2. Als Möglichkeit zum Parametrisieren von Abfragen.

Insbesondere-Client Anwendungen, die eine vom Benutzer bereitgestellte Eingabe in Abfragen kombinieren, die Sie dann an Kusto senden, sollten diesen Mechanismus verwenden, um die Kusto-Entsprechung von [SQL-Injection](https://en.wikipedia.org/wiki/SQL_injection) -Angriffen zu schützen.

## <a name="declaring-query-parameters"></a>Deklarieren von Abfrage Parametern

Um auf Abfrage Parameter verweisen zu können, muss der Abfragetext (oder die darin verwendeten Funktionen) zuerst den verwendeten Abfrage Parameter deklarieren. Für jeden Parameter stellt die Deklaration den Namen und den skalaren Typ bereit. Optional kann der-Parameter auch einen Standardwert aufweisen, der verwendet wird, wenn die Anforderung keinen konkreten Wert für den Parameter bereitstellt. Anschließend analysiert Kusto den Wert des Abfrage Parameters gemäß seinen normalen Analyse Regeln für diesen Typ.

**Syntax**

`declare``query_parameters` `:` *Type1* `,` *Name1* `=` Name1 Typ1 [DefaultValue1] [...] *DefaultValue1* `(``);`

* *Name1*: der Name eines in der Abfrage verwendeten Abfrage Parameters.
* *Typ1*: der entsprechende Typ (z. b `string`. `datetime`, usw.) Die vom Benutzer bereitgestellten Werte werden als Zeichen folgen codiert, damit Kusto die entsprechende Analysemethode auf den Abfrage Parameter anwendet, um einen stark typisierten Wert zu erhalten.
* *DefaultValue1*: ein optionaler Standardwert für den-Parameter. Dabei muss es sich um einen literalen des entsprechenden skalartyps handeln.

> [!NOTE]
> Wie [benutzerdefinierte Funktionen](functions/user-defined-functions.md)können Abfrage Parameter vom Typ `dynamic` keine Standardwerte aufweisen.

**Beispiele**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>Angeben von Abfrage Parametern in einer Client Anwendung

Die Namen und Werte von Abfrage Parametern werden von der `string` Anwendung, die die Abfrage vornimmt, als Werte bereitgestellt. Es kann kein Name wiederholt werden.

Die Interpretation der Werte erfolgt gemäß der Deklarations Anweisung der Abfrage Parameter. Jeder Wert wird so analysiert, als ob es sich um einen Literalwert im Text einer Abfrage handelt, der dem Typ entspricht, der von der Deklarations Anweisung der Abfrage Parameter angegeben wird.

### <a name="rest-api"></a>REST-API

Abfrage Parameter werden von Client Anwendungen über den `properties` Slot des JSON-Objekts des Anforderungs Texts in einem geschiesteten Eigenschaften Behälter namens `Parameters`bereitgestellt. Hier ist beispielsweise der Text eines REST-API-Aufrufes für Kusto, der das Alter einiger Benutzer berechnet (vermutlich, wenn die Anwendung den Benutzer zum Geburtstag auffordert):

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Um die Namen und Werte von Abfrage Parametern bei Verwendung der Kusto .NET-Client Bibliothek bereitzustellen, wird eine neue Instanz des `ClientRequestProperties` -Objekts erstellt und dann `HasParameter`die `SetParameter`-Methode `ClearParameter` , die-Methode und die-Methode verwendet, um Abfrage Parameter zu bearbeiten. Beachten Sie, dass diese Klasse eine Reihe stark typisierter über Ladungen für `SetParameter`bereitstellt. intern generieren Sie das entsprechende Literale der Abfragesprache und senden Sie als `string` über die Rest-API, wie oben beschrieben. Der Abfragetext selbst muss weiterhin [die Abfrage Parameter deklarieren](#declaring-query-parameters).

### <a name="kustoexplorer"></a>Kusto.Explorer

Um die Abfrage Parameter festzulegen, die beim Senden einer Anforderung an den Dienst gesendet werden, verwenden Sie das Symbol "Schlüssel`ALT` + `P`Symbol" der **Abfrage Parameter** ().

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
