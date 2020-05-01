---
title: Kusto. Erfassungs Verweis-Erfassungs Berechtigungen-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Kusto-Erfassung von Verweis Erfassungs Berechtigungen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ec3b91056a4cfc584c0d7168549864b2b50ef5ae
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617917"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto. Erfassungs Verweis-Erfassungs Berechtigungen
In diesem Artikel wird erläutert, welche Berechtigungen für den Dienst eingerichtet werden `Native` , damit die Erfassung funktioniert.


## <a name="prerequisites"></a>Voraussetzungen
* Informationen zum Anzeigen und Ändern von Autorisierungs Einstellungen für Kusto-Dienste und-Datenbanken finden Sie unter [Befehle](../../management/security-roles.md) 

## <a name="references"></a>References
* In den folgenden Beispielen werden die folgenden Aad-Anwendungen als Beispiel Prinzipale verwendet:
    * Testen der Aad-app (2a904276-1234-5678-9012-66fc53add60b; Microsoft.com)
    * Kusto Internal Ingestion Aad-app (76263cdb-1234-5678-9012-545644e9c404; Microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>Erfassungs Berechtigungs Modell für die Erfassung in der Warteschlange
Dieser Modus, der in [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)definiert ist, schränkt die Client Code Abhängigkeit für den Azure Daten-Explorer-Dienst ein. Die Erfassung erfolgt durch das Veröffentlichen einer Kusto-Erfassungs Nachricht in einer Azure-Warteschlange. Die Warteschlange wird vom Azure Daten-Explorer-Dienst abgerufen. "SS" wird auch als Erfassungs Dienst bezeichnet.  Alle Zwischenspeicher Artefakte werden vom Erfassungs Client mithilfe der Ressourcen erstellt, die vom Azure-Daten-Explorer Dienst zugeordnet werden.

Das folgende Diagramm zeigt die Interaktion mit dem Erfassungs Client in der Warteschlange mit Kusto:<BR>

:::image type="content" source="../images/queued-ingest.jpg" alt-text="in Warteschlange eingereiht":::

### <a name="permissions-on-the-engine-service"></a>Berechtigungen für den Engine-Dienst
Um die Datenerfassung in eine Tabelle `T1` in der Datenbank `DB1`zu qualifizieren, muss der Prinzipal, der den Erfassungs Vorgang durch nimmt, eine Autorisierung aufweisen
Die minimal erforderlichen Berechtigungs `Database Ingestor` Stufen `Table Ingestor` sind und, mit denen Daten in allen vorhandenen Tabellen in einer Datenbank oder in einer bestimmten vorhandenen Tabelle erfasst werden können.
Wenn eine Tabellenerstellung erforderlich ist `Database User` oder eine höhere Zugriffs Rolle zugewiesen werden muss.


|Role |Principaltype    |Principaldisplayname
|--------|------------|------------
|`Database Ingestor` |Aad-Anwendung |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor` |Aad-Anwendung |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`Prinzipal, die interne Erfassungs-APP von Kusto, ist unveränderlich der `Cluster Admin` Rolle zugeordnet und somit zum Erfassen von Daten in einer beliebigen Tabelle autorisiert. Dies geschieht in den Kusto-verwalteten Erfassungs Pipelines.

Das erteilen der erforderlichen Berechtigungen `DB1` für die `T1` Datenbank oder Tabelle für `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` die Aad-APP würde wie folgt aussehen:
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```
