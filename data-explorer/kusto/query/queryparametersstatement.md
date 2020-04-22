---
title: Anweisung für Abfrageparameterdeklaration - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Deklarationsanweisung der Abfrageparameter in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b7193ada6967882306d9a977b6c90af8b247036d
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765497"
---
# <a name="query-parameters-declaration-statement"></a>Anweisungsanweisung für Abfrageparameter

::: zone pivot="azuredataexplorer"

Abfragen, die an Kusto gesendet werden, können neben dem Abfragetext selbst einen Satz von Name/Wert-Paaren enthalten, die **als Abfrageparameter**bezeichnet werden. Der Abfragetext kann dann auf einen oder mehrere Abfrageparameterwerte verweisen, indem er deren Namen und Typ über eine **Anweisungsanweisung**für Abfrageparameter angibt.

Abfrageparameter haben zwei Hauptverwendungen:

1. Als Schutzmechanismus gegen **Injektionsangriffe**.
2. Als Möglichkeit zum Parametrierung von Abfragen.

Insbesondere Clientanwendungen, die vom Benutzer bereitgestellte Eingaben in Abfragen kombinieren, die sie dann an Kusto senden, sollten diesen Mechanismus verwenden, um sich vor dem Kusto-Äquivalent von [SQL-Injection-Angriffen](https://en.wikipedia.org/wiki/SQL_injection) zu schützen.

## <a name="declaring-query-parameters"></a>Deklarieren von Abfrageparametern

Um auf Abfrageparameter verweisen zu können, muss der Abfragetext (oder die von ihm verwendeten Funktionen) zunächst den Abfrageparameter deklarieren, den er verwendet. Für jeden Parameter enthält die Deklaration den Namen und den Skalartyp. Optional kann der Parameter auch einen Standardwert verwenden, der verwendet werden soll, wenn die Anforderung keinen konkreten Wert für den Parameter liefert. Kusto analysiert dann den Wert des Abfrageparameters gemäß den normalen Analyseregeln für diesen Typ.

**Syntax**

`declare``query_parameters` `:` `=` `,` *Type1* *Name1* *DefaultValue1*Name1 Typ1 [ DefaultValue1 ] [ ...] `(``);`

* *Name1*: Der Name eines Abfrageparameters, der in der Abfrage verwendet wird.
* *Typ1*: Der entsprechende Typ (z.B. `string`, `datetime`, etc.) Die vom Benutzer bereitgestellten Werte werden als Zeichenfolgen codiert, und Kusto wendet die entsprechende Analysemethode auf den Abfrageparameter an, um einen stark typisierten Wert abzubekommen.
* *DefaultValue1*: Ein optionaler Standardwert für den Parameter. Dies muss ein Literal des entsprechenden Skalartyps sein.

> [!NOTE]
> Wie [benutzerdefinierte Funktionen](functions/user-defined-functions.md)können `dynamic` Abfrageparameter des Typs keine Standardwerte aufweisen.

**Beispiele**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>Angeben von Abfrageparametern in einer Clientanwendung

Die Namen und Werte von `string` Abfrageparametern werden von der Anwendung, die die Abfrage erstellt, als Werte bereitgestellt. Es darf kein Name wiederholt werden.

Die Interpretation der Werte erfolgt gemäß der Deklarationsanweisung der Abfrageparameter. Jeder Wert wird analysiert, als wäre er ein Literal im Text körpereingehend einer Abfrage entsprechend dem Typ, der durch die Anweisung für die Abfrageparameterdeklaration angegeben wird.

### <a name="rest-api"></a>REST-API

Abfrageparameter werden von Clientanwendungen `properties` über den Steckplatz des JSON-Objekts des `Parameters`Anforderungstexts in einem verschachtelten Eigenschaftenbeutel mit dem Namen bereitgestellt. Hier ist z. B. der Text eines REST-API-Aufrufs an Kusto, der das Alter eines Benutzers berechnet (vermutlich, indem die Anwendung den Benutzer nach seinem Geburtstag fragt):

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Um die Namen und Werte von Abfrageparametern bei Verwendung der Kusto .NET-Clientbibliothek bereitzustellen, erstellt man eine neue Instanz des `ClientRequestProperties` Objekts und verwendet dann die `HasParameter`, `SetParameter`und `ClearParameter` Methoden, um Abfrageparameter zu bearbeiten. Beachten Sie, dass diese Klasse eine Reihe `SetParameter`stark typisierter Überladungen für ; Intern generieren sie das entsprechende Literal der Abfragesprache `string` und senden es wie oben beschrieben über die REST-API. Der Abfragetext selbst muss [die Abfrage paramters weiterhin deklarieren.](#declaring-query-parameters)

### <a name="kustoexplorer"></a>Kusto.Explorer

Um die Abfrageparameter festzulegen, die beim Stellenanstellen an den Dienst gesendet`ALT` + `P`werden, verwenden Sie das Symbol **Abfrageparameter** "Schraubenschlüssel" ( ).

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
