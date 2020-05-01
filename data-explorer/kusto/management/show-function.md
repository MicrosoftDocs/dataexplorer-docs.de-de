---
title: . Show Functions-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Funktionen in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa81df5ca89c1a7dc523d7799050b8a7ad3adaba
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617327"
---
# <a name="show-functions"></a>. Show-Funktionen

Listet alle gespeicherten Funktionen in der aktuell ausgewählten Datenbank auf.
Informationen zum Zurückgeben einer bestimmten Funktion finden Sie unter [. Show Function](#show-function).

```kusto
.show functions
```

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).
 
|Output-Parameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Body  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Art und Weise, in der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.
 
**Ausgabe Beispiel** 

|Name |Parameter|Body|Ordner|DocString
|---|---|---|---|---
|MyFunction1 |() | {Stormevents &#124; Limit 100}|MyFolder|Einfache Demofunktion|
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|MyFolder|Demo Funktion mit Parameter|
|MyFunction3 |() | {Stormevents (100)}|MyFolder|Funktion aufrufen einer anderen Funktion||

## <a name="show-function"></a>.show function

```kusto
.show function MyFunc1
```

Listet die Details einer bestimmten gespeicherten Funktion auf. Eine Liste **aller** Funktionen finden Sie unter [. Show Functions](#show-functions).

**Syntax**

`.show``function` *FunctionName*

**Ausgabe**

|Output-Parameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Body  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Methode, mit der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.
 
> [!NOTE] 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben.
> * Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).
 
**Beispiel** 

```kusto
.show function MyFunction1 
```
    
|Name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction1 |() | {Stormevents &#124; Limit 100}|MyFolder|Einfache Demofunktion
