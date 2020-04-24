---
title: Kusto-Clientbibliothek - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Kusto-Clientbibliothek in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 8a3ab9bb3727efdc80e1887ab802f2ad4e3c7cce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502600"
---
# <a name="kusto-client-library"></a>Kusto-Clientbibliothek
    
Das Kusto Client SDK (Kusto.Data) macht eine programmgesteuerte API verfügbar, die ADO.NET ähnelt. Sie erstellen entweder einen`ICslQueryProvider`Abfrageclient ( )`ICslAdminProvider`oder einen Steuerbefehlsanbieter ( ) aus einem Verbindungszeichenfolgenobjekt, das auf den Kusto-Moduldienst, die Datenbank, die Authentifizierungsmethode usw. verweist. Sie können dann Datenabfragen oder Steuerbefehle ausgeben, indem Sie die entsprechende Kusto-Abfragezeichenfolge `IDataReader` angeben und eine oder mehrere Datentabellen über das zurückgegebene Objekt zurücksenden.

Um konkret einen ADO.NET-ähnlichen Client zu erstellen, der Abfragen für `Kusto.Data.Net.Client.KustoClientFactory` Kusto ermöglicht, verwenden Sie statische Methoden für die Klasse. Diese nehmen die Verbindungszeichenfolge und erstellen ein threadsicheres, einweggeschütztes Clientobjekt. (Es wird dringend empfohlen, dass Clientcode nicht "zu viele" Instanzen dieses Objekts erstellt. Stattdessen sollte Clientcode ein Objekt pro Verbindungszeichenfolge erstellen und so lange wie nötig daran festhalten.) Dadurch kann das Clientobjekt Ressourcen effizient zwischenspeichern.

Im Allgemeinen sind alle Methoden auf den Clients threadsicher, mit zwei Ausnahmen: `Dispose`, und Settereigenschaften. Um konsistente Ergebnisse zu erzielen, rufen Sie keine der beiden Methoden gleichzeitig auf.

Es folgen einige Beispiele. Weitere Beispiele finden Sie [hier](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client).

**Beispiel: Zählen von Zeilen**
 
Der folgende Code veranschaulicht das Zählen `StormEvents` der Zeilen `Samples`einer Tabelle mit dem Namen in einer Datenbank mit dem Namen:

```csharp
var client = Kusto.Data.Net.Client.KustoClientFactory.CreateCslQueryProvider("https://help.kusto.windows.net/Samples;Fed=true");
var reader = client.ExecuteQuery("StormEvents | count");
// Read the first row from reader -- it's 0'th column is the count of records in MyTable
// Don't forget to dispose of reader when done.
```

**Beispiel: Abrufen von Diagnoseinformationen aus dem Kusto-Cluster**

```csharp
var kcsb = new KustoConnectionStringBuilder(cluster URI here). WithAadUserPromptAuthentication();
using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
{
    var diagnosticsCommand = CslCommandGenerator.GenerateShowDiagnosticsCommand();
    var objectReader = new ObjectReader<DiagnosticsShowCommandResult>(client.ExecuteControlCommand(diagnosticsCommand));
    DiagnosticsShowCommandResult diagResult = objectReader.ToList().FirstOrDefault();
    // DO something with the diagResult    
}
```



## <a name="the-kustoclientfactory-client-factory"></a>Die KustoClientFactory Client Factory

Die statische `Kusto.Data.Net.Client.KustoClientFactory` Klasse stellt den Haupteinstiegspunkt für Autoren von Clientcode bereit, der Kusto verwendet. Es bietet die folgenden wichtigen statischen Methoden:

|Methode                                      |Rückgabe                                |Syntaxelemente                                                      |
|--------------------------------------------|---------------------------------------|--------------------------------------------------------------|
|`CreateCslQueryProvider`                    |`ICslQueryProvider`                    |Senden von Abfragen an einen Kusto-Modulcluster.                    |
|`CreateCslAdminProvider`                    |`ICslAdminProvider`                    |Senden von Steuerbefehlen an einen Kusto-Cluster (jeder Art).    |
|`CreateRedirectProvider`                    |`IRedirectProvider`                    |Erstellen einer UMleitender HTTP-Antwortnachricht für eine Kusto-Anforderung.|

