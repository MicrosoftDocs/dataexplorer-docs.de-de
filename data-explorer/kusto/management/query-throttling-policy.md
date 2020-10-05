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
ms.openlocfilehash: 7d6db9ba7cbb7b1fbaaee7325043fed287673178
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734004"
---
# <a name="query-throttling-policy"></a>Richtlinie für die Abfrage Drosselung

Definieren Sie die Richtlinie für die Abfrage Einschränkung, um die Anzahl gleichzeitiger Abfragen, die der Cluster gleichzeitig ausführt, einzuschränken. Die Richtlinie kann zur Laufzeit geändert werden und findet unmittelbar nach Abschluss des Befehls "Richtlinie ändern" statt.

* Verwenden [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) Sie, um die aktuelle Richtlinie für die Abfrage Drosselung eines Clusters anzuzeigen.
* Verwenden [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) Sie, um die aktuelle Richtlinie für die Abfrage Drosselung eines Clusters festzulegen.
* Verwenden [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) Sie, um die aktuelle Richtlinie für die Abfrage Drosselung eines Clusters zu löschen.

> [!NOTE]
> Wenn Sie keine Richtlinie für die abfragendrosselung definieren, kann eine große Anzahl gleichzeitiger Abfragen dazu führen, dass der Cluster nicht zugänglich ist oder Leistungseinbußen verursachen.
