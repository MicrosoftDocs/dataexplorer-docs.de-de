---
title: Entitäten – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden Entitäten in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d31765d72d37b0146cf7ba8a42e02722296bf80e
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663468"
---
# <a name="entities"></a>Entitäten

Kusto-Abfragen werden im Kontext einer Kusto-Datenbank ausgeführt, die an einen Kusto-Cluster angefügt ist. Die Daten in der Datenbank sind als Tabellen strukturiert, auf die die Abfrage verweisen kann. Innerhalb der Tabelle sind die Daten als rechteckiges Raster mit Spalten und Zeilen angeordnet. Darüber hinaus können Abfragen auf gespeicherte Funktionen in der Datenbank verweisen. Dabei handelt es sich um wiederverwendbare Abfragefragmente.

* Cluster sind Entitäten, die Datenbanken enthalten.
  Cluster haben keinen Namen, auf sie kann jedoch mithilfe der Sonderfunktion `cluster()`unter Verwendung des Cluster-URI verwiesen werden.
  `cluster("https://help.kusto.windows.net")` verweist beispielsweise auf einen Cluster, der die Datenbank `Samples` enthält.

* [Datenbanken](./databases.md) sind benannte Entitäten, die Tabellen und gespeicherte Funktionen enthalten. Alle Kusto-Abfragen werden im Kontext einer Datenbank ausgeführt, und auf die Entitäten dieser Datenbank kann durch die Abfrage ohne Qualifizierungen verwiesen werden. Auf andere Datenbanken des Clusters sowie auf Datenbanken oder andere Cluster kann außerdem mithilfe der Sonderfunktion [database()](../databasefunction.md) verwiesen werden. Zum Beispiel, `cluster("https://help.kusto.windows.net").database("Samples")`
  ist beispielsweise ein universeller Verweis auf eine bestimmte Datenbank.

* [Tabellen](./tables.md) sind benannte Entitäten, die Daten enthalten. Eine Tabelle verfügt über einen geordneten Satz von Spalten sowie über null oder mehr Datenzeilen, die jeweils einen Datenwert für die einzelnen Tabellenspalten enthalten. Auf Tabellen kann allein anhand des Namens verwiesen werden, wenn sie sich in der Datenbank im Kontext der Abfrage befinden. Andernfalls, können sie mit einem Datenbankverweis qualifiziert werden. `cluster("https://help.kusto.windows.net").database("Samples").StormEvents` ist beispielsweise ein universeller Verweis auf eine bestimmte Tabelle in der Datenbank `Samples`.
  Auf Tabellen kann auch mithilfe der Sonderfunktion [table()](../tablefunction.md) verwiesen werden.

* [Spalten](./columns.md) sind benannte Entitäten mit einem [skalaren Datentyp](../scalar-data-types/index.md).
  Auf Spalten wird in der Abfrage relativ zum Tabular Data Stream verwiesen, der sich im Kontext des spezifischen Operators befindet, durch den auf sie verwiesen wird.

* [Gespeicherte Funktionen](./stored-functions.md) sind benannte Entitäten, die die Wiederverwendung von Kusto-Abfragen oder Abfrageteilen ermöglichen.

* [Externe Tabellen](./externaltables.md) sind Entitäten, die auf Daten verweisen, die außerhalb der Kusto-Datenbank gespeichert.
  Externe Tabellen werden zum Exportieren von Daten aus Kusto in den externen Speicher und zum Abfragen externer Daten verwendet, ohne sie in Kusto zu erfassen.