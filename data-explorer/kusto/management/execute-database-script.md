---
title: . Execute Database Script-Azure Daten-Explorer
description: In diesem Artikel wird beschrieben, wie Sie Datenbankskript Funktionen in Azure Daten-Explorer ausführen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: c8fa3a000de67559c83745c598da40797e31f9b9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248351"
---
# <a name="execute-database-script"></a>.execute database script

Führt Befehle für den Batch von Steuerelementen im Bereich einer einzelnen Datenbank aus.

## <a name="syntax"></a>Syntax

`.execute` `database` `script`  
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]   
`<|`  
 *Control-Commands-Script*

### <a name="parameters"></a>Parameter

* *Control-Commands-Script*: Text mit mindestens einem Steuerungs Befehl.
* *Daten Bankbereich*: das Skript wird auf den *Daten Bankbereich* angewendet, der als Teil des Anforderungs Kontexts angegeben ist.

### <a name="optional-properties"></a>Optionale Eigenschaften

| Eigenschaft            | type            | BESCHREIBUNG                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `ContinueOnErrors`            | `bool`        | Wenn diese Einstellung auf festgelegt ist, `false` wird das Skript beim ersten Fehler beendet. Wenn Sie auf festgelegt ist, `true` wird die Skriptausführung fortgesetzt. Standardwert: `false`. |

## <a name="output"></a>Output

Jeder Befehl, der im Skript angezeigt wird, wird als separater Datensatz in der Ausgabe Tabelle gemeldet. Jeder Datensatz enthält die folgenden Felder:

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|OperationId  |Guid |Der Bezeichner des Befehls.
|CommandType  |String |Der Typ des Befehls.
|CommandText  |String |Text des jeweiligen Befehls.
|Ergebnis|String|Ergebnis der Ausführung eines bestimmten Befehls.
|`Reason`|String|Ausführliche Informationen zum Ergebnis der Befehlsausführung.

>[!NOTE]
>* Der Skript Text kann leere Zeilen und Kommentare zwischen den Befehlen enthalten.
>* Befehle werden sequenziell in der Reihenfolge ausgeführt, in der Sie im Eingabe Skript angezeigt werden.
>* Die Skriptausführung ist nicht transaktional, und bei einem Fehler wird kein Rollback ausgeführt. Es wird empfohlen, die idempotente Form von Befehlen zu verwenden, wenn Sie verwenden `.execute database script` .
>* Standardverhalten des Befehls: Fehler beim ersten Fehler. er kann mithilfe des Property-Arguments geändert werden.
>* Schreibgeschützte Steuerungsbefehle (. Show-Befehle) werden nicht ausgeführt und werden mit dem Status gemeldet `Skipped` .

## <a name="example"></a>Beispiel

```kusto
.execute database script <|
//
// Create tables
.create-merge table T(a:string, b:string)
//
// Apply policies
.alter-merge table T policy retention softdelete = 10d 
//
// Create functions
.create-or-alter function
  with (skipvalidation = "true") 
  SampleT1(myLimit: long) { 
    T1 | limit myLimit
}
```

|OperationId|CommandType|CommandText|Ergebnis|`Reason`|
|---|---|---|---|---|
|1d28531b-58c8-4023-a5d3-16fa73c06cfa|Tablecreate|. Create-MERGE-Tabelle T (a:String, b:String)|Abgeschlossen||
|67d0ea69-baa4-419a-93d3-234c03834360|Retentionpolicyalter|. Alter-Merge Table T Policy Retention Software Update = 10D|Abgeschlossen||
|0b0e8769-d4e8-4ff9-Adae-071e52a650c7|Functionkreateoralter|.create-or-alter function<br>with (skipvalidation = "true")<br>SampleT1 (mylimit: Long) {<br>T1 \| Limit mylimit<br>}|Abgeschlossen||
