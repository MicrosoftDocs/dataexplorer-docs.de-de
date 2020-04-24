---
title: Authentifizierung über HTTPS - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Authentifizierung über HTTPS in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 4b6fbf5bb34dc3ff52938c7042778a7e49fd5faf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503042"
---
# <a name="authentication-over-https"></a>Authentifizierung über HTTPS

Bei Verwendung von HTTPS unterstützt `Authorization` der Dienst den Standard-HTTP-Header für die Authentifizierung.

Die unterstützten HTTP-Authentifizierungsmethoden sind:

* **Azure Active Directory** `bearer` , über die Methode.

Bei der Authentifizierung mit Azure `Authorization` Active Directory hat der Header das folgende Format:

```txt
Authorization: bearer TOKEN
```

Wo `TOKEN` befindet sich das Zugriffstoken, das der Aufrufer durch Die Kommunikation mit dem Azure Active Directory-Dienst mit den folgenden Eigenschaften erhält:

* Die Ressource ist der Dienst-URI `https://help.kusto.windows.net`(z. B. ).
* Der Azure Active Directory-Dienstendpunkt ist `https://login.microsoftonline.com/TENANT/`.

Wobei `TENANT` sich die Azure Active Directory-Mandanten-ID oder der Name befindet. Beispielsweise können Dienste, die unter `https://login.microsoftonline.com/microsoft.com/`dem Microsoft-Mandanten erstellt wurden, verwenden. Alternativ kann die Anforderung nur für die `https://login.microsoftonline.com/common/` Benutzerauthentifizierung gestellt werden.

> [!NOTE]
> Der Azure Active Directory-Dienstendpunkt ändert sich, wenn er in nationalen Clouds ausgeführt wird.
> Um den zu verwendenden Endpunkt zu `AadAuthorityUri` ändern, legen Sie eine Umgebungsvariable auf den erforderlichen URI fest.

Weitere Informationen finden Sie in der [Authentifizierungsübersicht](../../management/access-control/index.md) und [Anleitung zur Azure Active Directory-Authentifizierung](../../management/access-control/how-to-authenticate-with-aad.md).