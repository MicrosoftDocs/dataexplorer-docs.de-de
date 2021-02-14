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
adobe-target: true
ms.openlocfilehash: 234ae530fcd664d8d12418ed85d8394385bebf75
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359931"
---
# <a name="function-types"></a>Funktionstypen

**Funktionen** sind wiederverwendbare Abfragen oder Abfrageteile. Kusto unterstützt verschiedene Arten von Funktionen:

* **Gespeicherte Funktionen:** Hierbei handelt es sich um benutzerdefinierte Funktionen, die als eine Art von Schemaentitäten einer Datenbank gespeichert und verwaltet werden.
  Weitere Informationen finden Sie unter [Stored functions](../schema-entities/stored-functions.md) (Gespeicherte Funktionen).
* **Abfragedefinierte Funktionen:** Hierbei handelt es sich um benutzerdefinierte Funktionen, die im Rahmen einer einzelnen Abfrage definiert und verwendet werden. Solche Funktionen werden mithilfe einer [let-Anweisung](../letstatement.md) definiert.
  Weitere Informationen finden Sie unter [User-defined functions](./user-defined-functions.md) (Benutzerdefinierte Funktionen).
* **Integrierte Funktionen:** Hierbei handelt es sich um hartcodierte, durch Kusto definierte Funktionen, die von Benutzern nicht geändert werden können.