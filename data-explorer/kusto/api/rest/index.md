---
title: 'Azure Data Explorer-REST-API: Azure Data Explorer'
description: In diesem Artikel wird die Azure Data Explorer-REST-API beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 6abfe15490e5c633e1cb7912f4794ee90bec1947
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373603"
---
# <a name="azure-data-explorer-rest-api"></a>Azure Data Explorer-REST-API

In diesem Artikel erfahren Sie, wie Sie mit Kusto über HTTPS interagieren.

## <a name="supported-actions"></a>Unterstützte Aktionen

Welche Aktionen von einem Endpunkt unterstützt werden, hängt davon ab, ob es sich dabei um einen Endpunkt für die Engine oder um einen Endpunkt für die Datenverwaltung handelt.

|Aktion         |HTTP-Verb   |URI-Vorlage           |Engine|Datenverwaltung|Authentifizierung |
|---------------|------------|-----------------------|------|---------------|---------------|
|Abfrage          |GET oder POST |/v1/rest/query         |Ja   |Nein             |Ja            |
|Abfrage          |GET oder POST |/v2/rest/query         |Ja   |Nein             |Ja            |
|Verwaltung     |POST        |/v1/rest/mgmt          |Ja   |Ja            |Ja            |
|UI             |GET         |/                      |Ja   |Nein             |Nein             |
|UI             |GET         |/{dbname}              |Ja   |Nein             |Nein             |

*Aktion* steht hier für eine Gruppe verwandter Aktivitäten.

* Die Aktion „Abfrage“ sendet eine Abfrage an den Dienst und ruft die Ergebnisse der Abfrage ab.
* Die Aktion „Verwaltung“ sendet einen Steuerungsbefehl an den Dienst und ruft die Ergebnisse des Steuerungsbefehls ab.
* Mit der Aktion „Benutzeroberfläche“ kann ein Desktopclient oder ein Webclient gestartet werden. Für die Interaktion mit dem Dienst wird die Aktion über eine HTTP-Umleitungsantwort ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur HTTP-Anforderung und -Antwort der Abfrage- und Verwaltungsaktionen finden Sie unter folgenden Links:
 * [HTTP-Anforderung (Abfrage/Verwaltung)](./request.md)
 * [HTTP-Antwort (Abfrage/Verwaltung)](./response.md)
 * [HTTP-Antwort (Abfrage v2)](./response2.md)

Weitere Informationen zur Aktion „Benutzeroberfläche“ finden Sie unter folgendem Link:
 * [Benutzeroberfläche (Deep-Link)](./deeplink.md)
