---
title: 'Sand Fächer: Azure Daten-Explorer'
description: Dieser Artikel beschreibt Sand Fächer in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 5732d0fa9773d0c8fe5420026cb855d1040b8706
ms.sourcegitcommit: 42cc7d11f41a5bfa9e021764b044dcd68d99a258
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93403764"
---
# <a name="sandboxes"></a>Sandboxes

Der Daten-Engine-Dienst von Kusto kann Sandkästen für bestimmte Flows ausführen, die eine sichere Isolation erfordern.
Beispiele für diese Flows sind benutzerdefinierte Skripts, die mit dem [python-Plug](../query/pythonplugin.md) -in oder dem [R-Plug](../query/rplugin.md)-in ausgeführt werden.

Um diese Sandkästen auszuführen, verwendet Kusto eine entwickelte Version des [Drawbridge](https://www.microsoft.com/research/project/drawbridge/) -Projekts von Microsoft. Diese Lösung wird von anderen Microsoft-Diensten verwendet, um benutzerdefinierte Objekte in einer mehr Instanzen fähigen Umgebung auszuführen.

Flows, die in Sandkästen ausgeführt werden, sind nicht isoliert. Sie sind auch lokal (in der Nähe der Daten). Dies bedeutet, dass keine zusätzliche Latenz für Remote Aufrufe hinzugefügt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Für die Daten-Engine ist die Datenträger [Verschlüsselung](../../security.md#data-encryption) nicht aktiviert.
  * Die Unterstützung für beide Funktionen, die parallel ausgeführt werden, wird in Zukunft erwartet.
* Die erforderlichen Pakete (Images) zum Ausführen der Sandkästen werden für alle Knoten der Daten-Engine bereitgestellt und erfordern die Ausführung dedizierter SSD-Speicher.
  * Die geschätzte Größe beträgt 20 GB, d. h. ungefähr 2,5% der SSD-Kapazität einer D14_v2-VM, z. b. 0,7% der SSD-Kapazität einer L16_v1-VM.
  * Dies wirkt sich auf die Datenkapazität des Clusters aus und wirkt sich möglicherweise auf die [Kosten](https://azure.microsoft.com/pricing/details/data-explorer) des Clusters aus.

## <a name="runtime"></a>Typ

* Ein Sandkasten Abfrage Operator kann für die Ausführung mindestens ein Sandkasten verwenden.
  * Ein Sandkasten wird nur für eine einzelne Ausführung verwendet, nicht für mehrere Ausführungen freigegeben und wird nach Abschluss der Ausführung verworfen.
  * Sand Fächer werden auf einem Knoten verzögert initialisiert, wenn eine Abfrage zum ersten Mal einen Sandkasten für ihre Ausführung benötigt.
    * Dies bedeutet, dass die erste Ausführung eines Plug-ins, das Sandkästen auf einem Knoten verwendet, eine kurze Aufwärmphase beinhaltet.
  * Wenn ein Knoten neu gestartet wird, z. b. als Teil eines Dienst Upgrades, werden alle ausgelaufenden Sandkästen darauf verworfen.
* Jeder Knoten behält eine vordefinierte Anzahl von Sandkästen bei, die zum Ausführen eingehender Anforderungen bereit sind.
  * Nachdem ein Sandkasten verwendet wurde, wird automatisch ein neuer zum Ersetzen zur Verfügung gestellt.
* Wenn keine vorab zugeordneten Sand Fächer zum Verarbeiten eines Abfrage Operators verfügbar sind, wird dieser gedrosselt, bis neue Sand Fächer verfügbar sind. Weitere Informationen finden Sie unter [Fehler](#errors). Die Zuordnung neuer Sandbox kann je nach der SKU und den verfügbaren Ressourcen auf dem Datenknoten bis zu 10-15 Sekunden pro Sandbox dauern.

## <a name="limitations"></a>Einschränkungen

Einige Einschränkungen können mithilfe einer [Sandbox-Richtlinie](../management/sandboxpolicy.md)auf Cluster Ebene für jede Art von Sandbox gesteuert werden.

* **Anzahl der Sandkästen pro Knoten:** Die Anzahl der Sandkästen pro Knoten ist begrenzt.
  * Anforderungen, die durchgeführt werden, wenn kein verfügbarer Sandkasten vorhanden ist, werden gedrosselt.
* **Netzwerk:** Ein Sandkasten kann nicht mit einer Ressource auf dem virtuellen Computer (VM) oder außerhalb des Computers interagieren.
  * Ein Sandkasten kann nicht mit einem anderen Sandkasten interagieren.
* **CPU:** Die maximale CPU-Rate, die ein Sandkasten der Prozessoren seines Hosts belegen kann, ist begrenzt (Standardwert ist `50%` ).
  * Wenn der Grenzwert erreicht ist, wird die CPU-Auslastung des Sandkastens gedrosselt, die Ausführung wird jedoch fortgesetzt.
* Arbeits **Speicher:** Die maximale RAM-Größe, die ein Sandkasten für den Arbeitsspeicher seines Hosts belegen kann, ist begrenzt (Standardwert ist `20GB` ).
  * Das Erreichen des Limits führt zu einem Abbruch des Sandkastens und einem Fehler bei der Abfrage Ausführung.
* Datenträger **:** Einem Sandkasten ist ein eindeutiges und unabhängiges Verzeichnis angefügt. Der Zugriff auf das Dateisystem des Hosts ist nicht möglich.
  * Der eindeutige Ordner ermöglicht den Zugriff auf das Image/Paket, das mit dem Typ der Sandbox übereinstimmt. Beispielsweise das nicht anpassbare python-oder R-Paket.
* Untergeordnete **Prozesse:** Der Sandkasten ist daran gehindert, untergeordnete Prozesse zu erzeugen.

> [!NOTE]
> Die mit Sandbox verwendeten Ressourcen hängen nicht nur von der Größe der Daten ab, die als Teil der Anforderung verarbeitet werden, sondern auch von der Logik, die in der Sandbox ausgeführt wird, und von der Implementierung von Bibliotheken, die von ihr verwendet werden.
> Bei den `python` -und-Plug `r` -ins bedeutet das letztere beispielsweise, dass das vom Benutzer bereitgestellte Skript und die python-oder R-Bibliotheken zur Laufzeit verwendet werden.

## <a name="errors"></a>Errors

|ErrorCode                 |Status                     |`Message`                                                                                            |Möglicher Grund                                                                                                    |
|--------------------------|---------------------------|---------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|Toomanyrequests (429)      |Die Sandbox-Abfrage wurde aufgrund einer Drosselung abgebrochen. Erneuter Versuch, nachdem einige Backoff erfolgreich ausgeführt werden konnten   |Auf dem Zielknoten sind keine Sandkästen verfügbar. Neue Sand Fächer sollten in wenigen Sekunden verfügbar werden.         |
|E_SB_QUERY_THROTTLED_ERROR|Toomanyrequests (429)      |Sandboxes der Art "{Kind}" wurden noch nicht initialisiert.                                            |Die Sandbox Richtlinie wurde vor kurzem geändert. Neue Sand Fächer, die die neue Richtlinie einhalten, werden in wenigen Sekunden verfügbar sein.|
|                          |Internalserviceerror (520) |Die Sandbox-Abfrage wurde aufgrund eines Fehlers bei der Initialisierung von Sandkästen abgebrochen.                         |Unerwarteter Infrastruktur Fehler.                         |
