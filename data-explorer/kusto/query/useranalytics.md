---
title: 'Benutzer Analysen: Azure-Daten-Explorer'
description: In diesem Artikel wird die Benutzer Analyse in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 5a9ca6259296f2fa2c5ad83622e7f3012169864e
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966974"
---
# <a name="user-analytics-plugins"></a>Benutzeranalyse-Plug-Ins

In diesem Abschnitt werden Kusto-Erweiterungen (Plug-ins) für Benutzer Analyse Szenarios beschrieben.

|Szenario|Plug-In|Details|Benutzererfahrung|
|--------|------|--------|-------|
| Zählen neuer Benutzer im Zeitverlauf | [activity_counts_metrics](activity-counts-metrics-plugin.md)|Gibt Zählungen/dcounts/neue Anzahl für jedes Zeitfenster zurück. Jedes Zeitfenster wird mit *allen* vorherigen Zeitfenstern verglichen.|Kusto. Explorer: Berichts Katalog|
| Zeitraum-über-Zeitraum: Beibehaltungs Dauer/Änderungs Rate und neue Benutzer | [activity_metrics](activity-metrics-plugin.md)|Gibt die `dcount` Beibehaltungs Dauer für jedes Zeitfenster zurück. Jedes Zeitfenster wird mit dem *vorherigen* Zeitfenster verglichen.|Kusto. Explorer: Berichts Katalog|
| Anzahl von Benutzern und `dcount` über gleitender Fenster | [sliding_window_counts](sliding-window-counts-plugin.md)|Gibt für jedes Zeitfenster die Anzahl und `dcount` über einen Nachschlage Zeitraum in einem gleitenden Fenster zurück.|
| Kohorte für neue Benutzer: Beibehaltungs Dauer/Änderungs Rate und neue Benutzer | [new_activity_metrics](new-activity-metrics-plugin.md)|Vergleicht zwischen den Kohorten neuer Benutzer (alle Benutzer, die zuerst im Zeitfenster angezeigt wurden). Jede Kohorte wird mit allen vorherigen Kohorten verglichen. Vergleich berücksichtigt *alle* vorherigen Zeitfenster|Kusto. Explorer: Berichts Katalog|
|Aktive Benutzer: unterschiedliche Anzahl |[active_users_count](active-users-count-plugin.md)|Gibt für jedes Zeitfenster verschiedene Benutzer zurück. Ein Benutzer wird nur berücksichtigt, wenn er in einem bestimmten Nachschlage Zeitraum in mindestens X unterschiedlichen Zeiträumen angezeigt wird.|
|Benutzer Engagement: Dau/WAU/Mau|[activity_engagement](activity-engagement-plugin.md)|Vergleicht zwischen einem inneren Zeitfenster (z. b. täglich) und einem äußeren (z. b. wöchentlich) für die Computer Einbindung (z. b. Dau/WAU)|Kusto. Explorer: Berichts Katalog|
|Sitzungen: Anzahl aktiver Sitzungen|[session_count](session-count-plugin.md)|Zählt Sitzungen, bei denen eine Sitzung durch einen Zeitraum definiert wird. ein Benutzerdaten Satz wird als neue Sitzung betrachtet, wenn er im Abruf Zeitraum des aktuellen Datensatzes nicht gefunden wurde.|
||||
|Trichter: vorherige und nächste Zustands Sequenzanalyse | [funnel_sequence](funnel-sequence-plugin.md)|Zählt verschiedene Benutzer, die eine Sequenz von Ereignissen erstellt haben, und das vorherige oder das nächste Ereignis, das bzw. die die Sequenz gefolgt ist. Nützlich zum Erstellen von [Sankey-Diagrammen](https://en.wikipedia.org/wiki/Sankey_diagram)||
|Trichter: Analyse der Sequenz Vervollständigung|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|Berechnet die unterschiedliche Anzahl von Benutzern, die eine angegebene Sequenz in jedem Zeitfenster *abgeschlossen* haben.|
||||