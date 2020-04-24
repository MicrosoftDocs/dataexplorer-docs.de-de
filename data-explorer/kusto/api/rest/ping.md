---
title: Kusto Ping REST-API - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Kusto Ping REST-API in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 14a3d3dd641ff435784eac4e813d98bac8c4a552
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502583"
---
# <a name="kusto-ping-rest-api"></a>Kusto Ping REST-API

Die Ping-REST-API wird bereitgestellt, damit Netzwerkgeräte wie Load Balancer die einfache Netzwerkreaktionsfähigkeit der zustandslosen Front-End-Komponenten des Clusters überprüfen können. Wenn ein GET-Verb auf diesen Endpunkt angewendet wird, antwortet der Cluster, indem er eine 200 OK-HTTP-Antwort zurückgibt. Die REST-API ist nicht authentifiziert (der `Authorization` Aufrufer muss keinen HTTP-Header senden).

- Pfad: `/v1/rest/ping`
- Verb:`GET`
- Aktionen: Reagieren `200 OK` mit einer Nachricht
- Argumente: Keine