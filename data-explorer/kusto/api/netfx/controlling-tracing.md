---
title: Steuern oder Unterdrücken der Client-Seitenablaufverfolgung von Kusto SDK - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt das Steuern oder Unterdrücken der Client-Seitenablaufverfolgung von Kusto SDK in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 65964d7e990cdb639bd5bfe319d11874ea3de15d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502736"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Steuern oder Unterdrücken der Client-Seitenablaufverfolgung von Kusto SDK

Die Kusto-Clientbibliotheken verwenden eine gemeinsame Plattform für die Ablaufverfolgung. Die Plattform verwendet eine große`System.Diagnostics.TraceSource`Anzahl von Ablaufverfolgungsquellen (`System.Diagnostics.Trace.Listeners`), die während der Konstruktion jeweils mit dem Standardsatz von Ablaufverfolgungslistenern ( ) verbunden sind.

Eine Folge davon ist, dass die Kusto-Clientbibliotheken, wenn einer Anwendung Ablaufverfolgungslistener zugeordnet sind (z. `System.Diagnostics.Trace` B. über ihre `app.config` Datei), Ablaufverfolgungen an diese Listener aussenden.

Dieses Verhalten kann programmgesteuert oder über eine Konfigurationsdatei unterdrückt oder gesteuert werden.

## <a name="suppress-tracing-programmatically"></a>Programmgesteuerte Ablaufverfolgung unterdrücken

Um die Ablaufverfolgung aus den Kusto-Clientbibliotheken programmgesteuert zu unterdrücken, rufen Sie beim Laden der entsprechenden Bibliothek den folgenden Codefrühzeitig frühzeitig auf:

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>Unterdrücken der Ablaufverfolgung mithilfe einer Konfigurationsdatei

Um die Ablaufverfolgung aus den Kusto-Clientbibliotheken über `Kusto.Cloud.Platform.dll.tweaks` eine Konfigurationsdatei zu `Kusto.Data` unterdrücken, ändern Sie die Datei (die in der Bibliothek enthalten ist), so dass die entsprechende "Tweak" nun lautet:

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(Beachten Sie, dass es für die Optimierung kein Minuszeichen `key`im Wert von .) geben muss.

Eine alternative Möglichkeit ist, Folgendes zu tun:

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>So aktivieren Sie die Ablaufverfolgung der Kusto-Clientbibliotheken

Um die Ablaufverfolgung der Kusto-Clientbibliotheken zu aktivieren, aktivieren Sie die .NET-Ablaufverfolgung in der Datei app.config Ihrer Anwendung. Angenommen, die Anwendung `MyApp.exe` verwendet die Kusto.Data-Clientbibliothek. Wenn Sie `MyApp.exe.config` dann die Datei so ändern, dass sie Folgendes enthält, wird Kusto.Data beim nächsten Start der Anwendung aktiviert:

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

Dadurch wird ein Ablaufverfolgungslistener konfiguriert, der in `RollingLogs` CSV-Dateien in einem Unterverzeichnis schreibt, das im Prozessverzeichnis angegeben ist. (Natürlich jede . NET-kompatible Ablaufverfolgungslistenerklasse kann ebenfalls verwendet werden.) 

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>Aktivieren der AAD-Clientbibliotheken (ADAL)-Ablaufverfolgung

Sobald die Ablaufverfolgung für die Kusto-Clientbibliotheken aktiviert ist, wird auch die Ablaufverfolgung von den AAD-Clientbibliotheken (die Kusto-Clientbibliotheken konfigurieren automatisch die ADAL-Ablaufverfolgung)

