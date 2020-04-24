---
title: Cache-Richtlinie (heißer und kalter Cache) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Cacherichtlinie (heißer und kalter Cache) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 591763ac5d94a8361a4b78c1b199bb05299cc004
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522133"
---
# <a name="cache-policy-hot-and-cold-cache"></a>Cache-Richtlinie (Heißer und kalter Cache)

Azure Data Explorer speichert seine aufgenommenen Daten in zuverlässigem Speicher (am häufigsten Azure Blob Storage), weg von seinen tatsächlichen Verarbeitungsknoten (z. B. Azure Compute). Um Abfragen für diese Daten zu beschleunigen, speichert Azure Data Explorer diese Daten (oder Teile davon) auf den Verarbeitungsknoten, SSD oder sogar im RAM zwischen. Azure Data Explorer enthält einen ausgeklügelten Cachemechanismus, mit dem intelligent entschieden werden kann, welche Datenobjekte zwischengespeichert werden sollen. Der Cache ermöglicht es Azure Data Explorer, die von ihm verwendeten Datenartefakte (z. B. Spaltenindizes und Spaltendatenshards) zu beschreiben, sodass mehr "wichtige" Daten Vorrang haben können.

Obwohl die beste Abfrageleistung erreicht wird, wenn alle aufgenommenen Daten zwischengespeichert werden, rechtfertigen bestimmte Daten oft nicht die Kosten, die für das Halten im lokalen SSD-Speicher "warm" gehalten werden.
Beispielsweise betrachten viele Teams selten aufgerufene ältere Protokolldatensätze als von geringerer Bedeutung.
Sie würden es vorziehen, die Leistung bei der Abfrage dieser Daten zu reduzieren, anstatt zu bezahlen, um sie die ganze Zeit warm zu halten.

Der Azure Data Explorer-Cache bietet eine detaillierte **Cacherichtlinie,** mit der Kunden zwischen zwei Datencacherichtlinien unterscheiden können: **Hot Data Cache** und Cold Data **Cache**. Der Azure Data Explorer-Cache versucht, alle Daten, die in den Hot data Cache in lokale SSD (oder RAM) fallen, bis zur definierten Größe des Hotdata-Cache beizubehalten. Der verbleibende lokale SSD-Speicherplatz wird zum Auffassen von Daten verwendet, die nicht als heiß kategorisiert sind. Eine nützliche Auswirkung dieses Entwurfs ist, dass Abfragen, die viele kalte Daten aus zuverlässigem Speicher laden, keine Daten aus dem Hot Data Cache entfernen. Daher hat dies keine großen Auswirkungen auf Abfragen, die die Daten im Hot Data Cache betreffen.

Die wichtigsten Auswirkungen der Einstellung der Hot-Cache-Richtlinie sind:
* **Kosten** Die Kosten für zuverlässigen Speicher können erheblich niedriger sein als bei lokalen SSDs (z. B. ist sie in Azure derzeit etwa 45-mal billiger).
* **Performance** Daten können schneller abgefragt werden, wenn sie sich in lokaler SSD befinden. Dies gilt insbesondere für Bereichsabfragen, d. h. Abfragen, die große Datenmengen scannen.  

[Mit Steuerungsbefehlen](cache-policy.md) können Administratoren die Cacherichtlinie verwalten.

## <a name="how-the-cache-policy-gets-applied"></a>Wie die Cacherichtlinie angewendet wird

Wenn Daten in Azure Data Explorer aufgenommen werden, verfolgt das System das Datum/die Uhrzeit, zu der die Aufnahme durchgeführt wurde, und die Ausdehnung. Der Aufnahmedatums-/Uhrzeitwert der Ausdehnung (oder der Maximalwert, wenn eine Ausdehnung aus mehreren bereits vorhandenen Ausdehnungen erstellt wurde) wird verwendet, um die Cacherichtlinie auszuwerten.

Standardmäßig ist `null`die effektive Richtlinie , was bedeutet, dass alle Daten als **heiß**betrachtet werden.
Eine Richtlinie`null` auf Nicht-Tabellenebene überschreibt eine Richtlinie auf Datenbankebene.

> [!Note] 
> Sie können einen Wert für das Aufnahmedatum/die Aufnahmezeit `creationTime`angeben, indem Sie die Aufnahmeeigenschaft verwenden. 

## <a name="scoping-queries-to-hot-cache"></a>Scoping-Abfragen in den Hot-Cache

Kusto unterstützt Abfragen, die nur auf Hot-Cache-Daten beschränkt sind. Dafür stehen verschiedene Möglichkeiten zur Verfügung:

- Hinzufügen einer Clientanforderungseigenschaft, die `default`der `all`Abfrage `hotcache`aufgerufen `query_datascope` wird Mögliche Werte: , und .
- Verwenden `set` Sie eine Anweisung `set query_datascope='...'`im Abfragetext: , Mögliche Werte sind die gleichen wie für die Clientanforderungseigenschaft.
- Fügen `datascope=...` Sie einen Text unmittelbar nach einem Tabellenverweis im Abfragetext hinzu. Mögliche Werte sind `all` und `hotcache`.

Der `default` Wert gibt die Verwendung der Cluster-Standardeinstellungen an, die festlegen, dass die Abfrage alle Daten abdecken soll.



Wenn es eine Diskrepanz zwischen den `set` verschiedenen Methoden gibt: hat Vorrang vor der Clientanforderungseigenschaft, und die Angabe eines Werts für einen Tabellenverweis hat Vorrang vor beiden.

In der folgenden Abfrage verwenden z. B. alle Tabellenverweise nur `T` Hotcache-Daten, mit Ausnahme des zweiten Verweises, auf den alle Daten beschränkt sind:

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>Cache-Richtlinie vs. Aufbewahrungsrichtlinie

Die Cacherichtlinie ist unabhängig von der [Aufbewahrungsrichtlinie:](./retentionpolicy.md) 
- Die Cacherichtlinie definiert, wie Ressourcen priorisiert werden, damit Abfragen über wichtige Daten schneller und resistent gegen die Auswirkungen von Abfragen auf weniger wichtige Daten sind. 
- Die Aufbewahrungsrichtlinie definiert die Ausdehnung der abfraüberbaren Daten in einer Tabelle/Datenbank (insbesondere ). `SoftDeletePeriod`

Es wird empfohlen, diese Richtlinie so zu konfigurieren, dass basierend auf dem erwarteten Abfragemuster ein optimales Gleichgewicht zwischen Kosten und Leistung erreicht wird.

Beispiel:
* `SoftDeletePeriod`= 56d
* `hot cache policy`= 28d

Im Beispiel befinden sich die letzten 28 Tage der Daten auf der Cluster-SSD, und die **zusätzlichen** 28 Tage werden im Azure-Blobspeicher gespeichert. Sie können Abfragen für die gesamten 56 Tage der Daten ausführen. 

## <a name="cache-policy-does-not-make-kusto-a-cold-storage-technology"></a>Cache-Richtlinie macht Kusto nicht zu einer Cold Storage-Technologie

Azure Data Explorer wurde entwickelt, um Ad-hoc-Abfragen auszuführen, wobei Zwischenergebnissätze dem gesamten RAM des Clusters entsprechen. Für große Aufträge, z. B. map-reduce, bei denen Sie Zwischenergebnisse in persistentem Speicher (z. B. einer SSD) speichern möchten, verwenden Sie die kontinuierliche Exportfunktion. Auf diese Weise können Sie lang andauernde Batchabfragen mithilfe von Diensten wie HDInsight oder Azure Databricks durchführen.