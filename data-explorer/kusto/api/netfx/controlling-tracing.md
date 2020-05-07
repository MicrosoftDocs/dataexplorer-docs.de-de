---
title: 'Steuern oder unterdrücken der Client seitigen Ablauf Verfolgung von Kusto SDK: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie die Client seitige Ablauf Verfolgung für das Kusto-SDK in Azure Daten-Explorer steuern
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: cbda69063e3b1a20549dbadb4641fc9fd3f51f57
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862053"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Steuern oder unterdrücken der clientseitigen Ablauf Verfolgung von Kusto SDK

Die Kusto-Client Bibliotheken verwenden eine gängige Plattform für die Ablauf Verfolgung. Die Plattform verwendet eine große Anzahl von Ablauf Verfolgungs Quellen`System.Diagnostics.TraceSource`(), die jeweils mit dem Standardsatz von Ablaufverfolgungslistenern (`System.Diagnostics.Trace.Listeners`) verbunden sind.

Dies liegt daran, dass bei einer Anwendung Ablaufverfolgungslistener mit `System.Diagnostics.Trace` der Standard Instanz (z. b `app.config` . über die zugehörige Datei) erstellt werden, dann werden die Kusto-Client Bibliotheken Ablauf Verfolgungen an diese Listener ausgegeben.

Dieses Verhalten kann Programm gesteuert oder über eine Konfigurationsdatei unterdrückt oder gesteuert werden.

## <a name="suppress-tracing-programmatically"></a>Ablauf Verfolgung Programm gesteuert unterdrücken

Um die Ablauf Verfolgung von den Kusto-Client Bibliotheken Programm gesteuert zu unterdrücken, rufen Sie den folgenden Code Abschnitt beim Laden der relevanten Bibliothek in der frühen Zeit auf:

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>Unterdrücken der Ablauf Verfolgung mithilfe einer Konfigurationsdatei

Um die Ablauf Verfolgung aus den Kusto-Client Bibliotheken durch eine Konfigurationsdatei zu unter `Kusto.Cloud.Platform.dll.tweaks` drücken, ändern Sie die Datei `Kusto.Data` (die in der Bibliothek enthalten ist), damit die entsprechende "Tweak" nun Folgendes liest:

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(Beachten Sie, dass es für den Fall, dass die Optimierung wirksam wird, dass kein Minuszeichen im `key`Wert von vorhanden sein muss.)

Eine alternative Möglichkeit ist Folgendes:

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Aktivieren der Ablauf Verfolgung für die Kusto-Client Bibliotheken

Aktivieren Sie die .net-Ablauf Verfolgung in der app. config-Datei Ihrer Anwendung, um die Ablauf Verfolgung aus den Kusto-Client Bibliotheken zu aktivieren. Angenommen, die Anwendung `MyApp.exe` verwendet die Client Bibliothek "Kusto. Data". Wenn Sie die Datei `MyApp.exe.config` dann in Folgendes einschließen, wird die Kusto. Data-Ablauf Verfolgung beim nächsten Start der Anwendung aktiviert:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.diagnostics>
    <trace indentsize="4">
      <listeners>
        <add type="Kusto.Cloud.Platform.Utils.RollingCsvTraceListener2, Kusto.Cloud.Platform" name="RollingCsvTraceListener" initializeData="RollingLogs" />
        <remove name="Default" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

Dadurch wird ein Ablaufverfolgungslistener konfiguriert, der in die CSV- `RollingLogs` Dateien in einem Unterverzeichnis mit dem Namen im Verzeichnis "Process" schreibt. (Natürlich alle. Möglicherweise wird auch die NET-kompatible ablaufverfolgungslistenerklasse verwendet.)

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>Aktivieren der Aad-Client Bibliotheken (Adal)-Ablauf Verfolgung

Nachdem die Ablauf Verfolgung für die Kusto-Client Bibliotheken aktiviert ist, wird die Ablauf Verfolgung von den Aad-Client Bibliotheken ausgegeben (die Adal-Ablauf Verfolgung wird von den Kusto-Client Bibliotheken automatisch konfiguriert).
