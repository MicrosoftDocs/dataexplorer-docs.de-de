---
title: Kusto-Erfassungs Client Bibliothek-Azure Daten-Explorer
description: Dieser Artikel beschreibt die Kusto-Erfassungs Client Bibliothek in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/18/2020
ms.openlocfilehash: c5a0bd91df6e12d90436e3b27a2b55021668117a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874340"
---
# <a name="kusto-ingest-client-library"></a>Kusto-Erfassungs Client Bibliothek 

`Kusto.Ingest` Library ist eine .NET 4.6.2-Bibliothek zum Senden von Daten an den Kusto-Dienst.
Es werden Abhängigkeiten von folgenden Bibliotheken und sDas benötigt:

* Adal für Azure AD Authentifizierung
* Azure-Speicher Client

Die Erfassungsmethoden werden von der [ikustoingestclient](kusto-ingest-client-reference.md#interface-ikustoingestclient) -Schnittstelle definiert.  Die Methoden verarbeiten die Datenerfassung aus Stream, IDataReader, lokalen Dateien und Azure-blobdateien in synchronen und asynchronen Modi.

## <a name="ingest-client-flavors"></a>Erfassen von Client-Varianten

Es gibt zwei grundlegende Varianten des Erfassungs Clients: in der Warteschlange und direkt.

### <a name="queued-ingestion"></a>Erfassung aus der Warteschlange

Der in der Warteschlange befindliche Erfassungs Modus, der durch [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)definiert ist, schränkt die Abhängigkeit des Client Codes auf dem Kusto-Dienst ein. Die Erfassung erfolgt durch das Bereitstellen einer Kusto-Erfassungs Nachricht in einer Azure-Warteschlange, die dann vom Kusto-Datenverwaltung Dienst (Erfassung) abgerufen wird. Alle Zwischenspeicher Elemente werden vom Erfassungs Client mithilfe der Ressourcen aus dem Kusto-Datenverwaltung-Dienst erstellt.

**Der in der Warteschlange befindliche Modus bietet folgende Vorteile:**

* Entkoppelt des Daten Erfassungs Prozesses vom Kusto-Engine-Dienst
* Ermöglicht die Erfassung von Anforderungen, wenn der Kusto-Engine-Dienst (oder der Erfassungs Dienst) nicht verfügbar ist.
* Verbesserung der Leistung durch effiziente und steuerbare Aggregation eingehender Daten durch den Erfassungs Dienst 
* Ermöglicht dem Kusto-Erfassungs Dienst das Verwalten der Erfassungs Last für den Kusto-Engine-Dienst.
* Führt bei Bedarf bei vorübergehenden Erfassungs Fehlern, z. b. bei der XStore-Drosselung, einen Wiederholungsversuch für den Kusto-Erfassungs Dienst aus.
* Stellt eine bequeme Methode zum Nachverfolgen des Fortschritts und Ergebnisses der einzelnen Erfassungs Anforderungen bereit.

Das folgende Diagramm zeigt die Interaktion mit dem Erfassungs Client in der Warteschlange mit Kusto:

:::image type="content" source="../images/about-kusto-ingest/queued-ingest.png" alt-text="in Warteschlange eingereiht":::
 
### <a name="direct-ingestion"></a>Direkte Erfassung

Der von ikustodirectingestclient definierte direkt Erfassungs Modus erzwingt eine direkte Interaktion mit dem Kusto-Engine-Dienst. In diesem Modus werden die Daten vom Kusto-Erfassungs Dienst nicht gemäßigt oder verwaltet. Jede Erfassungs Anforderung wird schließlich in den `.ingest` Befehl übersetzt, der direkt für den Kusto-Engine-Dienst ausgeführt wird.

Im folgenden Diagramm wird die direkte Erfassung von Client Interaktionen mit Kusto erläutert:

:::image type="content" source="../images/about-kusto-ingest/direct-ingest.png" alt-text="Direkte Erfassung":::

> [!NOTE]
> Der direkte Modus wird für Erfassungs Lösungen in der Produktionsumgebung nicht empfohlen.

**Zu den Vorteilen des direkten Modus gehören:**

* Geringe Latenz und keine Aggregation. Eine geringe Latenz kann jedoch auch mit der in der Warteschlange befindlichen Erfassung erreicht werden.
* Wenn synchrone Methoden verwendet werden, gibt der Methoden Abschluss das Ende des Erfassungs Vorgangs an.

**Zu den Nachteilen des direkten Modus gehören:**

* Der Client Code muss jede beliebige Wiederholungs-oder Fehler Behandlungs Logik implementieren.
* Ingestionen sind nicht möglich, wenn der Kusto-Engine-Dienst nicht verfügbar ist.
* Der Client Code kann den Kusto-Engine-Dienst mit Erfassungs Anforderungen überlasten, da er die Kapazität des Engine-Dienstanbieter nicht kennt.

## <a name="ingestion-best-practices"></a>Bewährte Methoden für die Erfassung

[Bewährte Methoden](kusto-ingest-best-practices.md) für die Erfassung bieten COGS und Durchsatz-POV bei der Erfassung.

* **Thread Sicherheit-** Kusto-Erfassungs Client Implementierungen sind Thread sicher und sollen wieder verwendet werden. Es ist nicht erforderlich, `KustoQueuedIngestClient` für jeden oder mehrere Erfassungs Vorgänge eine Instanz der-Klasse zu erstellen. `KustoQueuedIngestClient`Pro Ziel-Kusto-Cluster pro Benutzer Prozess ist eine einzelne Instanz von erforderlich. Das Ausführen mehrerer Instanzen ist eine gegenproduktivität und kann zu DOS im Datenverwaltung Cluster führen.

* **Unterstützte Datenformate:** Wenn Sie die native Erfassung verwenden, wenn Sie nicht bereits vorhanden ist, laden Sie die Daten in mindestens einen Azure Storage-BLOB hoch. Unter [stützte Datenformate](../../../ingestion-supported-formats.md)werden derzeit unterstützte BLOB-Formate dokumentiert.

* **Schema Zuordnung-** 
 [Schema](../../management/mappings.md) Zuordnungen helfen bei der deterministisch Bindung von Quelldaten Feldern an Ziel Tabellen Spalten.

* **Erfassungs Berechtigungen:** 
 In den [Kusto](kusto-ingest-client-permissions.md) -Erfassungs Berechtigungen wird die Berechtigungs Einrichtung erläutert, die für eine erfolgreiche Erfassung mit dem Paket erforderlich ist `Kusto.Ingest` .

* **Verwendung:** Wie bereits beschrieben, sollte die empfohlene Grundlage für nachhaltige und hochskalierbare Erfassungs Lösungen für Kusto der **kustoqueuedingestclient**sein.
Um die unnötige Auslastung Ihres Kusto-Dienstanbieter zu minimieren, empfiehlt es sich, eine einzelne Instanz des Kusto-Erfassungs Clients (in der Warteschlange oder direkt) pro Prozess und pro Kusto-Cluster zu verwenden. Die Kusto-Erfassungs Client Implementierung ist Thread sicher und vollständig Wiedereinstiegs fähig.

## <a name="next-steps"></a>Nächste Schritte

* [Kusto. Erfassungs Client Verweis](kusto-ingest-client-reference.md) enthält eine umfassende Referenz zu Kusto-Erfassungs Client Schnittstellen und-Implementierungen. Hier finden Sie Informationen zum Erstellen von Erfassungs Clients, zum Erweitern von Erfassungs Anforderungen, zum Verwalten des Erfassungs Fortschritts und mehr.

* [Kusto. Erfassungs Vorgangs Status](kusto-ingest-client-status.md) erläutert die kustoqueuedingestclient-Funktionen zum Nachverfolgen des Erfassungs Status

* [Kusto.](kusto-ingest-client-errors.md) Erfassungs Fehler beschreibt das Erfassen von Client Fehlern und Ausnahmen in Kusto.

* [Kusto.](kusto-ingest-client-examples.md) Erfassungs Beispiele zeigt Code Ausschnitte, die verschiedene Techniken zum Erfassen von Daten in Kusto veranschaulichen.

* [Datenerfassung ohne Kusto.](kusto-ingest-client-rest.md) Erfassungs Bibliothek erläutert das Implementieren der Kusto-Erfassung in der Warteschlange, mithilfe von Kusto-Rest-APIs und ohne Abhängigkeit von der `Kusto.Ingest` Bibliothek.

