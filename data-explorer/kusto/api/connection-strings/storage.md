---
title: 'Speicher Verbindungs Zeichenfolgen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden Speicher Verbindungs Zeichenfolgen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 4b212435eb506ce71b52e19d3304461e9be35b5e
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342500"
---
# <a name="storage-connection-strings"></a>Storage-Verbindungszeichenfolgen

Einige Kusto-Befehle weisen Kusto an, mit externen Speicherdiensten zu interagieren. So kann z. b. von Kusto mitgeteilt werden, dass Daten in eine Azure Storage BLOB exportiert werden. in diesem Fall müssen die spezifischen Parameter (z. b. Speicherkonto Name oder BLOB-Container) bereitgestellt werden.

Kusto unterstützt die folgenden Speicher Anbieter:


* Azure Storage BLOB Speicher Anbieter
* Azure Data Lake Storage Speicher Anbieter

Jede Art von Speicheranbietern definiert ein Verbindungs Zeichen folgen Format, das zum Beschreiben der Speicherressourcen und zum Zugreifen auf diese verwendet wird.
Kusto verwendet ein URI-Format, um diese Speicherressourcen und die für den Zugriff darauf erforderlichen Eigenschaften zu beschreiben (z. b. Sicherheits Anmelde Informationen).


|Anbieter                   |Schema    |URI-Vorlage                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage-Blob         |`https://`|`https://`*Konto* `.blob.core.windows.net/` *Container*[ `/` *blobname*] [ `?` *saskey* \| `;` *accountkey*]|
|Azure Data Lake Storage Gen 2|`abfss://`|`abfss://`*Dateisystem* `@` *Konto* `.dfs.core.windows.net/` *Pathydirectoriyorfile*[ `;` *calleranmelde*Informationen]|
|Azure Data Lake Storage Gen 1|`adl://`  |`adl://`*Account*. azuredatalakestore.net/*pathtodirector yorfile*[ `;` *calleranmelde*Informationen]|

## <a name="azure-storage-blob"></a>Azure Storage-Blob

Dieser Anbieter wird am häufigsten verwendet und wird in allen Szenarien unterstützt.
Der Anbieter muss beim Zugriff auf die Ressource Anmelde Informationen erhalten. Es gibt zwei unterstützte Mechanismen für die Angabe von Anmelde Informationen:

* Stellen Sie einen SAS-Schlüssel (Shared Access) mithilfe der Standard Abfrage () des Azure Storage BLOB bereit `?sig=...` . Verwenden Sie diese Methode, wenn Kusto für einen begrenzten Zeitraum auf die Ressource zugreifen muss.
* Geben Sie den Speicherkonto Schlüssel ( `;ljkAkl...==` ) an. Verwenden Sie diese Methode, wenn Kusto fortlaufend auf die Ressource zugreifen muss.

Beispiele (Beachten Sie, dass dadurch verborgene Zeichen folgen Literale angezeigt werden, damit der Kontoschlüssel oder die SAS nicht verfügbar gemacht wird):

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Storage Gen 2

Dieser Anbieter unterstützt den Zugriff auf Daten in Azure Data Lake Store Gen 2.

Der URI hat folgendes Format:

`abfss://`*Dateisystem* `@` *Storageaccountname* `.dfs.core.windows.net/` *Pfad* `;` *Calleranmelde* Informationen

Hierbei gilt:

* _File System_ ist der Name des ADLS-Dateisystem Objekts (ungefähr Äquivalent zum BLOB-Container)
* _Storageaccountname_ ist der Name des Speicher Kontos.
* _Path_ ist der Pfad zu dem Verzeichnis oder der Datei, auf das der Schrägstrich () zugegriffen wird, `/` wird als Trennzeichen verwendet.
* _Caller-Anmelde_ Informationen gibt die Anmelde Informationen an, die für den Zugriff auf den Dienst verwendet werden.

Beim Zugriff auf Azure Data Lake Store Gen 2 muss der Aufrufer gültige Anmelde Informationen für den Zugriff auf den Dienst angeben. Die folgenden Methoden zum Bereitstellen von Anmelde Informationen werden unterstützt:

* Fügen Sie `;sharedkey=` *accountkey* an den URI an, wobei _accountkey_ der Speicherkonto Schlüssel ist.
* Fügen `;impersonate` Sie an den URI an. Kusto verwendet die Prinzipal Identität des Anforderers und nimmt die Identität für den Zugriff auf die Ressource an. Prinzipal muss über die entsprechenden RBAC-Rollenzuweisungen verfügen, um die Lese-/Schreibvorgänge ausführen zu können, wie [hier](/azure/storage/blobs/data-lake-storage-access-control)dokumentiert. (Die minimale Rolle für Lesevorgänge ist beispielsweise die- `Storage Blob Data Reader` Rolle.)
* Fügen Sie `;token=` *aadtoken* an den URI an, wobei _aadtoken_ ein Base-64-codiertes Aad-Zugriffs Token ist (stellen Sie sicher, dass das Token für die Ressource bestimmt ist `https://storage.azure.com/` ).
* Fügen `;prompt` Sie an den URI an. Kusto fordert Benutzer Anmelde Informationen an, wenn er auf die Ressource zugreifen muss. (Die Aufforderung, dass der Benutzer bei cloudbereitstellungen deaktiviert ist und nur in Testumgebungen aktiviert ist.)
* Stellen Sie einen SAS-Schlüssel (Shared Access) mithilfe der Standard Abfrage von Azure Data Lake Storage Gen 2 ( `?sig=...` ) bereit. Verwenden Sie diese Methode, wenn Kusto für einen begrenzten Zeitraum auf die Ressource zugreifen muss.



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Storage Gen 1

Dieser Anbieter unterstützt den Zugriff auf Dateien und Verzeichnisse in Azure Data Lake Store.
Er muss mit Anmelde Informationen bereitgestellt werden (Kusto verwendet nicht seinen eigenen Aad-Prinzipal, um auf Azure Data Lake zuzugreifen.) Die folgenden Methoden zum Bereitstellen von Anmelde Informationen werden unterstützt:

* Fügen `;impersonate` Sie an den URI an. Kusto verwendet die Prinzipal Identität des Anforderers und nimmt die Identität des Anforderers für den Zugriff auf die Ressource an.
* Fügen Sie `;token=` *aadtoken* an den URI an, wobei *aadtoken* ein Base-64-codiertes Aad-Zugriffs Token ist (stellen Sie sicher, dass das Token für die Ressource bestimmt ist `https://management.azure.com/` ).
* Fügen `;prompt` Sie an den URI an. Kusto fordert Benutzer Anmelde Informationen an, wenn er auf die Ressource zugreifen muss. (Die Aufforderung, dass der Benutzer bei cloudbereitstellungen deaktiviert ist und nur in Testumgebungen aktiviert ist.)