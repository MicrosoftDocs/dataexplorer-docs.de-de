---
title: Bewährte Methoden für den Schemaentwurf - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden bewährte Methoden für den Schemaentwurf in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f9e2d4a2ad50b140d041179736b48a41a424f5c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522167"
---
# <a name="best-practices-for-schema-design"></a>Bewährte Methoden für den Schemaentwurf

Es gibt mehrere "Dos and Don'ts", denen Sie folgen können, damit Ihre Verwaltungsbefehle besser funktionieren und einen leichteren Effekt auf den Dienst haben.

## <a name="do"></a>Sie sollten

1. Wenn Sie mehrere Tabellen erstellen [`.create tables`](create-tables-command.md) müssen, verwenden `.create table` Sie den Befehl, anstatt viele Befehle auszugeben.
2. Wenn Sie mehrere Tabellen umbenennen müssen, tun [`.rename tables`](rename-table-command.md)Sie dies mit einem einzelnen Aufruf von , anstatt einen separaten Aufruf für jedes Tabellenpaar auszugeben.
3. Verwenden Sie den `.show` Befehl mit dem niedrigsten Bereich,`|`anstatt Filter nach einer Pipe ( ) anzuwenden. Beispiel:
    - Verwenden von `.show table T extents` anstelle von `.show cluster extents | where TableName == 'T'`
    - Verwenden Sie `.show database DB schema` anstelle von `.show schema | where DatabaseName == 'DB'`.
4. Nur `.show table T` verwenden, wenn Sie aktuelle Statistiken für eine einzelne Tabelle abrufen müssen. Wenn Sie nur die Existenz der Tabelle überprüfen oder einfach das `.show table T schema as json`Schema der Tabelle abrufen müssen, verwenden Sie .
5. Stellen Sie beim Definieren des Schemas für eine Tabelle, die Datumszeitwerte enthält, sicher, dass diese Spalten mit dem `datetime` Typ eingegeben werden.
    - Kusto ist hochoptimiert für `datetime` die Filterung von Spalten. Konvertieren `string` oder numerische (z. B. `long`) `datetime` Spalten zum Zeitpunkt der Abfrage zum Filtern nicht, wenn dies vor oder während der Aufnahmezeit möglich ist.

## <a name="dont"></a>Sie sollten auf keinen Fall

1. Führen Sie `.show` Befehle nicht zu häufig `.show schema`aus `.show databases` `.show tables`(z. B. , , ). Wenn möglich - zwischenspeichern Sie die Informationen, die sie zurückgeben.
2. Führen `.show schema` Sie den Befehl nicht auf einem Cluster aus, der ein großes Schema (z. B. mit mehr als 100 Datenbanken) enthält. Verwenden Sie [`.show databases schema`](../management/show-schema-database.md)stattdessen .
3. Führen Sie [befehls-dann-abfragevorgänge](index.md#combining-queries-and-control-commands) nicht zu häufig aus.
    - *command-then-query:* bedeutet, das Resultset des Steuerbefehls zu pipieren und Filter/Aggregationen darauf anzuwenden.
        - Beispiel: `.show ... | where ... | summarize ...`
    - Beim Ausführen von `.show cluster extents | count` ca. `| count`:: (Hervorhebung auf der ), bereitet Kusto zunächst eine Datentabelle vor, die alle Details zu allen Ausdehnungen im Cluster enthält, und sendet dann diese nur in-Memory-Tabelle an das Kusto-Modul, um die Anzahl auszuführen. Das bedeutet, dass Kusto tatsächlich sehr hart in einem unoptimierten Pfad arbeitet, um Ihnen eine so triviale Antwort zurückzugeben.
4. Übermäßige Verwendung von Ausdehnungs-Tags als Teil der Datenerfassung. Vor allem `drop-by:` bei der Verwendung von Tags, die die Fähigkeit des Systems einschränken, leistungsorientierte Pflegeprozesse im Hintergrund durchzuführen.
    - Siehe Leistungshinweise [hier](../management/extents-overview.md#extent-tagging).
    