---
title: Kusto. Erfassungs Berechtigungen-Azure Daten-Explorer
description: In diesem Artikel werden die Erfassungs Berechtigungen von Kusto. Ingestion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e88ba9af0b9563274e15eff8d1c1f6e997fb45c
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630012"
---
# <a name="kustoingest---ingestion-permissions"></a>Kusto. inerfassungs Berechtigungen 

In diesem Artikel wird erläutert, welche Berechtigungen für den Dienst eingerichtet werden, damit `Native` die Erfassung funktioniert.

## <a name="prerequisites"></a>Voraussetzungen
 
* Informationen zum Anzeigen und Ändern von Autorisierungs Einstellungen für Kusto-Dienste und-Datenbanken finden Sie unter [Kusto-Steuerungsbefehle](../../management/security-roles.md).

* Azure Active Directory (Azure AD)-Anwendungen, die in den folgenden Beispielen als Beispiel Prinzipale verwendet werden:
    * Test Azure AD-App (2a904276-1234-5678-9012-66fc53add60b; Microsoft.com)
    * Interner Kusto-Erfassungs Azure AD-App (76263cdb-1234-5678-9012-545644e9c404; Microsoft.com)
 
## <a name="ingestion-permission-mode-for-queued-ingestion"></a>Erfassungs Berechtigungs Modus für die Erfassung in der Warteschlange

Der Erfassungs Berechtigungs Modus ist in [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)definiert. Dieser Modus schränkt die Client Code Abhängigkeit für den Azure Daten-Explorer-Dienst ein. Die Erfassung erfolgt durch das Veröffentlichen einer Kusto-Erfassungs Nachricht in einer Azure-Warteschlange. Die Warteschlange, auch als Erfassungs Dienst bezeichnet, wird vom Azure-Daten-Explorer Dienst abgerufen. Zwischenspeicher Artefakte werden vom Erfassungs Client mithilfe der vom Azure-Daten-Explorer Dienst zugewiesenen Ressourcen erstellt.

Das Diagramm zeigt die Interaktion mit dem Erfassungs Client in der Warteschlange mit Kusto.

:::image type="content" source="../images/kusto-ingest-client-permissions/queued-ingest.png" alt-text="Erfassung aus der Warteschlange":::

### <a name="permissions-on-the-engine-service"></a>Berechtigungen für den Engine-Dienst

Um die Datenerfassung in eine Tabelle in der Datenbank zu qualifizieren `T1` `DB1` , muss der Prinzipal, der den Erfassungs Vorgang durch nimmt, eine Autorisierung aufweisen
Die minimal erforderlichen Berechtigungsstufen sind `Database Ingestor` und `Table Ingestor` , mit denen Daten in allen vorhandenen Tabellen in einer Datenbank oder in einer bestimmten vorhandenen Tabelle erfasst werden können.
Wenn eine Tabellenerstellung erforderlich ist `Database User` oder eine höhere Zugriffs Rolle zugewiesen werden muss.


|Rolle                 |Principaltype        |Principaldisplayname
|---------------------|---------------------|------------
|`Database Ingestor`  |Azure AD Anwendung |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor`     |Azure AD Anwendung |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion Azure AD App (76263cdb-1234-5678-9012-545644e9c404)`Prinzipal, die interne Erfassungs-APP von Kusto, ist unveränderlich der `Cluster Admin` Rolle zugeordnet. Sie ist daher zum Erfassen von Daten in einer beliebigen Tabelle autorisiert. Dies geschieht in den Kusto-verwalteten Erfassungs Pipelines.

Das erteilen der erforderlichen Berechtigungen für `DB1` die Datenbank oder Tabelle `T1` für Azure AD-App `Test App (2a904276-1234-5678-9012-66fc53add60b in Azure AD tenant microsoft.com)` würde wie folgt aussehen:

```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
```
 