---
title: 'Sand Fächer: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt Sand Fächer in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 6c1f836596c27f0e2901e9f7b109d96aab89cdff
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373506"
---
# <a name="sandboxes"></a>Sandboxes

Der Daten-Engine-Dienst von Kusto kann Sandkästen zum Ausführen bestimmter Flows ausführen, für die eine sichere Isolation erforderlich ist.
Beispiele für diese Flows sind benutzerdefinierte Skripts, die mit dem python- [Plug](../query/pythonplugin.md) -in oder dem [R-Plug](../query/rplugin.md)-in ausgeführt werden.

Um diese Sand Fächer auszuführen, verwendet Kusto eine Weiterentwicklung des [Drawbridge](https://www.microsoft.com/research/project/drawbridge/) -Projekts von Microsoft. Diese Lösung wird von anderen Microsoft-Diensten verwendet, um benutzerdefinierte Objekte in einer mehr Instanzen fähigen Umgebung auszuführen.

Flows, die in Sandkästen ausgeführt werden, sind nicht nur isoliert, sondern auch lokal (in der Nähe der Daten), was bedeutet, dass keine zusätzliche Latenz für Remote Aufrufe hinzugefügt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Für die Daten-Engine darf die Datenträger [Verschlüsselung](../../security.md#data-encryption) **nicht** aktiviert sein.
  * Die Unterstützung für die parallele Ausführung beider Features wird in Zukunft erwartet.
* Die erforderlichen Pakete (Images) zum Ausführen der Sandkästen werden für alle Knoten der Daten-Engine bereitgestellt und erfordern dedizierten SSD-Speicherplatz, um ausgeführt werden zu können.
  * Die geschätzte Größe beträgt 20 GB, z. b. etwa 2,5% der SSD-Kapazität einer D14_v2-VM oder 0,7% der SSD-Kapazität einer L16_v1-VM.
  * Dies wirkt sich auf die Datenkapazität des Clusters aus und wirkt sich daher möglicherweise auf die [Kosten](https://azure.microsoft.com/pricing/details/data-explorer) des Clusters aus.

## <a name="runtime"></a>Typ

* Ein Sandkasten Abfrage Operator kann für die Ausführung mindestens ein Sandkasten verwenden.
  * Ein Sandkasten wird nur für eine einzelne Ausführung verwendet, nicht für mehrere Ausführungen freigegeben und wird nach Abschluss der Ausführung verworfen.
  * Sand Fächer werden auf einem Knoten verzögert initialisiert, wenn eine Abfrage zum ersten Mal einen Sandkasten zur Ausführung benötigt.
    * Dies bedeutet, dass die erste Ausführung eines Plug-ins, das Sandkästen auf einem Knoten verwendet, eine kurze Aufwärmphase beinhaltet.
  * Wenn ein Knoten neu gestartet wird (z. b. als Teil eines Dienst Upgrades), werden alle ausgelaufenden Sandkästen darauf verworfen.
* Jeder Knoten behält eine vordefinierte Anzahl von Sandkästen bei, die für die Ausführung eingehender Anforderungen bereit sind.
  * Nachdem ein Sandkasten verwendet wurde, wird automatisch eine neue zugeordnet.
* Falls keine vorab zugeordneten Sand Fächer zum Verarbeiten eines Abfrage Operators verfügbar sind, wird dieser gedrosselt.
  (Weitere Informationen finden Sie unter [Fehler](#errors), bis neue Sandkästen zugewiesen sind (kann je nach SKU und den verfügbaren Ressourcen auf dem Datenknoten bis zu 10-15 Sekunden pro Sandbox dauern).

## <a name="limitations"></a>Einschränkungen

Einige dieser Einschränkungen können mithilfe einer [Sandbox-Richtlinie](../management/sandboxpolicy.md)auf Cluster Ebene für jede Art von Sandbox gesteuert werden.

* **Anzahl der Sandkästen pro Knoten:** Die Anzahl der Sandkästen pro Knoten ist begrenzt.
  * Anforderungen, bei denen es sich um einen Status handelt, bei dem keine verfügbare Sandbox verfügbar ist, werden gedrosselt
* **Netzwerk:** Ein Sandkasten kann nicht mit einer Ressource auf dem virtuellen Computer oder außerhalb der VM interagieren.
  * Ein Sandkasten kann nicht mit einem anderen Sandkasten interagieren.
* **CPU:** Die maximale CPU-Rate, die ein Sandkasten von seinen Host Prozessoren belegen kann, ist begrenzt (standardmäßig auf `50%` ).
  * Wenn dieser Grenzwert erreicht wird, wird die CPU-Auslastung des Sandkastens gedrosselt, die Ausführung wird jedoch fortgesetzt.
* Arbeits **Speicher:** Die maximale RAM-Größe, die ein Sandkasten für den Arbeitsspeicher seines Hosts belegen kann, ist begrenzt (standardmäßig auf `20GB` ).
  * Das Erreichen dieses Limits führt zu einem Abbruch des Sandkastens (und einem Fehler bei der Abfrage Ausführung).
* Datenträger **:** Einem Sandkasten ist ein eindeutiges Verzeichnis angefügt, für das er nicht auf das Dateisystem des Hosts zugreifen kann.
  * Dieser Ordner ermöglicht den Zugriff auf das Image/Paket, das mit der Art der Sandbox übereinstimmt (z. b. das nicht anpassbare python-oder R-Paket).
* Untergeordnete **Prozesse:** Der Sandkasten ist daran gehindert, untergeordnete Prozesse zu erzeugen.

> [!NOTE]
> Die Ressourcennutzung von Sandbox hängt nicht nur von der Größe der Daten ab, die als Teil der Anforderung verarbeitet werden, sondern auch von der Logik, die in der Sandbox ausgeführt wird, und von der Implementierung der Bibliotheken, die von ihr verwendet werden.
> (z. b. für die `python` -und-Plug `r` -ins bezeichnet das letztere das vom Benutzer bereitgestellte Skript und die python-oder R-Bibliotheken, die zur Laufzeit genutzt werden

## <a name="errors"></a>Fehler

|Code                      |`Message`                                                                                        |Möglicher Grund                                                                                                    |
|--------------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|Die Sandbox-Abfrage wurde aufgrund einer Drosselung abgebrochen. Erneuter Versuch, nachdem einige Backoff erfolgreich ausgeführt werden konnten   |Auf dem Zielknoten sind keine Sandkästen verfügbar. Neue Sand Fächer sollten in wenigen Sekunden verfügbar werden.         |
|E_SB_QUERY_THROTTLED_ERROR|Sandboxes der Art "{Kind}" wurden noch nicht initialisiert.                                       |Die Sandbox Richtlinie wurde vor kurzem geändert. Neue Sand Fächer, die die neue Richtlinie einhalten, werden in wenigen Sekunden verfügbar sein.|
