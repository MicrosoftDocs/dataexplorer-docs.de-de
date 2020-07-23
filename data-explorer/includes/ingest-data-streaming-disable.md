---
title: include file
description: include file
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: a748332c85839bac8466532ba3959810a6387834
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423176"
---
## <a name="disable-streaming-ingestion-on-your-cluster"></a>Deaktivieren der Streamingerfassung in Ihrem Cluster

> [!WARNING]
> Die Deaktivierung der Streamingerfassung kann mehrere Stunden dauern.

Bevor Sie die Streamingangabe für Ihren Azure Data Explorer-Cluster deaktivieren, löschen Sie die [Streamingerfassungsrichtlinie](../kusto/management/streamingingestionpolicy.md) aus allen relevanten Tabellen und Datenbanken. Durch das Entfernen der Streamingerfassungsrichtlinie wird eine Neuanordnung der Daten in Ihrem Azure Data Explorer-Cluster ausgelöst. Die Streamingerfassungsdaten werden aus dem anfänglichen Speicher in den permanenten Speicher im Spaltenspeicher (Erweiterungen oder Shards) verschoben. Dieser Vorgang kann abhängig von der Datenmenge im anfänglichen Speicher zwischen einigen Sekunden und wenigen Stunden dauern.
