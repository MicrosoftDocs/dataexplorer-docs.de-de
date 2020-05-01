---
title: 'Authentifizierung über https: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Authentifizierung über HTTPS in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: e0910089d87d6bce6124cb7e4560c2fa7b92b847
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617985"
---
# <a name="authentication-over-https"></a>Authentifizierung über HTTPS

Bei Verwendung von HTTPS unterstützt der Dienst den HTTP `Authorization` -Standard Header zum Durchführen der Authentifizierung.

Die folgenden HTTP-Authentifizierungsmethoden werden unterstützt:

* **Azure Active Directory**über die `bearer` -Methode.

Bei der Authentifizierung mithilfe Azure AD hat der `Authorization` Header das folgende Format:

```txt
Authorization: bearer TOKEN
```

Dabei `TOKEN` ist das Zugriffs Token, das der Aufrufer durch die Kommunikation mit dem Azure AD-Dienst abruft. Das Token verfügt über die folgenden Eigenschaften:

* Die Ressource ist der Dienst-URI (z. `https://help.kusto.windows.net`b.).
* Der Azure AD-Dienst Endpunkt ist`https://login.microsoftonline.com/TENANT/`

Dabei `TENANT` ist die Azure AD Mandanten-ID oder der Name des Mandanten. Beispielsweise können Dienste, die unter dem Microsoft-Mandanten erstellt werden `https://login.microsoftonline.com/microsoft.com/`, verwenden. Alternativ kann die Anforderung nur für die Benutzerauthentifizierung an `https://login.microsoftonline.com/common/`erfolgen.

> [!NOTE]
> Der Azure AD-Dienst Endpunkt ändert sich, wenn er in nationalen Clouds ausgeführt wird.
> Um den Endpunkt zu ändern, legen Sie eine `AadAuthorityUri` Umgebungsvariable auf den erforderlichen URI fest.

Weitere Informationen finden Sie in der [Übersicht über die Authentifizierung](../../management/access-control/index.md) und im [Leitfaden zur Azure AD-Authentifizierung](../../management/access-control/how-to-authenticate-with-aad.md).
