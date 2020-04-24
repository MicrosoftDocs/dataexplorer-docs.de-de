---
title: Massenkopieren aus einer Datenbank in Azure Data Explorer mithilfe der Azure Data Factory-Vorlage
description: In diesem Artikel erfahren Sie, wie Sie eine Azure Data Factory-Vorlage zum Massenkopieren aus einer Datenbank in Azure Data Explorer verwenden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493226"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Massenkopieren aus einer Datenbank in Azure Data Explorer mithilfe der Azure Data Factory-Vorlage 

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst. Er bietet Echtzeitanalysen großer Datenmengen, die aus vielfältigen Quellen wie Anwendungen, Websites und IoT-Geräten gestreamt werden. 

Sie müssen große Datenmengen aus mehreren Tabellen laden, um Daten aus einer Datenbank in Oracle Server, Netezza, Teradata oder SQL Server in Azure Data Explorer kopieren zu können. In der Regel müssen die Daten in den einzelnen Tabellen partitioniert werden, um Zeilen mit mehreren Threads parallel aus einer einzelnen Tabelle laden zu können. In diesem Artikel wird eine Vorlage für diese Szenarios beschrieben.

[Azure Data Factory-Vorlagen](/azure/data-factory/solution-templates-introduction) sind vordefinierte Data Factory-Pipelines. Mithilfe dieser Vorlagen können Sie schnell mit Data Factory beginnen und die Entwicklungszeit für Datenintegrationsprojekte reduzieren. 

