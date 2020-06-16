---
title: 'Erfassung von Streamingvorgängen und Schema Änderungen: Azure Daten-Explorer'
description: In diesem Artikel werden die Optionen zum Behandeln von Schema Änderungen mit der streamingansung in Azure Daten-Explorer erläutert.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: f1ab3d012be7751eba33447b325748859509af00
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784564"
---
# <a name="streaming-ingestion-and-schema-changes"></a>Streamingerfassung und Schema Änderungen

## <a name="background"></a>Hintergrund

Cluster Knoten Cache Schema von Datenbanken, die Daten über die streamingerfassung empfangen. Dieser Prozess optimiert die Leistung und Auslastung von Cluster Ressourcen, kann jedoch zu Weiterleitungs Verzögerungen führen, wenn Schema Änderungen vorgenommen werden.

Beispiele für Schema Änderungen:

* Erstellen und Löschen von Datenbanken und Tabellen
* Hinzufügen, entfernen, erneutes typisieren oder Umbenennen der Spalten der Tabelle
* Hinzufügen oder Entfernen von vorab erstellten Erfassungs Zuordnungen
* Hinzufügen, entfernen oder Ändern von Richtlinien

Wenn Schema Änderungen und streamingerfassungs-Flows nicht koordiniert sind, können einige der streamingerfassungs Anforderungen fehlschlagen. Zu den Fehlern können Schema bezogene Fehler oder das Einfügen unvollständiger oder verzerrter Daten in die Tabelle gehören.
Beim Implementieren der Anwendung für die benutzerdefinierte Erfassung wird dringend empfohlen, die Schema bezogenen Fehler zu behandeln, indem Sie für einen begrenzten Zeitraum Wiederholungen ausführen oder Daten von den fehlgeschlagenen Anforderungen über in der Warteschlange eingereihte Erfassungsmethoden erneut ausführen.

## <a name="clearing-the-schema-cache"></a>Löschen des Schema Caches

Reduzieren Sie die Auswirkungen der propagierungs Verzögerung, indem Sie den Schema Cache auf den Cluster Knoten explizit löschen.
Löschen Sie den Schema Cache mithilfe eines der [Schemas zum Löschen](clear-schema-cache-command.md) von streamingzulagerungsverwaltung.
Wenn die streamingerfassung und Schema Änderungen koordiniert werden, können Sie Fehler und die damit verbundene Daten Verzerrung vollständig beseitigen. 

**Beispiel für koordiniertes Flow:**

1. Hält die streamingerfassung an.
1. Warten Sie, bis alle ausstehenden Streaminganforderungen vollständig sind>
1. Führen Sie Schema Änderungen aus.
1. Geben Sie einen oder mehrere `.clear cache streaming ingestion` Schema Befehle aus. 
    * Wiederholen bis zum erfolgreichen Abschluss und alle Zeilen in der Befehlsausgabe geben einen Erfolg an
1. Fortsetzen der streamingansung.

> [!NOTE]
> Die Verwendung von Schema Befehlen zum Löschen von Cache Streaming kann häufig eine negative Auswirkung auf die Leistung der streamingerfassung haben.
