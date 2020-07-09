---
title: '. Alter Block Tags: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Alter Block-Befehl in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 00c4cfbb4b6415afcd68e8e41864ca4a68cc097e
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060616"
---
# <a name="alter-extent-tags"></a>. Alter Block-Tags

Der Befehl wird im Kontext einer bestimmten Datenbank ausgeführt. Sie ändert die angegebenen Block [Tags](extents-overview.md#extent-tagging) aller Blöcke, die von der Abfrage zurückgegeben werden.

Die Blöcke und die zu ändernden Tags werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte mit dem Namen "extentid" zurückgibt.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für alle beteiligten Tabellen.

> [!NOTE]
> Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.
> Weitere Informationen zu Blöcken finden Sie unter [Übersicht über Blöcke (Daten-Shards)](extents-overview.md).

## <a name="syntax"></a>Syntax

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TAGN*'] `)`  <|  *Abfrage*

`async`(optional): führen Sie den Befehl asynchron aus.
   * Eine Vorgangs-ID (GUID) wird zurückgegeben. 
   * Der Status des Vorgangs kann überwacht werden. Verwenden Sie den Befehl [. Show Operations](operations.md#show-operations) .
   * Sie können die Ergebnisse einer erfolgreichen Ausführung abrufen. Verwenden Sie den Befehl [Vorgangs Details anzeigen](operations.md#show-operation-details) .

## <a name="restrictions"></a>Beschränkungen

Alle Blöcke müssen sich in der Kontext Datenbank befinden und müssen derselben Tabelle angehören.

## <a name="return-output"></a>Ausgabe zurückgeben

|Output-Parameter |type |BESCHREIBUNG|
|---|---|---|
|Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block, dessen Tags geändert wurden. Der Block wird als Teil des Vorgangs gelöscht.|
|Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der Tags geändert hat. Der Block wird erstellt und als Teil des Vorgangs hinzugefügt. Bei Fehler: "failed".|
|Resultextenttags |Zeichenfolge |Die Auflistung von Tags, mit denen der Ergebnis Block gekennzeichnet ist, oder "Null" für den Fall, dass der Vorgang fehlschlägt.|
|Details |Zeichenfolge |Schließt die Fehlerdetails ein, wenn der Vorgang fehlschlägt.|

## <a name="examples"></a>Beispiele

### <a name="alter-tags"></a>Tags ändern 

Ändern der Tags aller `MyTable` Blöcke in der Tabelle in`MyTag`

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

### <a name="alter-tags-of-all-extents"></a>Tags aller Blöcke ändern

Alter Tags aller Blöcke in der Tabelle, die `MyTable` mit gekennzeichnet sind `drop-by:MyTag` , `drop-by:MyNewTag` und`MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

## <a name="sample-output"></a>Beispielausgabe

|Originalextentid |Resultextentid | Resultextenttags | Details
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Drop-by: mynewtag myothernewtag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | Drop-by: mynewtag myothernewtag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f | Drop-by: mynewtag myothernewtag| 
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df | Drop-by: mynewtag myothernewtag| 
