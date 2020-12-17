---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 06/11/2020
ms.author: orspodek
ms.openlocfilehash: 3d366a824ecfdf89c56b1049b70df664573cd7e0
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "97371656"
---
Die Änderungen, die Sie an einer Tabelle vornehmen können, hängen von den folgenden Parametern ab:
* Die **Tabelle** ist neu oder vorhanden.
* Die **Zuordnung** ist neu oder vorhanden.

Tabellentyp | Zuordnungstyp | Verfügbare Anpassungen|
|---|---|---|
|Neue Tabelle   | Neue Zuordnung |Datentyp ändern, Spalte umbenennen, Neue Spalte, Spalte löschen, Spalte aktualisieren, Aufsteigend sortieren, Absteigend sortieren  |
|Vorhandene Tabelle  | Neue Zuordnung | Neue Spalte (für die Sie dann den Datentyp ändern und die Sie umbenennen und aktualisieren können) <br> Spalte aktualisieren, Aufsteigend sortieren, Absteigend sortieren  |
| | Vorhandene Zuordnung | Aufsteigend sortieren, Absteigend sortieren

> [!NOTE]
> Wenn Sie eine neue Spalte hinzufügen oder eine Spalte aktualisieren, können Sie Zuordnungstransformationen ändern. Weitere Informationen finden Sie unter [Zuordnungstransformationen](../ingest-data-one-click.md#mapping-transformations).