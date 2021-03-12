---
title: Neuerungen in Azure Data Explorer
description: Neuerungen in der Dokumentation zu Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/17/2021
ms.openlocfilehash: 8217843a36fd8c60b48b47f5200361155d9e8c85
ms.sourcegitcommit: 40f86b7f085152c21b6a1ee877f3ab324b59b88b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101838909"
---
# <a name="whats-new-in-azure-data-explorer-documentation"></a>Neuerungen in der Dokumentation zu Azure Data Explorer

Willkommen zu den Neuerungen in Azure Data Explorer. Dieser Artikel enthält Informationen zu neuen und signifikant aktualisierten Inhalten in der Dokumentation zu Azure Data Explorer.

## <a name="january-2021"></a>Januar 2021

In diesem Abschnitt sind die wichtigsten Dokumentationsänderungen für Januar 2021 aufgeführt.

### <a name="general"></a>Allgemein

Artikeltitel | BESCHREIBUNG
---|--- 
[Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Data Explorer](security-controls-policy.md) | Neuer Artikel. Auf dieser Seite werden die **Compliancedomänen** und **Sicherheitskontrollen** für Azure Data Explorer aufgeführt.
[Zulassen von mandantenübergreifenden Abfragen und Befehlen](cross-tenant-query-and-commands.md) | Neuer Artikel. In diesem Artikel erfahren Sie, wie Sie einem Cluster Zugriff auf Prinzipale eines anderen Mandanten gewähren.

### <a name="management"></a>Verwaltung

### <a name="new-articles"></a>Neue Artikel

Artikeltitel | BESCHREIBUNG
---|---
[Befehle zum Bereinigen von Erweiterungscontainern](kusto/management/clean-extent-containers.md) | Neuer Artikel. In diesem Artikel werden die Befehle `.clean databases extentcontainers` und `.show database extentcontainers clean operations` in Azure Data Explorer beschrieben.
[Anforderungsklassifizierungsrichtlinie (Vorschau)](kusto/management/request-classification-policy.md)  <br>[Anforderungsklassifizierungsrichtlinie (Vorschau): Steuerungsbefehle](kusto/management/request-classification-policy-commands.md) | Neue Artikel. Beim Klassifizierungsprozess werden eingehende Anforderungen auf der Grundlage der Anforderungseigenschaften einer Arbeitsauslastungsgruppe zugewiesen.
[Richtlinie für Anforderungsgrenzwerte (Vorschau)](kusto/management/request-limits-policy.md) | Neuer Artikel. Die Richtlinie für Anforderungsgrenzwerte einer Arbeitsauslastungsgruppe ermöglicht das Einschränken der Ressourcen, die während der Anforderungsausführung verwendet werden.
[Richtlinie für Anforderungsratenbegrenzung (Vorschau)](kusto/management/request-rate-limit-policy.md) | Neuer Artikel. Mit der Richtlinie für die Anforderungsratenbegrenzung einer Arbeitsauslastungsgruppe können Sie die Anzahl gleichzeitiger klassifizierter Anforderungen für die Arbeitsauslastungsgruppe begrenzen.
[Arbeitsauslastungsgruppen (Vorschau)](kusto/management/workload-groups.md)  <br> [Arbeitsauslastungsgruppen (Vorschau): Steuerungsbefehle](kusto/management/workload-groups-commands.md) | Neue Artikel. Eine Arbeitsauslastungsgruppe fungiert als Container für Anforderungen (Abfragen, Befehle) mit ähnlichen Klassifizierungskriterien. Arbeitsauslastungen ermöglichen die aggregierte Überwachung der Anforderungen und definieren Richtlinien für die Anforderungen.
[Abfrageverwaltung](kusto/management/queries.md) | Aktualisierter Artikel. Die Syntax wurde aktualisiert.

## <a name="december-2020"></a>Dezember 2020

In diesem Abschnitt sind die wichtigsten Dokumentationsänderungen für Dezember 2020 aufgeführt.

### <a name="general"></a>Allgemein

Artikeltitel | BESCHREIBUNG
---|---
[Erfassungsfehlercodes in Azure Data Explorer](error-codes.md) | Neuer Artikel. Diese Liste enthält Fehlercodes, die bei der [Erfassung](ingest-data-overview.md) auftreten können.

### <a name="management"></a>Verwaltung

