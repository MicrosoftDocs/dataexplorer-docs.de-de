---
title: Verwalten des horizontalen Skalierens (Aufskalieren) eines Clusters zum Anpassen an den Bedarf in Azure Data Explorer
description: Dieser Artikel beschreibt Schritte zum Auf- und Abskalieren eines Azure Data Explorer-Clusters basierend auf sich änderndem Bedarf.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494470"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Verwalten des horizontalen Skalierens (Aufskalieren) eines Clusters in Azure Data Explorer zur Anpassung an sich ändernden Bedarf

Die richtige Größe eines Clusters ist entscheidend für die Leistung von Azure-Daten-Explorer. Eine statische Clustergröße kann zu einer Unter- oder Überauslastung führen, was beides nicht ideal ist. Da der Bedarf für einen Cluster nicht mit absoluter Genauigkeit vorhergesagt werden kann, ist es besser, einen Cluster zu *skalieren* und je nach Bedarf Kapazität und CPU-Ressourcen hinzuzufügen bzw. zu entfernen. 

Es gibt zwei Workflows für die Skalierung eines Azure Data Explorer-Clusters: 
* Horizontales Hoch- oder Herunterskalieren
* [Zentrales Hoch- oder Herunterskalieren](manage-cluster-vertical-scaling.md)
In diesem Artikel wird der Workflow für die horizontale Skalierung beschrieben.

## <a name="configure-horizontal-scaling"></a>Konfigurieren der horizontalen Skalierung

Bei Verwendung der horizontalen Skalierung können Sie die Instanzenanzahl anhand von vordefinierten Regeln und Zeitplänen automatisch skalieren. Geben Sie die Einstellungen für die Autoskalierung für Ihren Cluster wie folgt an:

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Data Explorer-Clusterressource. Wählen Sie unter **Einstellungen** die Option **Aufskalieren** aus. 

2. Wählen Sie im Fenster **Aufskalieren** die gewünschte Methode für die Autoskalierung aus: **Manuelle Skalierung**, **Optimierte Autoskalierung** oder **Benutzerdefinierte Autoskalierung**.

### <a name="manual-scale"></a>Manuelles Skalieren

