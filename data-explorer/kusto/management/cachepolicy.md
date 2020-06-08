---
title: 'Cache Richtlinie (Hot und Cold Cache): Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Cache Richtlinie (Hot und Cold Cache) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 130526b41030ac3936236f8fd8bba81f20b4bb0e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512519"
---
# <a name="cache-policy-hot-and-cold-cache"></a>Cache Richtlinie (Hot und Cold Cache) 

Azure Daten-Explorer speichert die erfassten Daten in einem zuverlässigen Speicher (am häufigsten Azure BLOB Storage), von der tatsächlichen Verarbeitung (z. b. Azure Compute-Knoten). Um Abfragen für diese Daten zu beschleunigen, speichert Azure Daten-Explorer Sie oder Teile davon auf den Verarbeitungs Knoten, SSD oder sogar im RAM zwischenspeichert. Azure Daten-Explorer umfasst einen ausgereiften Cache Mechanismus, der Intelligent entscheiden soll, welche Datenobjekte zwischengespeichert werden sollen. Der Cache ermöglicht es Azure Daten-Explorer, die verwendeten Daten Artefakte zu beschreiben, damit wichtiger ist, dass wichtige Daten Vorrang haben. Beispielsweise Spalten Indizes und Spaltendaten-Shards,

Die beste Abfrageleistung wird erzielt, wenn alle erfassten Daten zwischengespeichert werden. Manchmal begründen bestimmte Daten nicht die Kosten dafür, dass Sie im lokalen SSD-Speicher "warm" bleiben.
Viele Teams sollten z. b. feststellen, dass auf ältere Protokolldaten Sätze seltener zugegriffen wird, ist weniger wichtig.
Sie bevorzugen eine geringere Leistung bei der Abfrage dieser Daten, anstatt zu zahlen, um Sie immer wieder warm zu halten.

Azure Daten-Explorer Cache bietet eine präzise **Cache Richtlinie** , die Kunden zur Unterscheidung zwischen dem Daten Cache für **heiße Daten** und dem **Cache für kalte Daten**verwenden können. Der Azure Daten-Explorer-Cache versucht, alle Daten, die in der Kategorie "Hot Data Cache" (lokaler SSD (oder RAM)) liegen, bis zur definierten Größe des Speichers für heiße Daten zu speichern. Der verbleibende lokale SSD-Speicherplatz wird verwendet, um Daten zu speichern, die nicht als Hot kategorisiert sind. Eine sinnvolle Konsequenz dieses Entwurfs ist, dass Abfragen, die viele kalte Daten aus zuverlässigem Speicher laden, keine Daten aus dem Cache für heiße Daten entfernen. Folglich gibt es keine großen Auswirkungen auf Abfragen, die die Daten im Cache für heiße Daten betreffen.

Die wichtigsten Auswirkungen beim Festlegen der Richtlinie für den Hot Cache lauten:
* **Kosten**: die Kosten für zuverlässigen Speicher können erheblich geringer sein als bei lokalen SSD. In Azure ist es zurzeit ungefähr 45 mal günstiger.
* **Leistung**: Daten werden schneller abgefragt, wenn Sie sich im lokalen SSD befinden, insbesondere bei Bereichs Abfragen, bei denen große Datenmengen gescannt werden.  

Verwenden Sie den [Cache Policy-Befehl](cache-policy.md) , um die Cache Richtlinie zu verwalten.

> [!TIP]
>Azure Daten-Explorer ist für Ad-hoc-Abfragen mit zwischen Resultsets konzipiert, die dem gesamten Arbeitsspeicher des Clusters entsprechen.
>Verwenden Sie für große Aufträge, wie z. b. Map-Reduce, wo Sie Zwischenergebnisse in dauerhaftem Speicher wie z. b. einem SSD speichern möchten, die Funktion für fortlaufenden Export. Diese Funktion ermöglicht das Ausführen von Batch Abfragen mit langer Ausführungszeit mithilfe von Diensten wie hdinsight oder Azure Databricks.
 
## <a name="how-cache-policy-is-applied"></a>Anwenden der Cache Richtlinie

Wenn Daten in Azure Daten-Explorer erfasst werden, verfolgt das System das Datum und die Uhrzeit der Erfassung sowie den Umfang, der erstellt wurde. Der Erfassungs Datums-und Uhrzeitwert (oder der Höchstwert, wenn ein Block aus mehreren bereits vorhandenen Blöcken erstellt wurde) wird zum Auswerten der Cache Richtlinie verwendet.

> [!Note]
> Sie können einen Wert für Datum und Uhrzeit der Erfassung angeben, indem Sie die Eigenschaft Erfassung verwenden `creationTime` .

Standardmäßig ist die effektive Richtlinie `null` . Dies bedeutet, dass alle Daten als **heiß**angesehen werden.
Eine Richtlinie, die keine `null` Tabellenebene ist, überschreibt eine Richtlinie auf Datenbankebene.

## <a name="scoping-queries-to-hot-cache"></a>Bereichs bezogene Abfragen für den Hot-Cache

Kusto unterstützt Abfragen, die auf Daten im Gültigkeitsbereich des Datenspeichers beschränkt sind.
Es gibt mehrere Abfrage Möglichkeiten.

- Fügen Sie der Abfrage eine Client Anforderungs Eigenschaft `query_datascope` mit dem Namen hinzu.
   Mögliche Werte: `default` , `all` und `hotcache` .
- Verwenden Sie eine- `set` Anweisung im Abfragetext: `set query_datascope='...'` .
   Mögliche Werte sind die gleichen wie für die Client Request-Eigenschaft.
- Fügen Sie `datascope=...` direkt nach einem Tabellen Verweis im Abfragetext einen Text hinzu. 
   Mögliche Werte sind `all` und `hotcache`.

Der `default` Wert gibt die Verwendung der Standardeinstellungen für den Cluster an, die bestimmen, dass die Abfrage alle Daten abdecken soll.

Wenn eine Abweichung zwischen den verschiedenen Methoden vorliegt, hat `set` Vorrang vor der Eigenschaft Client Anforderung. Die Angabe eines Werts für einen Tabellen Verweis hat Vorrang vor beiden.

In der folgenden Abfrage verwenden z. b. alle Tabellen Verweise nur Hot Cache-Daten, mit Ausnahme des zweiten Verweises auf "T", der auf alle Daten beschränkt ist:

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>Cache Richtlinie und Beibehaltungs Richtlinie

Die Cache Richtlinie ist unabhängig von der [Beibehaltungs Richtlinie](./retentionpolicy.md): 
- Cache Richtlinie definiert, wie Ressourcen priorisiert werden. Abfragen über wichtige Daten sind schneller und gegen die Auswirkungen von Abfragen auf weniger wichtige Daten.
- Die Beibehaltungs Richtlinie definiert den Umfang der abzufragbaren Daten in einer Tabelle/Datenbank (insbesondere `SoftDeletePeriod` ).

Konfigurieren Sie diese Richtlinie, um das optimale Gleichgewicht zwischen Kosten und Leistung basierend auf dem erwarteten Abfrage Muster zu erzielen.

Beispiel:
* `SoftDeletePeriod`= 56D
* `hot cache policy`= 28d

Im Beispiel befinden sich die Daten der letzten 28 Tage auf dem Cluster-SSD, und die zusätzlichen 28 Tage Daten werden in Azure BLOB Storage gespeichert.
Sie können Abfragen für die vollständigen 56 Tage von Daten ausführen.
 
