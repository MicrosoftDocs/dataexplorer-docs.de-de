---
title: Verwenden des Azure Data Explorer-Connectors für Apache Spark zum Verschieben von Daten zwischen Azure Data Explorer- und Spark-Clustern.
description: In diesem Thema erfahren Sie, wie Sie Daten zwischen Azure Data Explorer- und Apache Spark-Clustern verschieben.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 6e7879948fb7683210859c84970f31bc5adb4a90
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108319"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer-Connector für Apache Spark

[Apache Spark](https://spark.apache.org/) ist eine vereinheitlichte Engine zur Verarbeitung von umfangreichen Daten. Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Mengen an Daten. 

Der Azure Data Explorer-Connector für Spark ist ein [Open Source-Projekt](https://github.com/Azure/azure-kusto-spark), das auf jedem Spark-Cluster ausgeführt werden kann. Er implementiert Datenquellen und Datensenken zum Verschieben von Daten zwischen Azure Data Explorer und Spark-Clustern. Mit Azure Data Explorer und Apache Spark können Sie schnelle und skalierbare Anwendungen für datengesteuerte Szenarien erstellen. Beispiele dafür sind maschinelles Lernen (Machine Learning, ML), Extrahieren, Transformieren und Laden (Extract-Transform-Load, ETL) und Protokollanalysen (Log Analytics). Durch den Connector wird Azure Data Explorer ein gültiger Datenspeicher für Spark-Standardvorgänge für Quellen und Senken wie„write“, „read“ und „writeStream“.

Sie können im Batch- oder Streamingmodus in Azure Data Explorer schreiben. Das Lesen aus Azure Data Explorer unterstützt das Löschen von Spalten und die Prädikatweitergabe. Dabei werden die Daten in Azure Data Explorer gefiltert, wodurch die Menge der übertragenen Daten verringert wird.

In diesem Thema wird beschrieben, wie Sie den Azure Data Explorer-Connector für Spark installieren und konfigurieren und Daten zwischen Azure Data Explorer und Apache Spark-Clustern verschieben.

> [!NOTE]
> Obwohl sich einige der unten stehenden Beispiele auf einen [Azure Databricks](https://docs.azuredatabricks.net/) Spark-Cluster beziehen, akzeptiert Azure Data Explorer-Connector für Spark keine direkten Abhängigkeiten von Databricks oder einer anderen Spark-Distribution.

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](/azure/data-explorer/create-cluster-database-portal) 
* Erstellen eines Spark-Clusters
* Installieren Sie die Azure Data Explorer-Connectorbibliothek:
    * Vorgefertigte Bibliotheken für [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven-Repository](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi)-Installation

> [!TIP]
> 2.3.x-Versionen werden ebenfalls unterstützt, erfordern aber möglicherweise Änderungen in den „pom.xml“-Abhängigkeiten.

## <a name="how-to-build-the-spark-connector"></a>Erstellen des Spark-Connectors

> [!NOTE]
> Dieser Schritt ist optional. Wenn Sie vorgefertigte Bibliotheken verwenden, wechseln Sie zu [Einrichtung des Spark-Clusters](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Buildvoraussetzungen

1. Installieren Sie die unter [Abhängigkeiten](https://github.com/Azure/azure-kusto-spark#dependencies) aufgeführten Bibliotheken einschließlich der folgenden [Kusto Java SDK](kusto/api/java/kusto-java-client-library.md)-Bibliotheken:
    * [Kusto Data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Ziehe Sie [diese Quelle](https://github.com/Azure/azure-kusto-spark) zum Erstellen des Spark-Connectors zurate.

1. Wenn Sie Scala-/Java-Anwendungen mit Maven-Projektdefinitionen verwenden, verknüpfen Sie Ihre Anwendung mit dem folgenden Artefakt (die neueste Version kann abweichen):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Erstellen von Befehlen

Führen Sie den folgenden Befehl aus, um die JAR-Datei zu erstellen und alle Tests auszuführen:

```
mvn clean package
```

Um die JAR-Datei zu erstellen, führen Sie alle Tests aus, und installieren Sie die JAR-Datei in Ihrem lokalen Maven-Repository:

```
mvn clean install
```

Weitere Informationen finden Sie unter [Connectorverwendung](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Einrichtung des Spark-Clusters

> [!NOTE]
> Es wird empfohlen, die aktuelle Version des Azure Data Explorer-Connectors für Spark zu verwenden, wenn Sie die folgenden Schritte ausführen.

1. Konfigurieren Sie die folgenden Spark-Clustereinstellungen basierend auf dem Azure Databricks-Cluster unter Verwendung von Spark 2.4.4 und Scala 2.11:

    ![Databricks-Clustereinstellungen](media/spark-connector/databricks-cluster.png)
    
1. Installieren Sie die neueste Spark-Kusto-Connectorbibliothek von Maven:
    
    ![Bibliotheken importieren](media/spark-connector/db-libraries-view.png) ![Spark-Kusto-Connector auswählen](media/spark-connector/db-dependencies.png)

1. Überprüfen Sie, ob alle erforderlichen Bibliotheken installiert sind:

    ![Überprüfen von installierten Bibliotheken](media/spark-connector/db-libraries-view.png)

1. Überprüfen Sie bei der Installation mithilfe einer JAR-Datei, ob zusätzliche Abhängigkeiten installiert wurden:

    ![Hinzufügen von Abhängigkeiten](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Authentifizierung

Der Azure Data Explorer-Connector für Spark ermöglicht Ihnen die Authentifizierung bei Azure Active Directory (Azure AD) mit einer der folgenden Methoden:
* [Azure AD-Anwendung](#azure-ad-application-authentication)
* [Azure AD-Zugriffstoken](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#direct-authentication-with-access-token)
* [Geräteauthentifizierung](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#device-authentication) (in Nicht-Produktionsszenarien)
* [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#key-vault): Um auf die Key Vault-Ressource zugreifen zu können, müssen Sie das „azure-keyvault“-Paket installieren und die Anwendungsanmeldeinformationen bereitstellen.

### <a name="azure-ad-application-authentication"></a>Azure AD-Anwendungsauthentifizierung

Die Azure AD-Anwendungsauthentifizierung ist die einfachste und gängigste Authentifizierungsmethode. Sie wird für den Azure Data Explorer-Connector für Spark empfohlen.

|Eigenschaften  |BESCHREIBUNG  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   (Client-)Bezeichner der Azure AD-Anwendung.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-Authentifizierungsautorität. Azure AD Directory-(Mandanten-)ID.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-Anwendungsschlüssel für den Client.     |

### <a name="azure-data-explorer-privileges"></a>Azure Data Explorer-Berechtigungen

Erteilen Sie die folgenden Berechtigungen für einen Azure Data Explorer-Cluster:

* Zum Lesen (Datenquelle) muss die Azure AD-Identität über Berechtigungen zum *Anzeigen* für die Zieldatenbank oder über *Administratorberechtigungen* für die Zieltabelle verfügen.
* Zum Schreiben (Datensenke) muss die Azure AD-Identität über Berechtigungen zum *Erfassen* für die Zieldatenbank verfügen. Außerdem benötigt sie *Benutzer*berechtigungen für die Zieldatenbank, um neue Tabellen zu erstellen. Wenn die Zieltabelle bereits vorhanden ist, müssen Sie *Administratorberechtigungen* für die Zieltabelle konfigurieren.
 
Weitere Informationen zu Azure Data Explorer-Prinzipalrollen finden Sie unter [Rollenbasierte Autorisierung](kusto/management/access-control/role-based-authorization.md). Informationen zum Verwalten von Sicherheitsrollen finden Sie unter [Sicherheitsrollenverwaltung](kusto/management/security-roles.md).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-Senke: Schreiben in Azure Data Explorer

1. Einrichten von Senkenparametern:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Schreiben von Spark DataFrame in Azure Data Explorer-Cluster als Batch:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Oder verwenden Sie die vereinfachte Syntax:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Schreiben von Streamingdaten:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-Senke: Lesen aus Azure Data Explorer

1. Wenn [kleine Datenmengen](kusto/concepts/querylimits.md) gelesen werden, definieren Sie die Datenabfrage:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Optional: Wenn **Sie** den temporären Blobspeicher (und nicht Azure Data Explorer) bereitstellen, werden die Blobs in der Verantwortung des Aufrufers erstellt. Dies umfasst das Bereitstellen des Speichers, das Rotieren von Zugriffsschlüsseln und das Löschen temporärer Artefakte. 
    Das KustoBlobStorageUtils-Modul enthält Hilfsfunktionen zum Löschen von Blobs, die entweder auf Konto- und Containerkoordinaten und Kontoanmeldeinformationen oder einer vollständigen SAS-URL mit Schreib-, Lese- und Listenberechtigungen basieren. Wenn das entsprechende RDD nicht mehr benötigt wird, speichert jede Transaktion temporäre Blobartefakte in einem separaten Verzeichnis. Dieses Verzeichnis wird als Teil der Lesetransaktionsinformationsprotokolle erfasst, die auf dem Spark-Treiberknoten gemeldet werden.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Im obigen Beispiel erfolgt der Zugriff auf den Schlüsseltresor nicht über die Connectorschnittstelle. Es wird eine einfachere Methode der Verwendung der Databricks-Geheimnisse verwendet.

1. Lesen aus Azure Data Explorer.

    * Wenn **Sie** den temporären Blobspeicher bereitstellen, lesen Sie die Daten aus Azure Data Explorer wie folgt:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Wenn **Azure Data Explorer** den temporären Blobspeicher bereitstellt, lesen Sie die Daten aus Azure Data Explorer wie folgt:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Azure Data Explorer-Spark-Connector](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Beispielcode für Java und Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
