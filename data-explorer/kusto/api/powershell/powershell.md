---
title: Verwenden der .NET-Clientbibliotheken von PowerShell - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Verwendung der .NET-Clientbibliotheken von PowerShell in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 635a23021a1a8c30347bfa27ecd65886b46a6fea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503212"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>Verwenden der .NET-Clientbibliotheken von PowerShell

Die Azure Data Explorer .NET-Clientbibliotheken können von PowerShell-Skripts über die integrierte Integration von PowerShell mit beliebigen .NET-Bibliotheken (nicht PowerShell) verwendet werden.

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>Abrufen der .NET-Clientbibliotheken für das Skripting mit PowerShell

So starten Sie die Arbeit mit den Azure Data Explorer .NET-Clientbibliotheken mit PowerShell:

1. Laden Sie das [ `Microsoft.Azure.Kusto.Tools` NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)herunter.
2. Extrahieren Sie den Inhalt des Verzeichnisses "tools" im Paket (z. B. mit 7-zip).
3. Rufen `[System.Reflection.Assembly]::LoadFrom("path")` Sie von powershell an, um die erforderliche Bibliothek zu laden. 
    - Der `"path"` Parameter für den Befehl sollte den Speicherort der extrahierten Dateien angeben.
4. Nachdem alle abhängigen .NET-Assemblys geladen wurden, erstellen Sie eine Kusto-Verbindungszeichenfolge, instanziieren Sie einen *Abfrageanbieter* oder einen *Administratoranbieter*, und führen Sie die Abfragen oder Befehle aus (wie in den [folgenden Beispielen](powershell.md#examples) gezeigt).

Ausführliche Informationen finden Sie in den [Azure Data Explorer-Clientbibliotheken](../netfx/about-kusto-data.md).

## <a name="examples"></a>Beispiele

### <a name="initialization"></a>Initialisierung

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example to the location where you extract the Microsoft.Azure.Kusto.Tools package.
#  Please make sure you load the types from a local directory and not from a remote share.
$packagesRoot = "C:\Microsoft.Azure.Kusto.Tools\Tools"

#  Part 2 of 3
#  ------------
#  Loading the Kusto.Client library and its dependencies
dir $packagesRoot\* | Unblock-File
[System.Reflection.Assembly]::LoadFrom("$packagesRoot\Kusto.Data.dll")

#  Part 3 of 3
#  ------------
#  Defining the connection to your cluster / database
$clusterUrl = "https://help.kusto.windows.net;Fed=True"
$databaseName = "Samples"

#   Option A: using AAD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using AAD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>Beispiel: Ausführen eines Admin-Befehls

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

Und die Ausgabe ist:
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>Beispiel: Ausführen einer Abfrage

```powershell
$queryProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslQueryProvider($kcsb)
$query = "StormEvents | limit 5"
Write-Host "Executing query: '$query' with connection string: '$($kcsb.ToString())'"
#   Optional: set a client request ID and set a client request property (e.g. Server Timeout)
$crp = New-Object Kusto.Data.Common.ClientRequestProperties
$crp.ClientRequestId = "MyPowershellScript.ExecuteQuery." + [Guid]::NewGuid().ToString()
$crp.SetOption([Kusto.Data.Common.ClientRequestProperties]::OptionServerTimeout, [TimeSpan]::FromSeconds(30))

#   Execute the query
$reader = $queryProvider.ExecuteQuery($query, $crp)

# Do something with the result datatable, for example: print it formatted as a table, sorted by the 
# "StartTime" column, in descending order
$dataTable = [Kusto.Cloud.Platform.Data.ExtendedDataReader]::ToDataSet($reader).Tables[0]
$dataView = New-Object System.Data.DataView($dataTable)
$dataView | Sort StartTime -Descending | Format-Table -AutoSize
```

Und die Ausgabe ist:

|StartTime           |EndTime             |EpisodeId |EventId |State          |EventType         |InjuriesDirect |VerletzungenIndirekt |DeathsDirect |DeathsIndirect
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |Georgien        |Sturm |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |Mississippi    |Sturm |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |ATLANTIC SOUTH |Wasserauslauf        |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |Florida        |Tornado           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |Florida        |Heavy Rain        |             0 |               0 |           0 |             0