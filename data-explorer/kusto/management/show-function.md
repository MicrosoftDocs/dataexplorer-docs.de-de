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
ms.openlocfilehash: 6d4f4fcdbe60975b8b1c3369a364f062b223f9c6
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967365"
---
# <a name="show-functions"></a>. Show-Funktion (en)

Listet alle gespeicherten Funktionen in der aktuell ausgewählten Datenbank auf.
Informationen zum Zurückgeben einer bestimmten Funktion finden Sie unter [. Show Function](#show-function).

## <a name="show-functions"></a>. Show-Funktionen

```kusto
.show functions
```

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).
 
|Output-Parameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |Zeichenfolge |Die Parameter, die für die Funktion erforderlich sind.
|Text  |Zeichenfolge |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|Zeichenfolge|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Art und Weise, in der die Funktion aufgerufen wird.
|DocString|Zeichenfolge|Eine Beschreibung der Funktion zu UI-Zwecken.
 
**Ausgabe Beispiel** 

|Name |Parameter|Text|Ordner|DocString
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
|Parameter  |Zeichenfolge |Die Parameter, die für die Funktion erforderlich sind.
|Text  |Zeichenfolge |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|Zeichenfolge|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Methode, mit der die Funktion aufgerufen wird.
|DocString|Zeichenfolge|Eine Beschreibung der Funktion zu UI-Zwecken.
 
> [!NOTE] 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben.
> * Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).
 
**Beispiel** 

```kusto
.show function MyFunction1 
```
    
|Name |Parameter |Text|Ordner|DocString
|---|---|---|---|---
|MyFunction1 |() | {Stormevents &#124; Limit 100}|MyFolder|Einfache Demofunktion