Artikeltitel | BESCHREIBUNG
---|---
[.create table based-on](kusto/management/create-table-based-on-command.md)  | Neuer Artikel. Dient zum Erstellen einer neuen leeren Tabelle auf der Grundlage einer vorhandenen Tabelle.
[Gespeicherte Abfrageergebnisse (Vorschau)](kusto/management/stored-query-results.md) | Neuer Artikel. Gespeicherte Abfrageergebnisse ermöglichen das vorübergehende Speichern des Ergebnisses einer Abfrage für den Dienst. 
[Erstellen und Ändern externer Tabellen in Azure Storage oder Azure Data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake.md) | Aktualisierter Artikel. Dokumentation der Optionen `filesPreview` und `dryRun` zum Definieren externer Tabellen

### <a name="functions-library"></a>Functions-Bibliothek

Artikeltitel | BESCHREIBUNG
---|---
[series_metric_fl()](kusto/functions-library/series-metric-fl.md) | Neuer Artikel. Mit der Funktion `series_metric_fl()` können Zeitreihen von Metriken ausgewählt und abgerufen werden, die mit dem Prometheus-Überwachungssystem in Azure Data Explorer erfasst wurden.
[series_rate_fl()](kusto/functions-library/series-rate-fl.md) | Neuer Artikel. Die Funktion `series_rate_fl()` ermöglicht die Berechnung der durchschnittlichen Metrikerhöhungsrate pro Sekunde.
[series_fit_lowess_fl()](kusto/functions-library/series-fit-lowess-fl.md) | Neuer Artikel. Die Funktion `series_fit_lowess_fl()` ermöglicht die Anwendung einer LOWESS-Regression auf eine Reihe.
[Exportieren von Daten in eine externe Tabelle](/azure/data-explorer/kusto/management/data-export/export-data-to-an-external-table.md) | Aktualisierter Artikel. Neue Syntax für externe Tabellen in der Exportdokumentation

## <a name="november-2020"></a>November 2020

In diesem Abschnitt sind die wichtigsten Dokumentationsänderungen für November 2020 aufgeführt.

### <a name="general"></a>Allgemein

Artikeltitel | BESCHREIBUNG
---|---
[Integrierte Azure Policy-Richtliniendefinitionen für Azure Data Explorer](policy-reference.md) | Neuer Artikel. Index der integrierten [Azure Policy](/azure/governance/policy/overview)-Richtliniendefinitionen für Azure Data Explorer 
[Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-event-hub.md) | Neuer Artikel. Hier erfahren Sie, wie Sie mithilfe der [1-Klick-Erfassung](ingest-data-one-click.md) einen Event Hub mit einer Tabelle in Azure Data Explorer verbinden.
| [Konfigurieren verwalteter Identitäten für Ihren Azure Data Explorer-Cluster](managed-identities.md) | Aktualisierter Artikel. Unterstützt sowohl benutzerseitig zugewiesene als auch systemseitig zugewiesene verwaltete Identitäten.
| [Erstellen einer Tabelle in Azure Data Explorer](one-click-table.md) | Aktualisierter Artikel. Allgemeine Verfügbarkeit (General Availability, GA) |
 | [Schnellstart: Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md) | Aktualisierter Artikel. Neue Funktionen
|  [Was ist die 1-Klick-Erfassung?](ingest-data-one-click.md) | Aktualisierter Artikel. Erfassung aus geschachtelten JSON-Ebenen hinzugefügt. Allgemeine Verfügbarkeit (General Availability, GA)
| [Anpassen der visuellen Elemente des Azure Data Explorer-Dashboards](dashboard-customize-visuals.md) | Aktualisierter Artikel. Neue visuelle Dashboardelemente und Parameteränderungen

### <a name="query"></a>Abfrage

