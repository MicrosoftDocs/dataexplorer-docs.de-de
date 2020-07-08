---
title: '. Drop Block Tags: Azure Daten-Explorer'
description: In diesem Artikel wird der Befehl Drop Block Tags in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 23784a1e3e00c242665a43dffcc528bfeff68896
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060624"
---
# <a name="drop-extent-tags"></a>. Drop Block-Tags

Der Befehl wird im Kontext einer bestimmten Datenbank ausgeführt. Es löscht bestimmte Block [Tags](extents-overview.md#extent-tagging) aus allen oder bestimmten Blöcken in der Datenbank und in der Tabelle.  

> [!NOTE]
> Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.
> Weitere Informationen zu Blöcken finden Sie unter [Übersicht über Blöcke (Daten-Shards)](extents-overview.md).

Es gibt zwei Möglichkeiten, um anzugeben, welche Tags aus welchen Blöcken entfernt werden sollen:

* Geben Sie die Tags, die aus allen Blöcken in der angegebenen Tabelle entfernt werden sollen, explizit an.
* Stellen Sie eine Abfrage bereit, deren Ergebnisse den Block-IDs in der Tabelle und für jeden Block die zu entfernenden Tags angeben.

## <a name="syntax"></a>Syntax

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TAGN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *Abfrage*

`async`(optional): führen Sie den Befehl asynchron aus.
   * Eine Vorgangs-ID (GUID) wird zurückgegeben.
   * Der Status des Vorgangs kann überwacht werden. Verwenden Sie den Befehl [. Show Operations](operations.md#show-operations) .
   * Verwenden Sie den Befehl [Vorgangs Details anzeigen](operations.md#show-operation-details) , um die Ergebnisse einer erfolgreichen Ausführung abzurufen.

## <a name="restrictions"></a>Beschränkungen

Alle Blöcke müssen sich in der Kontext Datenbank befinden und müssen derselben Tabelle angehören.

## <a name="specify-extents-with-a-query"></a>Angeben von Blöcken mit einer Abfrage

Die Blöcke und die zu löschenden Tags werden mithilfe einer Kusto-Abfrage angegeben. Es wird ein Recordset mit der Spalte "extentid" und einer Spalte mit dem Namen "Tags" zurückgegeben.

> [!NOTE]
> Wenn Sie die [Kusto .NET-Client Bibliothek](../api/netfx/about-kusto-data.md)verwenden, generieren die folgenden Methoden den erforderlichen Befehl:
> * `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
> * `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für alle Beteiligten Quell-und Ziel Tabellen.

### <a name="syntax-for-drop-extent-tags-in-query"></a>Syntax für. Drop Block-Tags in der Abfrage

```kusto 
.drop extent tags <| ...query...
```

### <a name="return-output"></a>Ausgabe zurückgeben

Output-Parameter |Typ |Beschreibung 
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block, dessen Tags geändert wurden. Der Block wird als Teil des Vorgangs gelöscht.
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der Tags geändert hat. Der Block wird erstellt und als Teil des Vorgangs hinzugefügt. Bei Fehler: "failed".
Resultextenttags |Zeichenfolge |Die Auflistung von Tags, mit denen der Ergebnis Block markiert ist, falls vorhanden, oder "Null" für den Fall, dass der Vorgang fehlschlägt.
Details |Zeichenfolge |Schließt die Fehlerdetails ein, wenn der Vorgang fehlschlägt.

## <a name="examples"></a>Beispiele

### <a name="drop-one-tag"></a>Ein Tag ablegen

Löschen `drop-by:Partition000` Sie das Tag aus einem beliebigen Block in einer Tabelle, die mit ihm gekennzeichnet ist:

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

### <a name="drop-several-tags"></a>Mehrere Tags löschen

Löschen Sie die Tags `drop-by:20160810104500` , `a random tag` und `drop-by:20160810` aus einem beliebigen Block in einer Tabelle, die mit einem der beiden Tags gekennzeichnet ist:

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

### <a name="drop-all-drop-by-tags"></a>Alle `drop-by` Tags löschen 

Löschen Sie alle `drop-by` Tags aus den Blöcken in der Tabelle `MyTable` :

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

### <a name="drop-all-tags-matching-specific-regex"></a>Alle Tags löschen, die mit einem bestimmten regulären Ausdruck übereinstimmen 

Alle mit Regex übereinstimmenden Tags `drop-by:StreamCreationTime_20160915(\d{6})` aus Blöcken in der Tabelle löschen `MyTable` :

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

## <a name="sample-output"></a>Beispielausgabe

|Originalextentid |Resultextentid | Resultextenttags | Details
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df | |
