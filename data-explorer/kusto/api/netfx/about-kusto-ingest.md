---
title: Kusto-Erfassungs Client Bibliothek-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Kusto-Erfassungs Client Bibliothek in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/24/2020
ms.openlocfilehash: c43c6c09ad6da90685c56dc47ebe257a55eaede6
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862138"
---
# <a name="kusto-ingest-client-library"></a>Kusto-Erfassungs Client Bibliothek

## <a name="overview"></a>Übersicht
Die Kusto. Erfassungs Bibliothek ist eine .NET 4.6.2-Bibliothek, die das Senden von Daten an den Kusto-Dienst ermöglicht.
Kusto. inererfassung nimmt Abhängigkeiten von folgenden Bibliotheken und SDKs an:

* Adal für Aad-Authentifizierung
* Azure Storage-Client

Kusto-Erfassungsmethoden werden von der [ikustoingestclient](kusto-ingest-client-reference.md#interface-ikustoingestclient) -Schnittstelle definiert und ermöglichen die Erfassung von Daten aus Stream, IDataReader, lokalen Dateien und Azure-BLOBs im synchronen und asynchronen Modus.

## <a name="ingest-client-flavors"></a>Erfassen von Client-Varianten
Konzeptionell gibt es zwei grundlegende Varianten des Erfassungs Clients: in der Warteschlange und direkt.

### <a name="queued-ingestion"></a>Erfassung in Warteschlange
Dieser Modus wird von [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)definiert und schränkt die Client Code Abhängigkeit für den Kusto-Dienst ein. Die Erfassung erfolgt durch das Bereitstellen einer Kusto-Erfassungs Nachricht in einer Azure-Warteschlange, die dann vom Kusto-Datenverwaltung Dienst (Erfassung) abgerufen wird. Alle Zwischenspeicher Artefakte werden vom Erfassungs Client mithilfe der Ressourcen erstellt, die von Kusto-Datenverwaltung Dienst zugeordnet werden.

Zu **den Vorteilen des Warteschlangen Modus** gehören (aber nicht beschränkt auf):

* Entkopplung des Daten Erfassungs Prozesses vom Kusto-Engine-Dienst
* Ermöglicht die persistente Erfassung von Anforderungen, wenn der Kusto-Engine-Dienst (oder der Erfassungs Dienst) nicht verfügbar ist.
* Ermöglicht eine effiziente und steuerbare Aggregation eingehender Daten durch den Erfassungs Dienst, um die Leistung zu verbessern.
* Ermöglicht dem Kusto-Erfassungs Dienst das Verwalten der Erfassungs Last für den Kusto-Engine-Dienst.
* Der Kusto-Erfassungs Dienst versucht bei vorübergehenden Erfassungs Fehlern (z. b. bei der XStore-Drosselung) nach Bedarf.
* Stellt eine bequeme Methode zum Nachverfolgen des Fortschritts und Ergebnisses der einzelnen Erfassungs Anforderungen bereit.

Das folgende Diagramm zeigt die Interaktion mit dem Erfassungs Client in der Warteschlange mit Kusto:

![alt text](../images/queued-ingest.jpg "in Warteschlange eingereiht")

### <a name="direct-ingestion"></a>Direkte Erfassung
Dieser Modus wird von ikustodirectingestclient definiert und erzwingt eine direkte Interaktion mit dem Kusto-Engine-Dienst. In diesem Modus werden die Daten vom Kusto-Erfassungs Dienst nicht gemäßigt oder verwaltet. Jede Erfassungs Anforderung im direkten Modus wird schließlich in den `.ingest` Befehl übersetzt, der direkt auf dem Kusto-Engine-Dienst ausgeführt wird.
Im folgenden Diagramm wird die direkte Erfassung von Client Interaktionen mit Kusto erläutert:

![alt text](../images/direct-ingest.jpg "Direkte Erfassung")

> [!NOTE]
> Der direkte Modus wird für Erfassungs Lösungen in der Produktionsumgebung nicht empfohlen.

**Zu den Vorteilen des direkten Modus** gehören:

* Geringe Latenz (es gibt keine Aggregation). Eine geringe Latenz kann jedoch auch mit der in der Warteschlange befindlichen Erfassung erreicht werden.
* Wenn synchrone Methoden verwendet werden, gibt der Methoden Abschluss das Ende des Erfassungs Vorgangs an.

**Zu den Nachteilen des direkten Modus** gehören:

* Der Client Code muss jede beliebige Wiederholungs-oder Fehler Behandlungs Logik implementieren.
* Ingestionen sind nicht möglich, wenn der Kusto-Engine-Dienst nicht verfügbar ist.
* Der Client Code kann den Kusto-Engine-Dienst mit Erfassungs Anforderungen überlasten, da er die Kapazität des Engine-Dienstanbieter nicht kennt.

## <a name="ingestion-best-practices"></a>Bewährte Methoden für die Erfassung

### <a name="general"></a>Allgemein
[Bewährte Methoden](kusto-ingest-best-practices.md) für die Erfassung bieten COGS und Durchsatz-POV bei der Erfassung.

### <a name="thread-safety"></a>Threadsicherheit
Kusto-Erfassungs Client Implementierungen sind Thread sicher und sollen wieder verwendet werden. Es ist nicht erforderlich, eine Instanz der `KustoQueuedIngestClient` -Klasse für jeden oder sogar mehrere Erfassungs Vorgänge zu erstellen. Pro Ziel-Kusto-Cluster pro Benutzer Prozess ist eine einzelne Instanz von `KustoQueuedIngestClient` erforderlich. Das Ausführen mehrerer Instanzen ist eine gegen produktive Produktivität und kann den Datenverwaltung Cluster in den Betrieb setzen.

### <a name="supported-data-formats"></a>Unterstützte Datenformate
Wenn Sie die systemeigene Erfassung verwenden, wenn Sie nicht bereits vorhanden ist, laden Sie die Daten in mindestens einen Azure Storage blobmodus hoch. Zurzeit werden unterstützte BLOB-Formate im Thema [Unterstützte Datenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) dokumentiert.

### <a name="schema-mapping"></a>Schemazuordnung
[Schema](../../management/mappings.md) Zuordnungen helfen bei der deterministisch Bindung von Quelldaten Feldern an Ziel Tabellen Spalten.

## <a name="usage-and-further-reading"></a>Verwendung und weitere Informationen

* Wie oben beschrieben, sollte die empfohlene Grundlage für nachhaltige und hochskalierbare Erfassungs Lösungen für Kusto der **kustoqueuedingestclient**sein.
* Um die unnötige Auslastung Ihres Kusto-Dienstanbieter zu minimieren, empfiehlt es sich, eine einzelne Instanz des Kusto-Erfassungs Clients (in der Warteschlange oder direkt) pro Prozess pro Kusto-Cluster zu verwenden. Die Kusto-Erfassungs Client Implementierung ist Thread sicher und vollständig Wiedereintritts fähig.

### <a name="ingestion-permissions"></a>Erfassungs Berechtigungen
* [Kusto](kusto-ingest-client-permissions.md) -Erfassungs Berechtigungen: erläutert die Berechtigungen, die für eine erfolgreiche Erfassung mit dem Kusto.-Erfassungs Paket erforderlich sind.

### <a name="kustoingest-library-reference"></a>Kusto. inerfassungs-Bibliotheks Referenz
* [Kusto. Erfassungs Client Verweis](kusto-ingest-client-reference.md) enthält eine umfassende Referenz zu Kusto-Erfassungs Client Schnittstellen und-Implementierungen.<BR>Dort finden Sie die Informationen zum Erstellen von Erfassungs Clients, zum Erweitern von Erfassungs Anforderungen, zum Verwalten des Erfassungs Fortschritts und mehr.
* [Kusto. Erfassungs Vorgangs Status](kusto-ingest-client-status.md) erläutert **kustoqueuedingestclient** -Funktionen zum Nachverfolgen des Erfassungs Status
* [Kusto.](kusto-ingest-client-errors.md) Erfassungs Fehler dokumentiert die Erfassung von Client Fehlern und Ausnahmen durch Kusto.
* [Kusto.](kusto-ingest-client-examples.md) Erfassungs Beispiele zeigt Code Ausschnitte, die verschiedene Techniken zum Erfassen von Daten in Kusto veranschaulichen.

### <a name="data-ingestion-rest-apis"></a>Rest-APIs für die Datenerfassung
[Datenerfassung ohne Kusto.](kusto-ingest-client-rest.md) Erfassungs Bibliothek erläutert das Implementieren der Kusto-Erfassung in der Warteschlange mithilfe von Kusto-Rest-APIs und ohne Abhängigkeit von der Kusto. Erfassungs Bibliothek.