Artikeltitel | BESCHREIBUNG
---|---
[Plug-In „mysql_request“ (Vorschau)](./kusto/query/mysqlrequest-plugin.md) | Neuer Artikel. Das Plug-In `mysql_request` sendet eine SQL-Abfrage an einen MySQL Server-Netzwerkendpunkt und gibt das erste Rowset aus den Ergebnissen zurück. 
[Plug-In „ipv4_lookup“](./kusto/query/ipv4-lookup-plugin.md) | Neuer Artikel. Das Plug-In `ipv4_lookup` schlägt in einer Nachschlagetabelle einen IPv4-Wert nach und gibt Zeilen mit entsprechenden Werten zurück.
[ipv4_is_private()](./kusto/query/ipv4-is-privatefunction.md) | Neuer Artikel. Überprüft, ob die IPv4-Zeichenfolgenadresse zu einer Gruppe privater Netzwerk-IP-Adressen gehört.
[Zuordnung von Splunk zur Kusto-Abfragesprache](./kusto/query/splunk-cheat-sheet.md) | Neuer Artikel. Dieser Artikel unterstützt Benutzer, die mit Splunk vertraut sind, beim Erlernen der Kusto-Abfragesprache, um Protokollabfragen mit Kusto schreiben zu können. 
[gzip_compress_to_base64_string()](./kusto/query/gzip-base64-compress.md) | Neuer Artikel. Führt die gzip-Komprimierung durch und codiert das Ergebnis in „base64“.
[gzip_decompress_from_base64_string()](./kusto/query/gzip-base64-decompress.md) | Neuer Artikel. Decodiert die Eingabezeichenfolge aus „base64“ und führt die gzip-Dekomprimierung durch.
[array_reverse()](./kusto/query/array-reverse-function.md) | Neuer Artikel. Kehrt die Reihenfolge der Elemente in einem dynamischen Array um.

### <a name="management"></a>Verwaltung

Artikeltitel | BESCHREIBUNG
---|---
[.disable plugin](./kusto/management/disable-plugin.md) | Neuer Artikel. Deaktiviert ein Plug-In.
[.enable plugin](./kusto/management/enable-plugin.md) | Neuer Artikel. Aktiviert ein Plug-In.
[.show plugins](./kusto/management/show-plugins.md) | Neuer Artikel. Listet alle Plug-Ins des Clusters auf.
| [Follower-Befehle für Cluster](kusto/management/cluster-follower.md) | Aktualisierter Artikel. Syntax geändert und `.alter follower database prefetch-extents` hinzugefügt |

### <a name="functions-library"></a>Functions-Bibliothek

Artikeltitel | BESCHREIBUNG
---|---
[series_downsample_fl()](./kusto/functions-library/series-downsample-fl.md) | Die Funktion `series_downsample_fl()` führt ein [Downsampling einer Zeitreihe um einen ganzzahligen Faktor](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)#Downsampling_by_an_integer_factor) durch. 
[series_exp_smoothing_fl()](./kusto/functions-library/series-exp-smoothing-fl.md) | Wendet einen einfachen exponentiellen Glättungsfilter auf eine Reihe an.

## <a name="october-2020"></a>Oktober 2020

In diesem Abschnitt sind die wichtigsten Dokumentationsänderungen für Oktober 2020 aufgeführt.

### <a name="general"></a>Allgemein

 Artikeltitel | BESCHREIBUNG
