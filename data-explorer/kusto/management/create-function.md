---
title: '. Create-Funktion: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Funktion "Create" in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0a509e33ca1bc4c6d4a400428898b7c241222a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320977"
---
# <a name="create-function"></a>.create function

Erstellt eine gespeicherte Funktion, bei der es sich um eine wiederverwendbare [ `let` Anweisungs](../query/letstatement.md) Funktion mit dem angegebenen Namen handelt. Die Funktionsdefinition wird mit den Daten Bank Metadaten beibehalten.

Funktionen können andere Funktionen aufzurufen (rekursiv wird nicht unterstützt), und `let` Anweisungen sind als Teil des *Funktions* Texts zulässig. Siehe- [ `let` Anweisungen](../query/letstatement.md).

Regeln für Parametertypen und CSL-Anweisungen sind identisch mit denen für- [ `let` Anweisungen](../query/letstatement.md).
    
**Syntax**

`.create``function`[ `ifnotexists` ] [ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*] `)` ] [ `,` `skipvalidation` `=` ' true '] `)` ] *FunctionName* `(` *paramName* `:` *ParamType* [ `,` ...] `)` `{` *functionbody*`}`

**Ausgabe**    
    
|Ausgabeparameter |type |Beschreibung
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Text  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Methode, mit der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.

> [!NOTE]
> * Wenn die Funktion bereits vorhanden ist:
>    * Wenn `ifnotexists` Flag angegeben ist, wird der Befehl ignoriert (keine Änderung angewendet).
>    * Wenn das `ifnotexists` Flag nicht angegeben wird, wird ein Fehler zurückgegeben.
>    * Informationen zum Ändern einer vorhandenen Funktion finden Sie unter. [`.alter function`](alter-function.md)
> * Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).
> * Nicht alle Datentypen werden in- `let` Anweisungen unterstützt. Folgende Typen werden unterstützt: Boolean, String, Long, DateTime, TimeSpan, Double und Dynamic.
> * Verwenden Sie "skipvalidation", um die semantische Validierung der Funktion zu überspringen. Dies ist nützlich, wenn Funktionen in falscher Reihenfolge erstellt werden und F1, das F2 verwendet, zuvor erstellt wurde.

**Beispiele** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|Name|Parameter|Text|Ordner|DocString|
|---|---|---|---|---|
|MyFunction1|()|{Stormevents &#124; Limit 100}|Demo|Einfache Demofunktion|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|Name|Parameter|Text|Ordner|DocString|
|---|---|---|---|---|
|MyFunction2|(mylimit: Long)|{Stormevents &#124; Limit von mylimit}|Demo|Demo Funktion mit Parameter|
