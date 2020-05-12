---
title: 'Verwenden der .NET-Client Bibliotheken von PowerShell: Azure Daten-Explorer'
description: In diesem Artikel wird die Verwendung der .NET-Client Bibliotheken von PowerShell in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 5c521d7e63d58dd32425e759b0cf09a22a050b40
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226039"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>Verwenden der .NET-Client Bibliotheken von PowerShell

PowerShell-Skripts können Azure Daten-Explorer .NET-Client Bibliotheken über die integrierte Integration von PowerShell in beliebige (Non-PowerShell) .NET-Bibliotheken verwenden.

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>Erhalten der .NET-Client Bibliotheken für die Skripterstellung mit PowerShell

So beginnen Sie mit der Arbeit mit den Azure Daten-Explorer .NET-Client Bibliotheken mithilfe von PowerShell.

1. Laden Sie das [ `Microsoft.Azure.Kusto.Tools` nuget-Paket](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)herunter.
1. Extrahieren Sie den Inhalt des Verzeichnisses "Tools" im Paket (verwenden Sie ein Archivierungs Tool wie `7-zip` ).
1. `[System.Reflection.Assembly]::LoadFrom("path")`Wird von PowerShell aufgerufen, um die erforderliche Bibliothek zu laden. 
    - Der- `path` Parameter für den-Befehl sollte den Speicherort der extrahierten Dateien angeben.
1. Nachdem alle abhängigen .NET-Assemblys geladen wurden:
   1. Erstellen Sie eine Kusto-Verbindungs Zeichenfolge.
   1. Instanziieren Sie einen *Abfrage Anbieter* oder einen *Administrator Anbieter*.
   1. Führen Sie die Abfragen oder Befehle aus, wie in den folgenden [Beispielen](powershell.md#examples) gezeigt.

Weitere Informationen finden Sie unter [Azure Daten-Explorer-Client Bibliotheken](../netfx/about-kusto-data.md).

## <a name="examples"></a>Beispiele

### <a name="initialization"></a>Initialisierung

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example of the location from where you extract the Microsoft.Azure.Kusto.Tools package.
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

#   Option A: using Azure AD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using Azure AD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>Beispiel: Ausführen eines Administrator Befehls

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

Und die Ausgabe lautet:
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

Und die Ausgabe lautet:

|StartTime           |EndTime             |Episodeid |EventId |Bundesland/Kanton          |EventType         |Verletzen von Verletzungen |Verletzung indirekt |Deathsdirect |Deathsindirect
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |Georgien        |Sturm |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |Mississippi    |Sturm |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |Atlantik, Süden |Wasser-Spout       |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |Flori        |Hüterin           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |Flori        |Starker Regen        |             0 |               0 |           0 |             0