Sie erstellen die Vorlage zum *Massenkopieren aus der Datenbank in Azure Data Explorer* mithilfe von *Lookup*- und *ForEach*-Aktivitäten. Sie können die Vorlage zum schnelleren Kopieren von Daten verwenden, um pro Datenbank oder Tabelle viele Pipelines zu erstellen. 

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie das Tool verwenden, das für die zu kopierende Datenmenge geeignet ist.
> * Verwenden Sie die Vorlage zum *Massenkopieren aus der Datenbank in Azure Data Explorer*, um große Datenmengen aus Datenbanken wie SQL Server und Google BigQuery in Azure Data Explorer zu kopieren. 
> * Verwenden Sie das [*Data Factory-Tool „Daten kopieren“* ](data-factory-load-data.md), um einige Tabellen mit kleinen oder mittleren Datenmengen in Azure Data Explorer zu kopieren. 

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Schnellstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md).
* [Erstellen Sie eine Data Factory](data-factory-load-data.md#create-a-data-factory).
* Eine Datenquelle in der Datenbank

## <a name="create-controltabledataset"></a>Erstellen von ControlTableDataset

*ControlTableDataset* gibt an, welche Daten aus der Quelle in das Ziel in der Pipeline kopiert werden. Die Anzahl der Zeilen gibt die Gesamtanzahl der Pipelines an, die zum Kopieren der Daten erforderlich sind. ControlTableDataset muss als Teil der Quelldatenbank definiert werden.

Ein Beispiel für das SQL Server-Quelltabellenformat wird im folgenden Code gezeigt:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Die Codeelemente werden in der folgenden Tabelle beschrieben:

|Eigenschaft  |BESCHREIBUNG  | Beispiel
|---------|---------| ---------|
|PartitionId   |  Kopierauftrag | 1  |  
|SourceQuery   |  Die Abfrage, die angibt, welche Daten während der Pipelineruntime kopiert werden | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Name der Zieltabelle | MyAdxTable       |  

Wenn ControlTableDataset in einem anderen Format vorliegt, erstellen Sie ein vergleichbares ControlTableDataset für das Format.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Verwenden der Vorlage zum Massenkopieren aus einer Datenbank in Azure Data Explorer

1. Wählen Sie im Bereich **Erste Schritte** die Option **Pipeline aus Vorlage erstellen** aus, um den Bereich **Vorlagenkatalog** zu öffnen.

    ![Azure Data Factory – Bereich „Erste Schritte“](media/data-factory-template/adf-get-started.png)

1. Wählen Sie die Vorlage zum **Massenkopieren aus einer Datenbank in Azure Data Explorer** aus.
 
    ![Vorlage zum Massenkopieren aus einer Datenbank in Azure Data Explorer](media/data-factory-template/pipeline-from-template.png)

1.  Geben Sie im Bereich **Massenkopieren aus einer Datenbank in Azure Data Explorer** unter **Benutzereingaben** wie folgt die Datasets an: 

    a. Wählen Sie in der Dropdownliste **ControlTableDataset** den verknüpften Dienst für die Steuertabelle aus, der die aus der Quelle zu kopierenden Daten und den genauen Speicherort im Ziel angibt. 

    b. Wählen Sie in der Dropdownliste **SourceDataset** den verknüpften Dienst für die Quelldatenbank aus. 

    c. Wählen Sie in der Dropdownliste **AzureDataExplorerTable** die Azure Data Explorer-Tabelle aus. Wenn das Dataset nicht vorhanden ist, [erstellen Sie den mit Azure Data Explorer verknüpften Dienst](data-factory-load-data.md#create-the-azure-data-explorer-linked-service), um das Dataset hinzuzufügen.

    d. Klicken Sie auf **Diese Vorlage verwenden**.

    ![Bereich „Massenkopieren aus einer Datenbank in Azure Data Explorer“](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Wählen Sie einen Bereich in der Canvas außerhalb der Aktivitäten aus, um auf die Vorlagenpipeline zuzugreifen. Wählen Sie die Registerkarte **Parameter** aus, um die Parameter für die Tabelle einschließlich **Name** (Name der Steuertabelle) und **Standardwert** (Spaltennamen) einzugeben.

    ![Pipelineparameter](media/data-factory-template/pipeline-parameters.png)

1.  Wählen Sie unter **Lookup** die Option **GetPartitionList** aus, um die Standardeinstellungen anzuzeigen. Die Abfrage wird automatisch erstellt.
1.  Wählen Sie die Befehlsaktivität **ForEachPartition** und anschließend die Registerkarte **Einstellungen** aus. Gehen Sie dann wie folgt vor:

    a. Geben Sie im Feld **Batch count** (Batchanzahl) eine Zahl zwischen 1 und 50 ein. Diese Auswahl bestimmt die Anzahl der Pipelines, die parallel ausgeführt werden, bis die Anzahl der *ControlTableDataset*-Zeilen erreicht ist. 

    b. Um sicherzustellen, dass die Pipelinebatches parallel ausgeführt werden, aktivieren Sie *nicht* das Kontrollkästchen **Sequenziell**.

    ![ForEachPartition-Einstellungen](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Die bewährte Vorgehensweise ist die parallele Ausführung vieler Pipelines, damit Daten schneller kopiert werden können. Partitionieren Sie die Daten in der Quelltabelle, und weisen Sie nach Datum und Tabelle eine Partition pro Pipeline zu, um die Effizienz zu erhöhen.

1. Wählen Sie **Validate All** (Alle überprüfen) aus, um die Azure Data Factory-Pipeline zu überprüfen, und zeigen Sie dann das Ergebnis im Bereich **Pipeline Validation Output** (Pipelineüberprüfungsausgabe) an.

    ![Überprüfen der Vorlagenpipelines](media/data-factory-template/validate-template-pipelines.png)

1. Wählen Sie bei Bedarf **Debuggen** und dann **Trigger hinzufügen** aus, um die Pipeline auszuführen.

    ![Schaltflächen „Debuggen“ und „Build ausführen“](media/data-factory-template/trigger-run-of-pipeline.png)    

Sie können nun die Vorlage verwenden, um große Datenmengen effizient aus Datenbanken und Tabellen zu kopieren.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Daten mithilfe von Azure Data Factory in Azure Data Explorer kopieren](data-factory-load-data.md).
* Erfahren Sie mehr über den [Azure Data Explorer-Connector](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.
* Erfahren Sie mehr über [Azure Data Explorer-Abfragen](/azure/data-explorer/web-query-data) zum Abfragen von Daten.






