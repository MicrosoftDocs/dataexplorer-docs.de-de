---
title: Verwenden von Azure Data Share für das Freigeben von Daten mit Azure Data Explorer (Vorschauversion)
description: Erfahren Sie, wie Sie Ihre Daten mit Azure Data Explorer und Azure Data Share freigeben.
author: manojraheja
ms.author: maraheja
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: c935b6f888d40cfcd2208e8857dc3d794f9a50cb
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83865929"
---
# <a name="use-azure-data-share-to-share-data-with-azure-data-explorer-preview"></a>Verwenden von Azure Data Share für das Freigeben von Daten mit Azure Data Explorer (Vorschauversion)

Es gibt viele herkömmliche Möglichkeiten zum Freigeben von Daten, z. B. über Dateifreigaben, FTP, E-Mail und APIs. Diese Methoden erfordern, dass beide Parteien eine Datenpipeline erstellen und verwalten, über die die Daten zwischen Teams und Organisationen ausgetauscht werden. Mit Azure Data Explorer können Sie Ihre Daten problemlos und sicher für Personen in Ihrem Unternehmen oder externe Partner freigeben. Die Freigabe erfolgt nahezu in Echtzeit, ohne dass eine Datenpipeline erstellt oder gewartet werden muss. Alle Datenbankänderungen, einschließlich Schema und Daten, auf Anbieterseite sind auf Consumerseite sofort verfügbar.

[![Azure Friday-Video](https://img.youtube.com/vi/Q3MJv90PegE/0.jpg)](https://www.youtube.com/watch?v=Q3MJv90PegE?&autoplay=1)

Azure Data Explorer entkoppelt den Speicher und Compute, sodass Kunden mehrere Computeinstanzen (schreibgeschützt) mit demselben zugrunde liegenden Speicher ausführen können. Sie können eine Datenbank als [Followerdatenbank](follower.md) anfügen, eine schreibgeschützte Datenbank in einem Remotecluster.

## <a name="configure-data-sharing"></a>Konfigurieren der Datenfreigabe 

Sie können die Datenfreigabe anhand einer der folgenden Methoden konfigurieren:

* Verwenden Sie [Azure Data Share](/azure/data-share/), um Einladungen und Freigaben innerhalb des Unternehmens oder an externe Partner und Kunden zu senden und zu verwalten. Zum Erstellen einer symbolischen Verknüpfung zwischen den Azure Data Explorer-Clustern des Anbieters und des Consumers verwendet Azure Data Share eine [Followerdatenbank](follower.md). Diese Option bietet Ihnen einen zentralen Bereich zum Anzeigen und Verwalten Ihrer Datenfreigaben in Azure Data Explorer-Clustern und anderen Datendiensten. Azure Data Share ermöglicht Ihnen außerdem das Freigeben von Daten für Organisationen in unterschiedlichen Azure Active Directory-Mandanten.
* Konfigurieren Sie die [Followerdatenbank](follower.md) direkt nur in Szenarien, in denen Sie zusätzliche Computeressourcen zum Aufskalieren für die Berichterstellung benötigen und in denen Sie in beiden Clustern Administrator sind.

> [!Note] 
> Beim Herstellen einer Freigabebeziehung erstellt Azure Data Share eine symbolische Verknüpfung zwischen den Azure Data Explorer-Clustern des Anbieters und des Consumers. Wenn der Datenanbieter den Zugriff widerruft, wird die symbolische Verknüpfung gelöscht, und die freigegebenen Datenbanken sind nicht mehr für den Datenconsumer verfügbar.

:::image type="content" source="media/data-share/adx-datashare-image.png" alt-text="Datenfreigabe in Azure Data Explorer":::

Der Datenanbieter kann die Daten auf Datenbankebene oder auf Clusterebene freigeben. Der Cluster, der die Datenbank freigibt, ist der übergeordnete Cluster (Leader), und der Cluster, der die Freigabe empfängt, ist der untergeordnete Cluster (Follower). Ein Followercluster kann einer oder mehreren übergeordneten Clusterdatenbanken untergeordnet sein. Der Followercluster wird regelmäßig synchronisiert, um Änderungen zu ermitteln. Die Verzögerungszeit zwischen Leader und Follower variiert in Abhängigkeit von der Gesamtgröße der Metadaten und der Daten zwischen einigen Sekunden und wenigen Minuten. Daten werden im Consumercluster zwischengespeichert und sind nur für Lese- oder Abfragevorgänge verfügbar. Die einzige Ausnahme bildet die Außerkraftsetzung der Richtlinie für das heiße Caching und der Datenbankberechtigungen. Bei den Abfragen im Followercluster wird der lokale Cache verwendet, es werden keine Ressourcen im Leadercluster genutzt.

## <a name="prerequisites"></a>Voraussetzungen

1. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
1. [Erstellen Sie einen Anbietercluster und eine Datenbank](create-cluster-database-portal.md) für den Leader und den Follower.
1. [Erfassen Sie Daten](ingest-sample-data.md) in der Leader-Datenbank mithilfe einer der verschiedenen Methoden, die unter [Übersicht über die Datenerfassung](ingest-data-overview.md) erläutert werden.

## <a name="data-provider---share-data"></a>Datenanbieter: Freigeben von Daten

Befolgen Sie die Anweisungen im Video, um ein Datenfreigabekonto zu erstellen, ein Dataset hinzuzufügen und eine Einladung zu senden.

[![Datenanbieter: Freigeben von Daten](https://img.youtube.com/vi/QmsTnr90_5o/0.jpg)](https://youtu.be/QmsTnr90_5o?&autoplay=1)

## <a name="data-consumer---receive-data"></a>Datenconsumer: Empfangen von Daten

Befolgen Sie die Anweisungen im Video, um die Einladung anzunehmen, ein Datenfreigabekonto zu erstellen und es dem Consumercluster zuzuordnen.

[![Datenconsumer: Empfangen von Daten](https://img.youtube.com/vi/vBq6iFaCpdA/0.jpg)](https://youtu.be/vBq6iFaCpdA?&autoplay=1)

Der Datenconsumer kann nun in seinem Azure Data Explorer-Cluster Benutzerberechtigungen für die freigegebenen Datenbanken und den Zugriff auf die Daten gewähren. Daten, die im Batchmodus im Azure Data Explorer-Ausgangscluster erfasst werden, sind nach wenigen Sekunden bis zu einigen Minuten im Zielcluster verfügbar.

## <a name="limitations"></a>Einschränkungen

* [Einschränkungen der Followerdatenbank](follower.md#limitations)

## <a name="next-steps"></a>Nächste Schritte

* [Dokumentation zu Azure Data Share](/azure/data-share/)
* Weitere Informationen zu Followerclustern finden Sie unter [Followercluster.](follower.md)
