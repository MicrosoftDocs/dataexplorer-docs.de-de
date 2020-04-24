---
title: Kusto-Ingest-Clientbibliothek - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Kusto-Ingest-Clientbibliothek in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0fd86579f4ca6471903305ca9249b78bc03b8cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503127"
---
# <a name="kusto-ingest-client-library"></a>Kusto Ingest Client Library

## <a name="overview"></a>Übersicht
Die Kusto.Ingest-Bibliothek ist eine .NET 4.6.2-Bibliothek, die das Senden von Daten an den Kusto-Dienst ermöglicht.
Kusto.Ingest nimmt Abhängigkeiten von den folgenden Bibliotheken und SDKs auf:

* ADAL für AAD-Authentifizierung
* Azure Storage-Client
* [TBD: vollständige Liste der externen Abhängigkeiten]

Kusto-Aufnahmemethoden werden von der [IKustoIngestClient-Schnittstelle](kusto-ingest-client-reference.md#interface-ikustoingestclient) definiert und ermöglichen die Datenerfassung aus Stream, IDataReader, lokalen Dateien und Azure-Blobs sowohl im synchronen als auch im asynchronen Modus.

## <a name="ingest-client-flavors"></a>Aufnahme von Client-Geschmack
Konzeptionell gibt es zwei grundlegende Varianten des Ingest-Clients: Queued und Direct.

### <a name="queued-ingestion"></a>In der Warteschlange erhaltene Einnahme
Dieser Modus wird von [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)definiert und beschränkt die Clientcodeabhängigkeit vom Kusto-Dienst. Die Erfassung erfolgt durch Veröffentlichen einer Kusto-Aufnahmenachricht in einer Azure-Warteschlange, die dann vom Kusto Data Management (Ingestion)-Dienst erfasst wird. Alle Zwischenspeicherartefakte werden vom Aufnahmeclient unter Verwendung der vom Kusto Data Management-Dienst zugewiesenen Ressourcen erstellt.

**Zu den Vorteilen des Queued-Modus** gehören (aber nicht beschränkt auf):

* Entkopplung des Datenerfassungsprozesses vom Kusto Engine-Service
* Ermöglicht das Beibehalten von Aufnahmeanforderungen, wenn der Kusto Engine -Dienst (oder Dieszuser-Dienst) nicht verfügbar ist
* Ermöglicht eine effiziente und steuerbare Aggregation eingehender Daten durch den Insetion-Dienst und verbessert die Leistung
* Ermöglicht dem Kusto-Insetion-Dienst die Erfassungslast des Kusto Engine-Dienstes zu verwalten
* Der Kusto-Erfassungsdienst wird bei bedarf vorübergehenden Einnahmefehlern erneut versucht (z. B. XStore-Einschränkung)
* Bietet einen praktischen Mechanismus, um den Fortschritt und das Ergebnis jeder Aufnahmeanforderung zu verfolgen

Das folgende Diagramm beschreibt die Interaktion des Warteschlangen-Aufnahmeclients mit Kusto:

![alt text](../images/queued-ingest.jpg "Queued-Ingest")

### <a name="direct-ingestion"></a>Direkte Einnahme
Dieser Modus, der von IKustoDirectIngestClient definiert wird, erzwingt die direkte Interaktion mit dem Kusto Engine-Dienst. In diesem Modus moderiert oder verwaltet der Kusto-Erfassungsdienst die Daten nicht. Jede Aufnahmeanforderung im Direct-Modus wird `.ingest` schließlich in den Befehl übersetzt, der direkt im Kusto Engine-Dienst ausgeführt wird.
Das folgende Diagramm beschreibt die Direkte Aufnahmeclientinteraktion mit Kusto:

![alt text](../images/direct-ingest.jpg "direkt-ingest")

> [!NOTE]
> Der Direktmodus wird für Produktionsklasse-Aufnahmelösungen nicht empfohlen.

**Zu den Vorteilen des Direct-Modus** gehören:

* Niedrige Latenz (es gibt keine Aggregation). Eine geringe Latenz kann jedoch auch mit Queued-Aufnahme erreicht werden.
* Wenn synchrone Methoden verwendet werden, gibt die Methodenvervollständigung das Ende des Aufnahmevorgangs an.

**Zu den Nachteilen des Direktmodus** gehören:

* Der Clientcode muss jede Retry- oder Fehlerbehandlungslogik implementieren.
* Verunstungen sind nicht möglich, wenn der Kusto Engine-Dienst nicht verfügbar ist
* Der Clientcode kann den Kusto Engine-Dienst mit Aufnahmeanforderungen überfordern, da er die Engine-Servicekapazität nicht kennt.

## <a name="ingestion-best-practices"></a>Best Practices für die Verfilmung

### <a name="general"></a>Allgemein
[Die Best Practices für die Verfilmung](kusto-ingest-best-practices.md) bieten COGs und Durchsatz-POV bei der Aufnahme.

### <a name="thread-safety"></a>Threadsicherheit
Kusto Ingest Client-Implementierungen sind threadsicher und sollen wiederverwendet werden. Es ist nicht erforderlich, `KustoQueuedIngestClient` eine Instanz der Klasse für jede oder sogar mehrere Aufnahmevorgänge zu erstellen. Pro Zielkusto-Cluster pro Benutzerprozess `KustoQueuedIngestClient` ist eine einzelne Instanz erforderlich. Das Ausführen mehrerer Instanzen ist kontraproduktiv und kann den Data Management-Cluster aufstellen.

### <a name="supported-data-formats"></a>Unterstützte Datenformate
Wenn Sie die systemeigene Aufnahme verwenden, wenn Sie es nicht bereits vorhanden sind, laden Sie die Daten in ein oder mehrere Azure Storage-Blobs hoch. Derzeit unterstützte Blobformate werden im Thema [Unterstützte Datenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) dokumentiert.

### <a name="schema-mapping"></a>Schemazuordnung
[Schemazuordnungen](../../management/mappings.md) helfen beim deterministischen Binden von Quelldatenfeldern an Zieltabellenspalten.

## <a name="usage-and-further-reading"></a>Nutzung und weitere Lektüre

* Wie oben beschrieben, sollte die empfohlene Grundlage für nachhaltige und hochskalierende Aufnahmelösungen für Kusto der **KustoQueuedIngestClient**sein.
* Um unnötige Auslastung Ihres Kusto-Dienstes zu minimieren, wird empfohlen, dass pro Prozess pro Kusto-Cluster eine einzelne Instanz des Kusto Ingest-Clients (Queued oder Direct) verwendet wird. Die Clientimplementierung von Kusto Ingest ist threadsicher und vollständig wieder eintretend.

### <a name="ingestion-permissions"></a>Erfassungsberechtigungen
* [Kusto-Erfassungsberechtigungen](kusto-ingest-client-permissions.md) erläutert die Berechtigungseinrichtung, die für eine erfolgreiche Einnahme mit dem Paket Kusto.Ingest erforderlich ist.

### <a name="kustoingest-library-reference"></a>Kusto.Ingest Bibliotheksreferenz
* [Kusto.Ingest Client Reference](kusto-ingest-client-reference.md) enthält einen vollständigen Verweis auf Kusto Aufnahme Client-Schnittstellen und -Implementierungen.<BR>Dort finden Sie Informationen zum Erstellen von Aufnahmeclients, zur Erweiterung von Aufnahmeanfragen, zum Verwalten des Aufnahmefortschritts und mehr
* [Kusto.Ingest Operation Status](kusto-ingest-client-status.md) erklärt **KustoQueuedIngestClient-Einrichtungen** zum Nachverfolgen des Aufnahmestatus
* [Kusto.Ingest Errors](kusto-ingest-client-errors.md) dokumentiert Kusto Ingest Client-Fehler und Ausnahmen
* [Kusto.Ingest Beispiele](kusto-ingest-client-examples.md) präsentiert Codeausschnitte, die verschiedene Techniken zur Aufnahme von Daten in Kusto demonstrieren

### <a name="data-ingestion-rest-apis"></a>REST-APIs zur Datenerfassung
[Datenerfassung ohne Kusto.Ingest Library](kusto-ingest-client-rest.md) erklärt, wie Queued Kusto-Aufnahme mithilfe von Kusto REST-APIs implementiert wird und ohne Abhängigkeit von der Kusto.Ingest-Bibliothek zu verwenden.

