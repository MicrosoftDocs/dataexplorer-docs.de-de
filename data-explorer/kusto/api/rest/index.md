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
ms.openlocfilehash: e7598ccc2c04ab54a27830ba16e2b307e84bba2c
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428276"
---
# <a name="azure-data-explorer-rest-api"></a>Azure Data Explorer-REST-API

In diesem Artikel erfahren Sie, wie Sie mit Azure Data Explorer über HTTPS interagieren.

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
