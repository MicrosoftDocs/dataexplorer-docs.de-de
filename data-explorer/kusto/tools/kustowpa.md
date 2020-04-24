---
title: Kusto WPA - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto WPA in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 2d8c5a9b11d8045897d8a9bd798e40ceb0f48b6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503297"
---
# <a name="kusto-wpa"></a>Kusto WPA

Kusto WPA bietet die Möglichkeit, Kusto-Abfrageergebnisse in Windows Performance Analyzer (WPA) auszuführen und zu visualisieren. Es besteht aus zwei Hauptteilen:

1. Ein Launcher-Tool, das Kusto.Explorer-shared-Abfragen `Share` &gt; `Query to Clipboard`(über`.kpq`) akzeptieren und Abfragedateien ( ) generieren kann, die von WPA verarbeitet werden sollen.

1. Ein WPA-Plugin, das die generierten`.kpq`Abfragedateien " öffnen kann ( ). Beim Öffnen einer solchen Datei wird die in der Datei angegebene Abfrage automatisch an einen Kusto-Cluster gesendet und die Ergebnisse geladen, als ob sie aus einer "Standard"-ETL-Datei stammten.

Weiterehttps://aka.ms/kustowpaInformationen und Download-Informationen finden Sie unter [ ].