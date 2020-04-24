---
title: Speicherverbindungszeichenfolgen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Speicherverbindungszeichenfolgen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 909198e42786666d3a26874e18b5cfd57b27ab1f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502957"
---
# <a name="storage-connection-strings"></a>Storage-Verbindungszeichenfolgen

Einige Kusto-Befehle weisen Kusto an, mit externen Speicherdiensten zu interagieren. Kusto kann beispielsweise angewiesen werden, Daten in ein Azure Storage Blob zu exportieren, in diesem Fall müssen die spezifischen Parameter (z. B. Speicherkontoname oder Blobcontainer) bereitgestellt werden.

Kusto unterstützt die folgenden Speicheranbieter:


* Azure Storage Blob-Speicheranbieter
* Azure Data Lake Storage-Speicheranbieter

Jede Art eines Speicheranbieters definiert ein Verbindungszeichenfolgenformat, das verwendet wird, um die Speicherressourcen und den Zugriff darauf zu beschreiben.
Kusto verwendet ein URI-Format, um diese Speicherressourcen und die Eigenschaften zu beschreiben, die für den Zugriff auf diese Ressourcen erforderlich sind (z. B. Sicherheitsanmeldeinformationen).


|Anbieter                   |Schema    |URI-Vorlage                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage-Blob         |`https://`|`https://`*Kontocontainer*`.blob.core.windows.net/`*Container*`/`[*BlobName*][`?`*SasKey* \| `;` *AccountKey*]|
|Azure Data Lake Storage Gen 2|`abfss://`|`abfss://`*Dateisystemkonto*`@`*Account*`.dfs.core.windows.net/`*PathToDirectoryOrFile*[`;`*CallerCredentials*]|
|Azure Data Lake Storage Gen 1|`adl://`  |`adl://`*Konto*.azuredatalakestore.net/*PathToDirectoryOrFile*[`;`*CallerCredentials*]|

## <a name="azure-storage-blob"></a>Azure Storage-Blob

Dieser Anbieter ist der am häufigsten verwendete anbieter und wird in allen Szenarien unterstützt.
Der Anbieter muss beim Zugriff auf die Ressource Anmeldeinformationen erhalten. Es gibt zwei unterstützte Mechanismen für die Bereitstellung von Anmeldeinformationen:

* Geben Sie einen SAS-Schlüssel (Shared Access) mithilfe der`?sig=...`Standardabfrage des Azure Storage Blobs ( ) an. Verwenden Sie diese Methode, wenn Kusto für eine begrenzte Zeit auf die Ressource zugreifen muss.
* Geben Sie den`;ljkAkl...==`Speicherkontoschlüssel ( ) an. Verwenden Sie diese Methode, wenn Kusto fortlaufend auf die Ressource zugreifen muss.

Beispiele (beachten Sie, dass dadurch verschleierte Zeichenfolgenliterale angezeigt werden, um den Kontoschlüssel oder SAS nicht verfügbar zu machen):

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Storage Gen 2

Dieser Anbieter unterstützt den Zugriff auf Daten in Azure Data Lake Store Gen 2.

Das Format des URI ist:

`abfss://`*Dateisystem* `@` *StorageAccountName* `.dfs.core.windows.net/` *Path* `;` *CallerCredentials*

Hierbei gilt:

* _Dateisystem_ ist der Name des ADLS-Dateisystemobjekts (ungefähr gleichbedeutend mit Blob Container)
* _StorageAccountName_ ist der Name des Speicherkontos
* _Pfad_ ist der Pfad zum Verzeichnis oder der`/`Datei, auf die zugegriffen wird Das Schrägstrich ( ) wird als Trennzeichen verwendet.
* _CallerCredentials_ gibt die Anmeldeinformationen an, die für den Zugriff auf den Dienst verwendet werden, wie unten beschrieben.

Beim Zugriff auf Azure Data Lake Store Gen 2 muss der Aufrufer gültige Anmeldeinformationen für den Zugriff auf den Dienst angeben. Die folgenden Methoden zum Bereitstellen von Anmeldeinformationen werden unterstützt:

* Anfügen `;sharedkey=`von *AccountKey* an den URI, wobei _AccountKey_ der Speicherkontoschlüssel ist
* An `;impersonate` den URI anhängen. Kusto verwendet die Hauptidentität des Anforderers und verkörpert sie, um auf die Ressource zuzugreifen. Principal muss über die entsprechenden RBAC-Rollenzuweisungen verfügen, um die Lese-/Schreibvorgänge ausführen zu können, wie [hier](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)dokumentiert. (Die minimale Rolle für Lesevorgänge `Storage Blob Data Reader` ist z. B. die Rolle).
* Fügen `;token=`Sie *AadToken* an den URI an, wobei _AadToken_ ein Base-64-codiertes AAD-Zugriffstoken ist (stellen Sie sicher, dass das Token für die Ressource `https://storage.azure.com/`bestimmt ist).
* An `;prompt` den URI anhängen. Kusto fordert Benutzeranmeldeinformationen an, wenn es auf die Ressource zugreifen muss. (Die Aufforderung des Benutzers ist für Cloudbereitstellungen deaktiviert und nur in Testumgebungen aktiviert.)
* Geben Sie einen SAS-Schlüssel (Shared Access) mithilfe der Standardabfrage`?sig=...`von Azure Data Lake Storage Gen 2 ( an . Verwenden Sie diese Methode, wenn Kusto für eine begrenzte Zeit auf die Ressource zugreifen muss.



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Storage Gen 1

Dieser Anbieter unterstützt den Zugriff auf Dateien und Verzeichnisse in Azure Data Lake Store.
Sie muss mit Anmeldeinformationen versehen werden (Kusto verwendet keinen eigenen AAD-Prinzipal, um auf Azure Data Lake zuzugreifen.) Die folgenden Methoden zum Bereitstellen von Anmeldeinformationen werden unterstützt:

* An `;impersonate` den URI anhängen. Kusto verwendet die Hauptidentität des Anforderers und imitiert sie, um auf die Ressource zuzugreifen.
* Fügen `;token=`Sie _AadToken _to dem URI an, wobei _AadToken_ ein Base-64-codiertes AAD-Zugriffstoken ist (stellen Sie sicher, dass das Token für die Ressource `https://management.azure.com/`bestimmt ist).
* An `;prompt` den URI anhängen. Kusto fordert Benutzeranmeldeinformationen an, wenn es auf die Ressource zugreifen muss. (Die Aufforderung des Benutzers ist für Cloudbereitstellungen deaktiviert und nur in Testumgebungen aktiviert.)



