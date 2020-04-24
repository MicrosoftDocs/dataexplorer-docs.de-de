---
title: Sandboxen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Sandboxen in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: faa7a8225aecd5992e3064fd10cfcc0e559c5127
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522966"
---
# <a name="sandboxes"></a>Sandkästen

Der Data Engine-Dienst von Kusto kann Sandboxen ausführen, um bestimmte Flows auszuführen, die eine sichere Isolierung erfordern.
Beispiele für diese Flows sind benutzerdefinierte Skripte, die mit dem [Python-Plugin](../query/pythonplugin.md) oder dem [R-Plugin](../query/rplugin.md)ausgeführt werden.

Für die Ausführung dieser Sandboxen verwendet Kusto eine Weiterentwicklung des [Drawbridge-Projekts](https://www.microsoft.com/research/project/drawbridge/) von Microsoft. Diese Lösung wird von anderen Microsoft-Diensten zum Ausführen benutzerdefinierter Objekte in einer Umgebung mit mehreren Mandanten verwendet.

Flows, die in Sandboxen ausgeführt werden, sind nicht nur isoliert, sondern auch lokal (in der Nähe der Daten), was bedeutet, dass für Remoteanrufe keine zusätzliche Latenz hinzugefügt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Das Datenmodul darf **nicht** für die [Festplattenverschlüsselung](https://docs.microsoft.com/azure/data-explorer/security#data-encryption) aktiviert sein.
  * Unterstützung für beide Features, die nebeneinander laufen, wird in Zukunft erwartet.
* Die erforderlichen Pakete (Images) zum Ausführen der Sandboxes werden auf jedem Knoten der Data Engine bereitgestellt und benötigen dedizierten SSD-Speicherplatz, um
  * Die geschätzte Größe beträgt 20 GB, was z. B. etwa 2,5 % der SSD-Kapazität einer D14_v2 VM oder 0,7 % der SSD-Kapazität einer L16_v1 VM entspricht.
  * Dies wirkt sich auf die Datenkapazität des Clusters aus und kann sich daher auf die [Kosten](https://azure.microsoft.com/pricing/details/data-explorer) des Clusters auswirken.

## <a name="runtime"></a>Typ

* Ein Sandkastenabfrageoperator kann eine oder mehrere Sandboxes für seine Ausführung verwenden.
  * Diese werden nur für eine einzelne Ausführung verwendet, nicht über mehrere Durchläufe hinweg freigegeben und nach Abschluss der Ausführung verworfen.
* Jeder Knoten verwaltet eine vordefinierte Menge an Sandboxes, die für die Ausführung eingehender Anforderungen bereit sind.
  * Sobald eine Sandbox verwendet wird, wird automatisch eine neue zugewiesen, um sie zu ersetzen.
* Falls keine vorab zugewiesenen Sandboxes für einen Abfrageoperator verfügbar sind, wird dieser gedrosselt.
  (siehe [Fehler](#errors), bis neue Sandboxen zugewiesen werden (kann je nach Sku und verfügbaren Ressourcen auf dem Datenknoten bis zu 10-15 Sekunden pro Sandbox dauern).

## <a name="limitations"></a>Einschränkungen

Einige dieser Einschränkungen können mithilfe einer [Sandboxrichtlinie](../management/sandboxpolicy.md)auf Clusterebene für jede Art von Sandbox gesteuert werden.

* **Anzahl der Sandboxes pro Knoten:** Die Anzahl der Sandboxes pro Knoten ist begrenzt.
  * Anfragen, die auf einen Zustand stoßen, in dem keine Sandbox verfügbar ist, werden gedrosselt.
* **Netzwerk:** Eine Sandbox kann nicht mit einer Ressource auf der VM oder außerhalb der VM interagieren.
  * Eine Sandbox kann nicht mit einer anderen Sandbox interagieren.
* **PROZESSOR:** Die maximale CPU-Rate, die eine Sandbox von den Prozessoren `50%`des Hosts verbrauchen kann, ist begrenzt (standardmäßig auf ).
  * Wenn diese Grenze erreicht ist, wird die CPU-Auslastung der Sandbox gedrosselt, die Ausführung wird jedoch fortgesetzt.
* **Speicher:** Die maximale RAM-Menge, die eine Sandbox vom RAM des `20GB`Hosts verbrauchen kann, ist begrenzt (standardmäßig auf ).
  * Das Erreichen dieses Limits führt zum Beenden der Sandbox (und zu einem Abfrageausführungsfehler).
* **Datenträger:** Eine Sandbox verfügt über ein eindeutiges Verzeichnis, für das sie nicht auf das Dateisystem des Hosts zugreifen kann.
  * Dieser Ordner bietet Zugriff auf das Image/Paket, das der Art der Sandbox entspricht (z. B. das nicht anpassbare Python- oder R-Paket).
* **Kinderprozesse:** Die Sandbox ist für das Laichen von untergeordneten Prozessen blockiert.

> [!NOTE]
> Die Ressourcennutzung der Sandbox hängt nicht nur von der Größe der Daten ab, die als Teil der Anforderung verarbeitet werden, sondern auch von der Logik, die in der Sandbox ausgeführt wird, und von der Implementierung von Bibliotheken, die von ihr verwendet werden.
> (z.B. für `python` `r` die und Plugins, letzteres bedeutet das vom Benutzer bereitgestellte Skript und die Python- oder R-Bibliotheken, die es zur Laufzeit verwendet)

## <a name="errors"></a>Errors

|Code                      |`Message`                                                                                        |Möglicher Grund                                                                                                    |
|--------------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|Die Sandkastenabfrage wurde aufgrund von Drosselungen abgebrochen. Der Erneutversuch nach einem Backoff könnte erfolgreich sein   |Auf dem Zielknoten sind keine Sandboxes verfügbar. Neue Sandkästen sollen in wenigen Sekunden verfügbar sein         |
|E_SB_QUERY_THROTTLED_ERROR|Sandboxen der Art '''''' wurden noch nicht initialisiert                                       |Die Sandbox-Richtlinie hat sich kürzlich geändert. Neue Sandboxen, die der neuen Richtlinie gehorchen, werden in wenigen Sekunden verfügbar sein|