---
title: '. Merge-Blöcke: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Befehl Merge Blöcke in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 6b633358713b0ff48d14fc9c5ca2a907bad0afab
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060661"
---
# <a name="merge-extents"></a>.merge extents

Mit diesem Befehl werden die Blöcke zusammengeführt, die von Ihren IDs in der angegebenen Tabelle angegeben werden. 

> [!NOTE]
> Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.
> Weitere Informationen zu Blöcken finden Sie unter [Übersicht über Blöcke (Daten-Shards)](extents-overview.md).

## <a name="syntax"></a>Syntax

`.merge``[async | dryrun]` *TableName* `(` *guid1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

Es gibt zwei Arten von Merge-Vorgängen:
* Zusammen *führen* , um Indizes neu zu erstellen
* *Neu erstellen* , um die Daten vollständig neu zu erstellen

Es stehen drei Optionen zur Verfügung:
* `async`: Führen Sie den Befehl asynchron aus. 
    * Eine Vorgangs-ID (GUID) wird zurückgegeben.
    * Der Status des Vorgangs kann überwacht werden. Verwenden Sie die Vorgangs-ID mit dem `.show operations <operation ID>` Befehl.
* `dryrun`: Mit dem Vorgang werden nur die Blöcke aufgelistet, die zusammengeführt werden sollen, aber Sie werden nicht tatsächlich zusammengeführt.
* `with(rebuild=true)`: Die Blöcke werden neu erstellt und die Daten erneut erstellt, anstatt zusammengeführt zu werden. Die Indizes werden neu erstellt.

## <a name="return-output"></a>Ausgabe zurückgeben

Output-Parameter |type |BESCHREIBUNG
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block in der Quell Tabelle, der in den Zielblock zusammengeführt wurde.
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Umfang, der aus den Quell Blöcken erstellt wurde. Bei Fehler: "failed" oder "abgebrochen".
Duration |Zeitraum |Der Zeitraum, der für den Abschluss des MERGE-Vorgangs benötigt wird.

## <a name="examples"></a>Beispiele

### <a name="rebuild-two-specific-extents-in-mytable-asynchronously"></a>Erneutes Erstellen zweier spezifischer Blöcke in `MyTable` , asynchron.

```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

### <a name="merge-two-specific-extents-in-mytable-synchronously"></a>Zusammenführen zweier spezifischer Blöcke in `MyTable` synchron.

```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

## <a name="notes"></a>Notizen

* Im allgemeinen `.merge` sollten Befehle nicht manuell ausgeführt werden. Die Befehle werden fortlaufend und automatisch im Hintergrund des Clusters ausgeführt, gemäß den [Zusammenarbeits Richtlinien](mergepolicy.md) für Tabellen und Datenbanken.  
  * Weitere Informationen zu den Kriterien für das Zusammenführen mehrerer Blöcke zu einer einzigen Erweiterung finden Sie unter [Merge Policy](mergepolicy.md).
* `.merge`Vorgänge haben einen möglichen Endzustand von `Abandoned` , der beim Ausführen `.show operations` mit der Vorgangs-ID angezeigt werden kann. Dieser Status weist darauf hin, dass die Quell Blöcke nicht zusammengeführt wurden, da einige der Quell Blöcke nicht mehr in der Quell Tabelle vorhanden sind. Der Status wird in folgenden Fällen angezeigt `Abandoned` :
   * Die Quell Blöcke wurden im Rahmen der Beibehaltungs Dauer der Tabelle gelöscht.
   * Die Quell Blöcke wurden in eine andere Tabelle verschoben.
   * Die Quell Tabelle wurde vollständig gelöscht oder umbenannt.