---|---
[Erfassen von Daten mithilfe des Java SDK für Azure Data Explorer](java-ingest-data.md) | Neuer Artikel.  In diesem Artikel erfahren Sie, wie Sie Daten mithilfe der Azure Data Explorer-Java-Bibliothek erfassen. 
[Manuelles Erstellen von Ressourcen für die Event Grid-Erfassung](ingest-data-event-grid-manual.md) | Neuer Artikel. In diesem Artikel erfahren Sie, wie Sie die Ressourcen, die für die Event Grid-Erfassung benötigt werden, manuell erstellen: Event Grid-Abonnement, Event Hub-Namespace und Event Hub. 
[Erstellen eines privaten Endpunkts oder eines Dienstendpunkts für Event Hub und Azure Storage](vnet-endpoint-storage-event-hub.md) | Neuer Artikel. Ein [privater Endpunkt](/azure/private-link/private-endpoint-overview) verwendet eine IP-Adresse aus dem Adressraum Ihres VNET für den Azure-Dienst, um eine sichere Verbindung zwischen Azure Data Explorer und Azure-Diensten wie Azure Storage und Event Hub zu ermöglichen.  
[EngineV3 (Vorschauversion)](engine-v3.md) | Neuer Artikel. Die Kusto-EngineV3 ist die Speicher- und Abfrage-Engine der nächsten Generation von Azure Data Explorer. 
[Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe von Go](create-cluster-database-go.md) |  Neuer Artikel. In diesem Artikel erstellen Sie mithilfe von [Go](https://golang.org/) einen Azure Data Explorer-Cluster und eine Datenbank. 
[Erstellen von Power Apps-Anwendungen zum Abfragen von Daten in Azure Data Explorer (Vorschau)](power-apps-connector.md) | Neuer Artikel. In diesem Artikel erstellen Sie eine Power Apps-Anwendung zum Abfragen von Azure Data Explorer-Daten. 
|[Microsoft Logic App und Azure Data Explorer (Vorschau)](kusto/tools/logicapps.md) |Aktualisierter Artikel. Allgemeine Verfügbarkeit (General Availability, GA)
| [Optimieren Ihres Azure Data Explorer-Clusters mithilfe von Azure Advisor-Empfehlungen](azure-advisor.md) | Aktualisierter Artikel. Neue Azure Advisor-Empfehlung
| [Verwenden der Follower-Datenbank zum Anfügen von Datenbanken in Azure Data Explorer](follower.md) | Aktualisierter Artikel. Verwenden Sie PowerShell, um Follower-Datenbanken anzufügen und zu trennen. 

### <a name="query"></a>Abfrage

Artikeltitel | BESCHREIBUNG
---|---
[project-keep-Operator](./kusto/query/project-keep-operator.md) | Neuer Artikel. Wählen Sie aus, welche Spalten aus der Eingabe in der Ausgabe erhalten bleiben sollen.
[bag_remove_keys()](./kusto/query/bag-remove-keys-function.md) | Neuer Artikel. Entfernt Schlüssel und zugeordnete Werte aus einem Eigenschaftenbehälter vom Typ `dynamic`.
[series_fit_poly()](./kusto/query/series-fit-poly-function.md) | Neuer Artikel. Wendet eine polynomiale Regression aus einer unabhängigen Variablen (x_series) auf eine abhängige Variable (y_series) an. 
[array_sort_asc()](./kusto/query/arraysortascfunction.md) | Neuer Artikel. Empfängt einzelne oder mehrere Arrays. Sortiert das erste Array in aufsteigender Reihenfolge. Sortiert die verbleibenden Arrays so, dass sie zum neu sortierten ersten Array passen.
[array_sort_desc()](./kusto/query/arraysortdescfunction.md) | Neuer Artikel. Empfängt einzelne oder mehrere Arrays. Sortiert das erste Array in absteigender Reihenfolge. Sortiert die verbleibenden Arrays so, dass sie zum neu sortierten ersten Array passen.

### <a name="management"></a>Verwaltung

 Artikeltitel | BESCHREIBUNG
---|---
[Befehle der Abfragedrosselungsrichtlinie](./kusto/management/query-throttling-policy-commands.md) | Neuer Artikel. Die [Abfragedrosselungsrichtlinie](kusto/management/query-throttling-policy.md) ist eine Richtlinie auf Clusterebene und dient zum Einschränken der Abfrageparallelität im Cluster. 
[Abfragedrosselungsrichtlinie](./kusto/management/query-throttling-policy.md) | Neuer Artikel. Definieren Sie die Abfragedrosselungsrichtlinie, um die Anzahl von Abfragen einzuschränken, die der Cluster gleichzeitig ausführen kann. 
[.clear table data](./kusto/management/clear-table-data-command.md) | Neuer Artikel. Löscht die Daten einer vorhandenen Tabelle (einschließlich der Streamingerfassungsdaten).
|  [Richtlinienbefehl für „row_level_security“](kusto/management/row-level-security-policy.md) <br> [Sicherheit auf Zeilenebene](kusto/management/rowlevelsecuritypolicy.md) |Aktualisierte Artikel. Allgemeine Verfügbarkeit (General Availability, GA)

### <a name="functions-library"></a>Functions-Bibliothek

 Artikeltitel | BESCHREIBUNG
---|---
[kmeans_fl()](./kusto/functions-library/kmeans-fl.md) | Neuer Artikel.  Die Funktion `kmeans_fl()` gruppiert ein Dataset mit dem [K-Means-Algorithmus](https://en.wikipedia.org/wiki/K-means_clustering).
[series_dot_product_fl()](./kusto/functions-library/series-dot-product-fl.md) | Neuer Artikel. Berechnet das Skalarprodukt zweier numerischer Vektoren.

## <a name="september-2020"></a>September 2020

In diesem Abschnitt sind die wichtigsten Dokumentationsänderungen für September 2020 aufgeführt.

#### <a name="general"></a>Allgemein

 Artikeltitel | BESCHREIBUNG
---|---
[Erstellen einer Tabelle in Azure Data Explorer (Vorschauversion)](one-click-table.md) | Neuer Artikel.  In diesem Artikel erfahren Sie, wie Sie über die Webbenutzeroberfläche von Azure Data Explorer schnell eine Tabelle und eine Schemazuordnung erstellen. 
[Aktivieren von isoliertem Computing in Ihrem Azure Data Explorer-Cluster](isolated-compute.md) | Neuer Artikel. Mit virtuellen Computern (virtual machines, VMs) für isoliertes Computing können Kunden ihre Workload in einer hardwareisolierten, für einen einzelnen Kunden dedizierten Umgebung ausführen.
[Optimieren Ihres Azure Data Explorer-Clusters mithilfe von Azure Advisor-Empfehlungen (Vorschau)](azure-advisor.md) | Neuer Artikel.  Azure Advisor analysiert die Konfigurationen und Nutzungstelemetriedaten Ihrer Azure Data Explorer-Cluster und bietet personalisierte und handlungsrelevante Empfehlungen, die Ihnen bei der Optimierung von Clustern helfen.
[Anpassen der visuellen Elemente des Azure Data Explorer-Dashboards](dashboard-customize-visuals.md) |Neuer Artikel. In diesem Dokument werden die verschiedenen Arten von visuellen Elementen erläutert und verschiedene Optionen beschrieben, mit denen Dashboardbenutzer ihre visuellen Elemente anpassen können.
| [Erstellen eines privaten Endpunkts in Ihrem Azure Data Explorer-Cluster in Ihrem virtuellen Netzwerk (Vorschau)](vnet-create-private-endpoint.md) | Aktualisierter Artikel. Allgemeine Verfügbarkeit (General Availability, GA)
| [Visualisieren von Daten über Azure Data Explorer in Grafana](grafana.md) | Aktualisierter Artikel. Mit neuen Funktionen aktualisiert
| [Visualisieren von Daten mit Azure Data Explorer-Dashboards](azure-data-explorer-dashboards.md) <br> [Verwenden von Parametern in Azure Data Explorer-Dashboards](dashboard-parameters.md) | Aktualisierte Artikel. Mit neuen Funktionen aktualisiert

#### <a name="query"></a>Abfrage

 Artikeltitel | BESCHREIBUNG
---|---
[zlib_compress_to_base64_string()](./kusto/query/zlib-base64-compress.md) | Neuer Artikel.  Führt die zlib-Komprimierung durch und codiert das Ergebnis in „base64“.
[zlib_decompress_from_base64_string()](./kusto/query/zlib-base64-decompress.md) |Neuer Artikel.  Decodiert die Eingabezeichenfolge aus „base64“ und führt die zlib-Dekomprimierung durch.
[cosmosdb_sql_request plugin](./kusto/query/cosmosdb-plugin.md) | Neuer Artikel. Das Plug-In `cosmosdb_sql_request` sendet eine SQL-Abfrage an einen Cosmos DB-SQL-Netzwerkendpunkt und gibt die Ergebnisse der Abfrage zurück. 
[Funktion „materialized_view()“](./kusto/query/materialized-view-function.md) |Neuer Artikel.  Verweist auf den materialisierten Teil einer [materialisierten Sicht](kusto/management/materialized-views/materialized-view-overview.md). 
| [geo_distance_point_to_line()](./kusto/query/geo-distance-point-to-line-function.md) | Aktualisierter Artikel. Dokumentation für Multilinienunterstützung hinzugefügt
|[geo_line_densify()](./kusto/query/geo-line-densify-function.md)| Aktualisierter Artikel. Dokumentation für Multilinienunterstützung hinzugefügt |

#### <a name="management"></a>Verwaltung

 Artikeltitel | BESCHREIBUNG
---|---
[.create materialized-view](./kusto/management/materialized-views/materialized-view-create.md) | Neuer Artikel.  Eine [materialisierte Sicht](kusto/management/materialized-views/materialized-view-overview.md) ist eine Aggregationsabfrage für eine Quelltabelle und stellt eine einzelne Zusammenfassungsanweisung dar.
[Materialisierte Sichten (Vorschau)](./kusto/management/materialized-views/materialized-view-overview.md) | Neuer Artikel.  Von [materialisierten Sichten](kusto/query/materialized-view-function.md) wird eine für eine Quelltabelle ausgeführte *Aggregationsabfrage* verfügbar gemacht. 
[.alter materialized-view](./kusto/management/materialized-views/materialized-view-alter.md) | Neuer Artikel. Durch Ändern der [materialisierten Sicht](kusto/management/materialized-views/materialized-view-overview.md) kann die Abfrage einer materialisierten Sicht geändert werden, ohne die vorhandenen Daten in der Sicht zu verlieren.
[.disable .enable materialized-view](./kusto/management/materialized-views/materialized-view-enable-disable.md) | Neuer Artikel. Beschreibt das Deaktivieren einer materialisierten Sicht.
[.drop materialized-view](./kusto/management/materialized-views/materialized-view-drop.md) | Neuer Artikel. Löscht eine materialisierte Sicht.
[Befehle vom Typ „.show materialized-views“](./kusto/management/materialized-views/materialized-view-show-commands.md) | Neuer Artikel. Die hier beschriebenen Anzeigebefehle dienen zum Anzeigen von Informationen zu [materialisierten Sichten](kusto/management/materialized-views/materialized-view-overview.md).

#### <a name="functions-library"></a>Functions-Bibliothek

 Artikeltitel | BESCHREIBUNG
---|---
[Functions-Bibliothek](./kusto/functions-library/functions-library.md) | Neuer Artikel. Dieser Artikel enthält eine kategorisierte Liste mit [benutzerdefinierten Funktionen (User-Defined Functions, UDFs)](kusto/query/functions/user-defined-functions.md). 

#### <a name="api"></a>API

 Artikeltitel | BESCHREIBUNG
---|---
[Azure Data Explorer Golang SDK](./kusto/api/golang/kusto-golang-client-library.md) | Neuer Artikel. Die Azure Data Explorer-Go-Clientbibliothek ermöglicht die Abfrage, Steuerung und Erfassung in Azure Data Explorer-Clustern mithilfe von Go. 
[Erfassung ohne die Bibliothek „Kusto.Ingest“](./kusto/api/netfx/kusto-ingest-client-rest.md) | Neuer Artikel. Die Bibliothek „Kusto.Ingest“ wird für die Erfassung von Daten in Azure Data Explorer bevorzugt. Aber auch ohne Abhängigkeit vom Paket „Kusto.Ingest“ können nahezu die gleichen Funktionen erreicht werden.

## <a name="august-2020"></a>August 2020

In diesem Abschnitt sind die wichtigsten Dokumentationsänderungen für August 2020 aufgeführt.

### <a name="general"></a>Allgemein

 Artikeltitel | BESCHREIBUNG
---|---
[Aktivieren der Infrastrukturverschlüsselung (doppelte Verschlüsselung) während der Clustererstellung in Azure Data Explorer](double-encryption.md) | Neuer Artikel. Wenn Sie Ihre Daten noch besser schützen möchten, können Sie auch die [Azure Storage-Verschlüsselung auf Infrastrukturebene](/azure/storage/common/infrastructure-encryption-enable) aktivieren (auch als doppelte Verschlüsselung bezeichnet).
[Erfassen von Daten mit dem Go SDK für Azure Data Explorer](go-ingest-data.md) | Neuer Artikel. Mit dem [Go SDK](https://github.com/Azure/azure-kusto-go) können Daten in Azure Data Explorer-Clustern erfasst, gesteuert und abgefragt werden. 
[Übersicht über Business Continuity &amp; Disaster Recovery](business-continuity-overview.md) | Neuer Artikel. Business Continuity & Disaster Recovery in Azure Data Explorer ermöglicht es Ihrem Unternehmen, den Geschäftsbetrieb im Falle einer Störung aufrechtzuerhalten. 
[Herstellen einer Verbindung mit Event Grid](ingest-data-event-grid-overview.md) | Neuer Artikel. Die Event Grid-Erfassung ist eine Pipeline, die auf Azure Storage lauscht und Azure Data Explorer aktualisiert, um Informationen zu pullen, wenn abonnierte Ereignisse auftreten. 
[Erstellen von BCDR-Lösungen (Business Continuity &amp; Disaster Recovery) mit Azure Data Explorer](business-continuity-create-solution.md) | Neuer Artikel. In diesem Artikel erfahren Sie, wie Sie sich auf einen regionalen Azure-Ausfall vorbereiten können, indem Sie Ihre Azure Data Explorer-Ressourcen, Ihre Verwaltung und Ihre Erfassung in verschiedenen Azure-Regionen replizieren.

### <a name="query"></a>Abfrage

 Artikeltitel | BESCHREIBUNG
---|---
[series_fft()](./kusto/query/series-fft-function.md) | Neuer Artikel. Wendet die schnelle Fourier-Transformation (Fast Fourier Transform, FFT) auf eine Reihe an.  
[series_ifft()](./kusto/query/series-ifft-function.md) | Neuer Artikel. Wendet die inverse schnelle Fourier-Transformation (Inverse Fast Fourier Transform, IFFT) auf eine Reihe an. 
[dynamic_to_json()](./kusto/query/dynamic-to-json-function.md) | Neuer Artikel. Konvertiert eine Eingabe vom Typ `dynamic` in eine Zeichenfolgendarstellung. 
[format_ipv4()](./kusto/query/format-ipv4-function.md) | Neuer Artikel. Analysiert eine Eingabe mit einer Netzmaske und gibt eine Zeichenfolge zurück, die eine IPv4-Adresse darstellt.
[format_ipv4_mask()](./kusto/query/format-ipv4-mask-function.md) | Neuer Artikel. Analysiert eine Eingabe mit einer Netzmaske und gibt eine Zeichenfolge, die eine IPv4-Adresse darstellt, als CIDR-Notation zurück.

### <a name="management"></a>Verwaltung

 Artikeltitel | BESCHREIBUNG
---|---- 
[Erstellen oder Ändern eines fortlaufenden Exports](./kusto/management/data-export/create-alter-continuous.md) | Neuer Artikel. Erstellt oder ändert einen Auftrag für einen fortlaufenden Export.
[Deaktivieren oder Aktivieren eines fortlaufenden Exports](./kusto/management/data-export/disable-enable-continuous.md) | Neuer Artikel. Deaktiviert oder aktiviert den Auftrag für einen fortlaufenden Export. 
[Löschen eines fortlaufenden Exports](./kusto/management/data-export/drop-continuous-export.md) | Neuer Artikel. Löscht einen Auftrag für einen fortlaufenden Export.
[Anzeigen der Artefakte eines fortlaufenden Exports](./kusto/management/data-export/show-continuous-artifacts.md) | Neuer Artikel. Gibt alle Artefakte zurück, die durch den fortlaufenden Export in allen Ausführungen exportiert wurden. Filtern Sie die Ergebnisse im Befehl nach der Zeitstempelspalte, um nur die für Sie interessanten Datensätze anzuzeigen.
[Anzeigen eines fortlaufenden Exports](./kusto/management/data-export/show-continuous-export.md) | Neuer Artikel. Gibt die Eigenschaften des fortlaufenden Exports auf der Grundlage des *Namens des fortlaufenden Exports* zurück. 
[Anzeigen der Fehler bei einem fortlaufenden Export](./kusto/management/data-export/show-continuous-failures.md) | Neuer Artikel. Gibt alle Fehler zurück, die im Rahmen des fortlaufenden Exports protokolliert wurden. Filtern Sie die Ergebnisse im Befehl nach der Zeitstempelspalte, um nur den für Sie interessanten Zeitbereich anzuzeigen. 
| [Sicherheit auf Zeilenebene](kusto/management/rowlevelsecuritypolicy.md) | Aktualisierter Artikel. Hier erfahren Sie, wie Sie einen Fehler für nicht autorisierten Zugriff generieren.
|[Erstellen und Ändern externer Tabellen in Azure Storage oder Azure Data Lake](./kusto/management/external-tables-azurestorage-azuredatalake.md) <br> <br> [Erstellen und Ändern externer SQL-Tabellen](./kusto/management/external-sql-tables.md) | Aktualisierte Artikel. Neue Befehlsoption `.create-or-alter`

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Mitwirkung an der Dokumentation zu Azure Data Explorer finden Sie im [Leitfaden für Dokumentationsmitwirkende](https://docs.microsoft.com/contribute/).
