---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 56095df921864896dacdb100302d686d66f40572
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82109765"
---
## <a name="select-an-ingestion-type"></a>Auswahl eines Erfassungstyps

Wählen Sie für **Erfassungstyp** eine der folgenden Optionen aus:
   * **Aus Speicher**: Fügen Sie im Feld **Mit Speicher verknüpfen** die URL des Speicherkontos hinzu. Verwenden Sie die [Blob-SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) für private Speicherkonten.
   
      ![1-Klick-Erfassung aus Speicher](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **Aus Datei**: Wählen Sie **Durchsuchen** aus, um nach der Datei zu suchen, oder ziehen Sie die Datei in das Feld.
  
      ![1-Klick-Erfassung aus Datei](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **Aus Container**: Fügen Sie im Feld **Mit Speicher verknüpfen** die [SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) des Containers hinzu, und geben Sie optional die Stichprobengröße ein.

      ![1-Klick-Erfassung aus Container](media/data-explorer-one-click-ingestion-types/from-container.png)

  Es wird eine Stichprobe der Daten angezeigt. Sie können diese Anzeige filtern, um nur Dateien anzuzeigen, die mit bestimmten Zeichen beginnen und/oder auf bestimmte Zeichen enden. Die Vorschau wird automatisch aktualisiert, sobald Sie die Filter anpassen.
  
  Sie können die Anzeige beispielsweise so filtern, dass alle Dateien aufgelistet werden, die mit dem Begriff *Daten* beginnen und auf die Erweiterung *.csv.gz* enden.

  ![Filter bei der 1-Klick-Erfassung](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
