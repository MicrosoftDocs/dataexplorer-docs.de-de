---
title: Kusto.Ingest-Referenz - Erfassungsberechtigungen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto.Ingest Reference - Ingestion Permissions in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 198d562f880b74f6df4c6318f72213ee865f8f28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502889"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto.Ingest Referenz - Einnahmeberechtigungen
In diesem Artikel wird erläutert, welche Berechtigungen für `Native` Ihren Dienst eingerichtet werden müssen, damit die Erfassung funktioniert.



## <a name="prerequisites"></a>Voraussetzungen
* In diesem Artikel wird erläutert, wie [Sie Kusto-Steuerbefehle](../../management/security-roles.md) verwenden, um Autorisierungseinstellungen für Kusto-Dienste und -Datenbanken anzuzeigen und zu ändern.
* Die folgenden AAD-Anwendungen werden in den folgenden Beispielen als Beispielprinzipale verwendet:
    * Test AAD App (2a904276-1234-5678-9012-66fc53add60b;microsoft.com)
    * Kusto Interne Aufnahme AAD App (76263cdb-1234-5678-9012-545644e9c404;microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>Aufnahmeberechtigungsmodell für die Aufnahme in die Warteschlange
Definiert in [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), schränkt dieser Modus die Clientcodeabhängigkeit vom Kusto-Dienst ein. Die Aufnahme erfolgt durch Das Posten einer Kusto-Aufnahmenachricht in einer Azure-Warteschlange, die wiederum von Kusto Data Management (auch als "Kusto Data Management" (auch bezeichnet) erfasst wird. Verunstesung) Service. Alle Zwischenspeicherartefakte werden vom Aufnahmeclient unter Verwendung der vom Kusto Data Management-Dienst zugewiesenen Ressourcen erstellt.<BR>

Das folgende Diagramm beschreibt die Interaktion des Warteschlangen-Aufnahmeclients mit Kusto:<BR>

![alt text](../images/queued-ingest.jpg "Queued-Ingest")

### <a name="permissions-on-the-engine-service"></a>Berechtigungen für den Engine-Service
Um sich für die Datenerfassung `T1` in `DB1` der Tabelle in der Datenbank zu qualifizieren, muss der Prinzipal, der den Aufnahmevorgang durchführt, dafür autorisiert werden.
Minimale erforderliche Berechtigungsstufen sind `Database Ingestor` und `Table Ingestor` können Daten entsprechend in alle vorhandenen Tabellen in einer Datenbank oder in eine bestimmte vorhandene Tabelle aufnehmen.
Wenn die Tabellenerstellung `Database User` erforderlich ist oder eine Rolle mit höherem Zugriff zugewiesen werden muss.


|Role |PrincipalType    |PrincipalDisplayName
|--------|------------|------------
|Datenbank *** Ingestor |AAD-Anwendung |Test-App (App-ID: 2a904276-1234-5678-9012-66fc53add60b)
|Tabelle *** Ingestor |AAD-Anwendung |Test-App (App-ID: 2a904276-1234-5678-9012-66fc53add60b)

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`Principal (Kusto internal Ingestion App) ist der `Cluster Admin` Rolle unveränderlich zugeordnet und somit berechtigt, Daten in jeder Tabelle zu erfassen (das geschieht in Kusto-verwalteten Aufnahmepipelines).

Wenn Sie AAD `DB1` `T1` App `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` erforderliche Berechtigungen für eine Datenbank oder Tabelle erteilen, sieht dies wie folgt aus:
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```