„Manuelle Skalierung“ ist die Standardeinstellung bei der Clustererstellung. Der Cluster verfügt über eine statische Kapazität, die sich nicht automatisch ändert. Sie wählen die statische Kapazität über die Leiste **Anzahl von Instanzen** aus. Für die Skalierung des Clusters wird diese Einstellung so lange beibehalten, bis Sie eine weitere Änderung vornehmen.

   ![Manuelle Skalierung](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Optimierte Autoskalierung (Vorschau)

Die optimierte Autoskalierung ist die empfohlene Methode für die Autoskalierung. Mit dieser Methode werden die Leistung und die Kosten des Clusters optimiert. Wenn sich der Cluster einem Status mit zu geringer Auslastung nähert, wird er horizontal herunterskaliert. Durch diese Aktion werden die Kosten verringert, während gleichzeitig die Leistungsstufe erhalten bleibt. Wenn sich der Cluster einem Status mit zu hoher Auslastung nähert, wird er horizontal hochskaliert, damit die optimale Leistung erhalten bleibt. Konfigurieren Sie die optimierte Autoskalierung wie folgt:

1. Wählen Sie **Optimierte Autoskalierung**. 

1. Wählen Sie eine Mindest- und eine Höchstzahl von Instanzen aus. Die automatische Skalierung des Clusters bewegt sich dann je nach Last zwischen diesen beiden Werten.

1. Wählen Sie **Speichern** aus.

   ![Optimierte Autoskalierung](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Die optimierte automatische Skalierung beginnt mit der Arbeit. Ihre Aktionen sind im Azure-Aktivitätsprotokoll des Clusters jetzt sichtbar.

#### <a name="logic-of-optimized-autoscale"></a>Logik der optimierten Autoskalierung 

**Aufskalieren**

Wenn sich der Cluster einem Status mit zu hoher Auslastung nähert, wird er aufskaliert, damit die optimale Leistung erhalten bleibt. Das Aufskalieren tritt in folgenden Situationen auf:
* Die Anzahl der Clusterinstanzen liegt unter der maximalen Anzahl von Instanzen, die vom Benutzer definiert wurden.
* Die Cacheauslastung ist länger als eine Stunde sehr hoch.
* Die CPU-Auslastung ist für mehr als eine Stunde hoch.
* Die Erfassungsauslastung ist seit über einer Stunde hoch.

> [!NOTE]
> Die Logik für das Aufskalieren berücksichtigt derzeit nicht die Metrik zur Erfassungsauslastung. Wenn diese Metrik für Ihren Anwendungsfall wichtig ist, verwenden Sie die [benutzerdefinierte Autoskalierung](#custom-autoscale).

**Abskalieren**

Wenn sich der Cluster einem Status mit zu niedriger Auslastung nähert, wird er abskaliert, um bei gleichbleibender Leistung die Kosten zu senken. Es werden mehrere Metriken verwendet, um zu überprüfen, ob das Abskalieren des Clusters sicher ist. Die folgenden Regeln werden 6 Stunden lang stündlich ausgewertet, bevor eine horizontale Herunterskalierung erfolgt:
* Die Anzahl der Instanzen liegt über 2 und über der definierten Mindestanzahl von Instanzen.
* Um sicherzustellen, dass keine Ressourcen überladen werden, müssen die folgenden Metriken überprüft werden, bevor ein Abskalieren erfolgt: 
    * Die Cacheauslastung ist nicht hoch.
    * Die CPU-Auslastung liegt unter dem Durchschnitt. 
    * Die Erfassungsauslastung liegt unter dem Durchschnitt. 
    * Die Auslastung bei der Streamingerfassung (sofern verwendet) ist nicht hoch.
    * Keep-Alive-Ereignisse liegen oberhalb eines definierten Mindestwerts und werden ordnungsgemäß und rechtzeitig verarbeitet.
    * Es erfolgt keine Abfragedrosselung. 
    * Die Anzahl der fehlerhaften Abfragen liegt unter einem definierten Mindestwert.

> [!NOTE]
> Die Logik zum Abskalieren erfordert derzeit eine 7-tägige Auswertung, bevor eine optimierte Abskalierung erfolgt. Diese Auswertung erfolgt einmal alle 24 Stunden. Wenn eine schnelle Änderung erforderlich ist, nutzen Sie die [manuelle Skalierung](#manual-scale).

### <a name="custom-autoscale"></a>Benutzerdefinierte Autoskalierung

Bei Verwendung der benutzerdefinierten Autoskalierung können Sie Ihren Cluster basierend auf den von Ihnen angegebenen Metriken dynamisch skalieren. Die folgende Grafik zeigt den Ablauf und die Schritte zum Konfigurieren der benutzerdefinierten Autoskalierung. Weitere Details finden Sie in der Grafik.

1. Geben Sie im Feld **Name der Einstellung für die Autoskalierung** einen Namen ein, z. B. *Horizontale Skalierung: Cacheauslastung*. 

   ![Skalierungsregel](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Wählen Sie unter **Skalierungsmodus** die Option **Basierend auf einer Metrik skalieren** aus. Dieser Modus ermöglicht die dynamische Skalierung. Sie können auch **Auf eine bestimmte Anzahl von Instanzen skalieren** auswählen.

3. Wählen Sie **+ Regel hinzufügen** aus.

4. Geben Sie im Abschnitt **Skalierungsregel** auf der rechten Seite Werte für jede Einstellung ein.

    **Kriterien**

    | Einstellung | Beschreibung und Wert |
    | --- | --- |
    | **Zeitaggregation** | Wählen Sie ein Aggregationskriterium aus, z. B. **Durchschnitt**. |
    | **Metrikname** | Wählen Sie die Metrik aus, auf der der Skalierungsvorgang basieren soll, z. B. **Cacheauslastung**. |
    | **Statistik zum Aggregationsintervall** | Wählen Sie zwischen **Durchschnitt**, **Minimum**, **Maximum** und **Summe** aus. |
    | **Operator** | Wählen Sie die entsprechende Option aus, z. B. **Größer als oder gleich**. |
    | **Schwellenwert** | Wählen Sie einen passenden Wert aus. Für die Cacheauslastung ist beispielsweise ist 80 % ein guter Ausgangspunkt. |
    | **Dauer (in Minuten)** | Wählen Sie eine angemessene Zeitspanne, die das System bei der Berechnung von Metriken berücksichtigt. Beginnen Sie mit dem Standardwert von 10 Minuten. |
    |  |  |

    **Aktion**

    | Einstellung | Beschreibung und Wert |
    | --- | --- |
    | **Vorgang** | Wählen Sie die entsprechende Option zum Auf- oder Abskalieren aus. |
    | **Anzahl der Instanzen** | Wählen Sie die Anzahl von Knoten oder Instanzen aus, die hinzugefügt oder entfernt werden sollen, wenn eine Metrikbedingung erfüllt ist. |
    | **Abkühlen (Minuten)** | Wählen Sie ein geeignetes Intervall für die Zeit zwischen den einzelnen Skalierungsvorgängen. Beginnen Sie mit dem Standardwert von 5 Minuten. |
    |  |  |

5. Wählen Sie **Hinzufügen**.

6. Geben Sie im Abschnitt **Instanzgrenzwerte** auf der linken Seite Werte für jede Einstellung ein.

    | Einstellung | Beschreibung und Wert |
    | --- | --- |
    | **Mindestanforderungen** | Die Anzahl der Instanzen, unter die Ihr Cluster unabhängig von der Auslastung nicht herunterskaliert wird. |
    | **Maximum** | Die Anzahl der Instanzen, über die Ihr Cluster unabhängig von der Auslastung nicht hochskaliert wird. |
    | **Standard** | Die Standardanzahl von Instanzen. Diese Einstellung wird verwendet, wenn Probleme beim Lesen der Ressourcenmetriken auftreten. |
    |  |  |

7. Wählen Sie **Speichern** aus.

Sie haben nun die horizontale Skalierung für Ihren Azure Data Explorer-Cluster konfiguriert. Fügen Sie eine weitere Regel für die vertikale Skalierung hinzu. Wenn Sie Hilfe bei Clusterskalierungsproblemen benötigen, erstellen Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eine Supportanfrage.

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen der Azure Data Explorer-Leistung, -Integrität und -Auslastung mit Metriken](using-metrics.md)
* [Manage cluster vertical scaling (scale up) in Azure Data Explorer to accommodate changing demand](manage-cluster-vertical-scaling.md) (Verwalten der vertikalen Clusterskalierung (Hochskalieren) in Azure Data Explorer bei sich änderndem Bedarf)
