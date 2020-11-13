---
title: 'Richtlinie für die Abfrage Drosselung: Azure Daten-Explorer'
description: In diesem Artikel wird die Richtlinie für die Abfrage Drosselung in Azure beschrieben Daten-Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 0f50170e3ccfab20d434a90188baf511de056cef
ms.sourcegitcommit: 541164d8745022906b1da4aaca41ec15393f85d5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570564"
---
# <a name="query-throttling-policy"></a>Abfragedrosselungsrichtlinie

Definieren Sie die Richtlinie für die Abfrage Einschränkung, um die Anzahl gleichzeitiger Abfragen, die der Cluster gleichzeitig ausführen kann, einzuschränken. Diese Richtlinie schützt den Cluster vor der Überlastung durch mehr gleichzeitige Abfragen als möglich. Die Richtlinie kann zur Laufzeit geändert werden und findet unmittelbar nach Abschluss des Befehls "Richtlinie ändern" statt.

* Verwenden [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) Sie, um die aktuelle Richtlinie für die Abfrage Drosselung eines Clusters anzuzeigen.
* Verwenden [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) Sie, um die aktuelle Richtlinie für die Abfrage Drosselung eines Clusters festzulegen.
* Verwenden [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) Sie, um die aktuelle Richtlinie für die Abfrage Drosselung eines Clusters zu löschen.

> [!NOTE]
> Ändern Sie die Richtlinie für die Abfrage Drosselung nicht, ohne gründliche Tests durchzuführen. Änderungen können zu Leistungseinbußen führen, wenn die Cluster Ressourcen nicht gemäß der Auslastung skaliert werden. Daher treten bei Abfragen möglicherweise Fehler auf. 
