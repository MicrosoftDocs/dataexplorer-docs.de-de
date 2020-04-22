---
title: Azure Data Explorer-REST-API – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Azure Data Explorer-REST-API beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 000561f96bd4174b94cca8e9db4c932a3cf3f0c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490683"
---
# <a name="azure-data-explorer-rest-api"></a>Azure Data Explorer-REST-API

In diesem Artikel erfahren Sie, wie Sie mit Kusto über HTTPS interagieren.

## <a name="what-actions-are-supported"></a>Welche Aktionen werden unterstützt?

Welche Aktionen von einem Endpunkt unterstützt werden, hängt davon ab, ob es sich dabei um einen Endpunkt für die Engine oder um einen Endpunkt für die Datenverwaltung handelt.

|Aktion         |HTTP-Verb  |URI-Vorlage             |Engine|Datenverwaltung|Authentifizierung?|
|---------------|-----------|-------------------------|------|---------------|---------------|
|Abfrage          |GET oder POST|`/v1/rest/query`         |Ja   |Nein             |Ja            |
|Abfrage          |GET oder POST|`/v2/rest/query`         |Ja   |Nein             |Ja            |
|Verwaltung     |POST       |`/v1/rest/mgmt`          |Ja   |Ja            |Ja            |
|UI             |GET        |`/`                      |Ja   |Nein             |Nein             |
|UI             |GET        |`/{dbname}`              |Ja   |Nein             |Nein             |

**Aktion** steht hier für eine Gruppe verwandter Aktivitäten:

* Die Aktion **Abfrage** sendet eine Abfrage an den Dienst und ruft die Ergebnisse der Abfrage ab.
* Die Aktion **Verwaltung** sendet einen Steuerungsbefehl an den Dienst und ruft die Ergebnisse des Steuerungsbefehls ab.
* Mit der Aktion **Benutzeroberfläche** kann ein Desktop- oder Webclient (über eine HTTP-Umleitungsantwort) für die Interaktion mit dem Dienst gestartet werden.

Ausführliche Informationen zur HTTP-Anforderung/-Antwort der Aktionen „Abfrage“ und „Verwaltung“ finden Sie unter [Query/management HTTP request](./request.md) (HTTP-Anforderung (Abfrage/Verwaltung)), [Query/management HTTP response](./response.md) (HTTP-Antwort (Abfrage/Verwaltung)) sowie unter [The Kusto REST API Response Object (V2)](./response2.md) (Das Kusto-REST-API-Antwortobjekt (v2)). Ausführliche Informationen zur Aktion „Benutzeroberfläche“ finden Sie unter [UI deep links](./deeplink.md) (Deep-Links für die Benutzeroberfläche).