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
ms.openlocfilehash: f1307b54c9f0b7a948925dd4eaa4d1f2e89d8070
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490309"
---
# <a name="functions"></a>Functions

**Funktionen** sind wiederverwendbare Abfragen oder Abfrageteile. Kusto unterstützt verschiedene Arten von Funktionen:

* **Gespeicherte Funktionen:** Hierbei handelt es sich um benutzerdefinierte Funktionen, die als eine Art von Schemaentitäten einer Datenbank gespeichert und verwaltet werden.
  Weitere Informationen finden Sie unter [Stored functions](../schema-entities/stored-functions.md) (Gespeicherte Funktionen).
* **Abfragedefinierte Funktionen:** Hierbei handelt es sich um benutzerdefinierte Funktionen, die im Rahmen einer einzelnen Abfrage definiert und verwendet werden. Solche Funktionen werden mithilfe einer [let-Anweisung](../letstatement.md) definiert.
  Weitere Informationen finden Sie unter [User-defined functions](./user-defined-functions.md) (Benutzerdefinierte Funktionen).
* **Integrierte Funktionen:** Hierbei handelt es sich um hartcodierte, durch Kusto definierte Funktionen, die von Benutzern nicht geändert werden können.