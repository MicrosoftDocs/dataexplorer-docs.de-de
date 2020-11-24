---
title: Funktionen – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden Funktionen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 1e0b3f339a755531d8db146ed2dc478ebb5a888d
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513028"
---
# <a name="function-types"></a>Funktionstypen

**Funktionen** sind wiederverwendbare Abfragen oder Abfrageteile. Kusto unterstützt verschiedene Arten von Funktionen:

* **Gespeicherte Funktionen:** Hierbei handelt es sich um benutzerdefinierte Funktionen, die als eine Art von Schemaentitäten einer Datenbank gespeichert und verwaltet werden.
  Weitere Informationen finden Sie unter [Stored functions](../schema-entities/stored-functions.md) (Gespeicherte Funktionen).
* **Abfragedefinierte Funktionen:** Hierbei handelt es sich um benutzerdefinierte Funktionen, die im Rahmen einer einzelnen Abfrage definiert und verwendet werden. Solche Funktionen werden mithilfe einer [let-Anweisung](../letstatement.md) definiert.
  Weitere Informationen finden Sie unter [User-defined functions](./user-defined-functions.md) (Benutzerdefinierte Funktionen).
* **Integrierte Funktionen:** Hierbei handelt es sich um hartcodierte, durch Kusto definierte Funktionen, die von Benutzern nicht geändert werden können.