---
title: 'Kusto-Steuerung oder SDK-Client seitige Ablauf Verfolgung unterdrücken: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie die Client seitige Ablauf Verfolgung von Kusto SDK in Azure Daten-Explorer steuern und unterdrücken
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: 2224fe28c7f0088ac1a16cdee4d452e354ff0800
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324753"
---
# <a name="controlling-and-suppressing-kusto-sdk-client-side-tracing"></a>Steuern und unterdrücken von Client seitiger Kusto SDK-Ablauf Verfolgung

Die Kusto-Client Bibliotheken verwenden eine gängige Plattform für die Ablauf Verfolgung. Die Plattform verwendet eine große Anzahl von Ablauf Verfolgungs Quellen ( `System.Diagnostics.TraceSource` ), und jede ist während ihrer Erstellung mit dem Standardsatz von Ablaufverfolgungslistenern ( `System.Diagnostics.Trace.Listeners` ) verbunden.

Wenn einer Anwendung Ablaufverfolgungslistener mit der Standard `System.Diagnostics.Trace` Instanz (z. b. über die zugehörige `app.config` Datei) zugeordnet sind, geben die Kusto-Client Bibliotheken Ablauf Verfolgungen an diese Listener aus.

Die Ablauf Verfolgung kann Programm gesteuert oder über eine Konfigurationsdatei unterdrückt oder gesteuert werden.

## <a name="suppress-tracing-programmatically"></a>Ablauf Verfolgung Programm gesteuert unterdrücken

Um die Ablauf Verfolgung von den Kusto-Client Bibliotheken Programm gesteuert zu unterdrücken, rufen Sie diesen Code Abschnitt auf, wenn Sie die relevante Bibliothek laden:

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="use-a-config-file-to-suppress-tracing"></a>Verwenden einer Konfigurationsdatei zum Unterdrücken der Ablauf Verfolgung 

Um die Ablauf Verfolgung aus den Kusto-Client Bibliotheken durch eine Konfigurationsdatei zu unterdrücken, ändern Sie die Datei `Kusto.Cloud.Platform.dll.tweaks` (die in der Bibliothek enthalten ist `Kusto.Data` ).

```xml
    //Overrides the default trace verbosity level
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

> [!NOTE]
> Damit die Optimierung wirksam wird, darf kein Minuszeichen im Wert von vorhanden sein. `key`

Eine Alternative ist:

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="enable-the-kusto-client-libraries-tracing"></a>Aktivieren der Ablauf Verfolgung für die Kusto-Client Bibliotheken

Aktivieren Sie die .net-Ablauf Verfolgung in der *app.config Datei* Ihrer Anwendung, um die Ablauf Verfolgung aus den Kusto-Client Bibliotheken zu aktivieren. Nehmen Sie z. b. an, dass die Anwendung `MyApp.exe` die Kusto. Data-Client Bibliothek verwendet. Wenn Sie die Datei *MyApp.exe.config* ändern, um Folgendes einzuschließen, wird die Ablauf `Kusto.Data` Verfolgung beim nächsten Start der Anwendung aktiviert.

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

Im Code wird ein Ablaufverfolgungslistener konfiguriert, der in CSV-Dateien in einem Unterverzeichnis namens *rollinglogs* schreibt. Das Unterverzeichnis befindet sich im Verzeichnis "Prozess".

> [!NOTE]
> Irgendeiner. Die NET-kompatible ablaufverfolgungslistenerklasse kann ebenfalls verwendet werden

## <a name="enable-the-azure-ad-client-libraries-adal-tracing"></a>Aktivieren der Ablauf Verfolgung für die Azure AD Client Bibliotheken (Adal)

Nachdem die Ablauf Verfolgung für die Kusto-Client Bibliotheken aktiviert ist, ist dies die Ablauf Verfolgung durch die Azure AD Client Bibliotheken. Die Kusto-Client Bibliotheken konfigurieren die Adal-Ablauf Verfolgung automatisch.
