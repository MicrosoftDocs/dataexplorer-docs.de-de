---
title: Beheben allgemeiner Probleme in Kusto. Explorer
description: Erfahren Sie mehr über häufige Probleme beim Installieren und Ausführen von Kusto. Explorer und deren Lösungen.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: dd312c4a36bb095dbe4b5e475e758c141bbab6e0
ms.sourcegitcommit: aacea5c4c397479e8254c1fe6ed0b2f333307b14
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86470093"
---
# <a name="troubleshooting"></a>Problembehandlung

Dieses Dokument stellt häufige Probleme beim Ausführen und Verwenden von Kusto. Explorer dar und bietet Lösungen. Außerdem wird in diesem Dokument beschrieben [, wie Sie Kusto. Explorer zurücksetzen](#reset-kustoexplorer).

## <a name="kustoexplorer-fails-to-start"></a>"Kusto. Explorer" kann nicht gestartet werden.

### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>"Kusto. Explorer" zeigt ein Fehler Dialogfeld während oder nach dem Start an.

**Symptom**

Beim Start zeigt Kusto. Explorer einen `InvalidOperationException` Fehler an.

**Mögliche Lösung**

Dieser Fehler kann darauf hindeuten, dass das Betriebssystem beschädigt wurde oder einige der grundlegenden Module fehlt.
Um fehlende oder beschädigte Systemdateien zu überprüfen, führen Sie die folgenden Schritte aus:   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

## <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>"Kusto. Explorer" wird immer heruntergeladen, auch wenn keine Updates vorhanden sind.

**Symptom**

Jedes Mal, wenn Sie "Kusto. Explorer" öffnen, werden Sie aufgefordert, eine neue Version zu installieren. "Kusto. Explorer" lädt das gesamte Paket herunter, ohne die bereits installierte Version zu aktualisieren.

**Mögliche Lösung**

Dieses Symptom könnte auf eine Beschädigung in Ihrem lokalen ClickOnce-Speicher zurückzuführen sein. Sie können den lokalen ClickOnce-Speicher löschen, indem Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten ausführen.

> [!Important]
> 1. Wenn andere Instanzen von ClickOnce-Anwendungen oder von vorhanden sind `dfsvc.exe` , beenden Sie diese, bevor Sie diesen Befehl ausführen.
> 1. Alle ClickOnce-apps werden automatisch neu installiert, wenn Sie Sie das nächste Mal ausführen, sofern Sie auf den in der APP-Verknüpfung gespeicherten ursprünglichen Installationsort zugreifen können. App-Verknüpfungen werden nicht gelöscht.

```kusto
rd /q /s %userprofile%\appdata\local\apps\2.0
```

Versuchen Sie, Kusto. Explorer erneut von einem der [Installations Spiegelungen](kusto-explorer.md#installing-kustoexplorer)zu installieren.

### <a name="clickonce-error-cannot-start-application"></a>ClickOnce-Fehler: die Anwendung kann nicht gestartet werden.

**Symptome**  

Das Programm kann nicht gestartet werden und zeigt einen der folgenden Fehler an: 
* `External component has thrown an exception`
* `Value does not fall within the expected range`
* `The application binding data format is invalid.` 
* `Exception from HRESULT: 0x800736B2`
* `The referenced assembly is not installed on your system. (Exception from HRESULT: 0x800736B3)`

Sie können die Fehlerdetails durch Klicken `Details` auf das folgende Fehler Dialogfeld Durchsuchen:

![ClickOnce-Fehler](./Images/kusto-explorer-troubleshooting/clickonce-err-1.png)

```kusto
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**Vorgeschlagene Lösungsschritte**

1. Deinstallieren Sie Kusto. Explorer mithilfe von `Programs and Features` ( `appwiz.cpl` ).

1. Führen Sie aus `CleanOnlineAppCache` , und versuchen Sie dann erneut, Kusto. Explorer zu installieren. 
    An einer Eingabeaufforderung mit erhöhten Rechten: 
    
    ```kusto
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    Installieren Sie Kusto. Explorer erneut von einem der [Installations Spiegelungen](kusto-explorer.md#installing-kustoexplorer).

1. Wenn die Anwendung immer noch nicht gestartet wird, löschen Sie den lokalen ClickOnce-Speicher. Alle ClickOnce-apps werden automatisch neu installiert, wenn Sie Sie das nächste Mal ausführen, sofern Sie auf den in der APP-Verknüpfung gespeicherten ursprünglichen Installationsort zugreifen können. App-Verknüpfungen werden nicht gelöscht.

    An einer Eingabeaufforderung mit erhöhten Rechten:

    ```kusto
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Installieren Sie Kusto. Explorer erneut von einem der [Installations Spiegelungen](kusto-explorer.md#installing-kustoexplorer) .

1. Wenn die Anwendung immer noch nicht gestartet wird:
    1. Entfernen Sie temporäre Bereitstellungs Dateien.
    1. Benennen Sie den lokalen Ordner AppData von Kusto. Explorer um.

        An einer Eingabeaufforderung mit erhöhten Rechten:

        ```kusto
        rd /s/q %userprofile%\AppData\Local\Temp\Deployment
        ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
        ```

    1. Installieren Sie Kusto. Explorer erneut von einem der [Installations Spiegelungen](kusto-explorer.md#installing-kustoexplorer) .

    1. Zum Wiederherstellen der Verbindungen aus "Kusto. Explorer. bak" über eine Eingabeaufforderung mit erhöhten Rechten:

        ```kusto
        copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
        ```

1. Wenn die Anwendung immer noch nicht gestartet wird:
    1. Aktivieren Sie die ausführliche ClickOnce-Protokollierung, indem Sie den LogVerbosityLevel-Zeichen folgen Wert 1 unter:

        ```kusto
        HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment
        ```
    
    1. Wiederholen Sie den Vorgang.
    1. Senden Sie die ausführliche Ausgabe an KEBugReport@microsoft.com . 

### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce-Fehler: Ihr Administrator hat diese Anwendung blockiert, da Sie potenziell ein Sicherheitsrisiko für Ihren Computer darstellt.

**Symptom**  
Die Anwendung kann mit einem der folgenden Fehler nicht installiert werden:
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**Lösung**

Dieses Symptom könnte daran liegen, dass eine andere Anwendung das Standardverhalten der ClickOnce-Vertrauens Aufforderung überschreibt. 
1. Anzeigen der Standard Konfigurationseinstellungen.
1. Vergleichen Sie Ihre Konfigurationseinstellungen mit den tatsächlichen Einstellungen auf Ihrem Computer.
1. Setzen Sie die Konfigurationseinstellungen nach Bedarf zurück, wie [in diesem Artikel](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)erläutert.

### <a name="cleanup-application-data"></a>Bereinigen von Anwendungsdaten

In einigen Fällen, in denen die vorherigen Schritte zur Problembehandlung beim Einstieg von Kusto. Explorer nicht helfen konnten, kann das Bereinigen von lokal gespeicherten Daten helfen.

Die von der Anwendung "Kusto. Explorer" gespeicherten Daten finden Sie hier: `C:\Users\[your username]\AppData\Local\Kusto.Explorer` .

> [!NOTE]
> Das Bereinigen der Daten führt zu einem Verlust der geöffneten Registerkarten (Wiederherstellungs Ordner), gespeicherter Verbindungen (Verbindungs Ordner) und Anwendungseinstellungen (Ordner "UserSettings").

## <a name="reset-kustoexplorer"></a>Zurücksetzen von Kusto. Explorer

Wenn erforderlich, können Sie Kusto. Explorer vollständig zurücksetzen. Im folgenden Verfahren wird beschrieben, wie Sie "Kusto. Explorer" progressiv zurücksetzen, bis Sie vollständig vom Computer entfernt werden und von Grund auf neu installiert werden müssen.

1. Öffnen Sie in Windows das **Programm ändern oder entfernen Sie ein Programm** (auch als **Programme und Funktionen**bezeichnet).
1. Wählen Sie alle Elemente aus, die mit beginnen `Kusto.Explorer` .
1. Wählen Sie **Deinstallieren** aus.

   Wenn diese Vorgehensweise die Anwendung nicht deinstallieren kann (ein bekanntes Problem bei ClickOnce-Anwendungen), finden Sie in [diesem Artikel Anweisungen](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer).

1. Löschen Sie den Ordner `%LOCALAPPDATA%\Kusto.Explorer` , der alle Verbindungen, den Verlauf usw. entfernt.

1. Löschen Sie den Ordner `%APPDATA%\Kusto` , der den Kusto. Explorer-Tokencache entfernt. Sie müssen sich erneut bei allen Clustern authentifizieren.

Es ist auch möglich, zu einer bestimmten Version von Kusto. Explorer zurückzukehren:

1. Führen Sie `appwiz.cpl` aus.
1. Wählen Sie **Kusto. Explorer** und anschließend **Deinstallieren/ändern**aus.
1. Wählen Sie **Wiederherstellen des vorherigen Zustands der Anwendung**aus.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Benutzeroberfläche von "Kusto. Explorer](kusto-explorer.md#overview-of-the-user-interface) "
* Erfahren Sie mehr über [die Ausführung von Kusto. Explorer über die Befehlszeile.](kusto-explorer-using.md#kustoexplorer-command-line-arguments)
* Weitere Informationen zu [Kusto Query Language (kql)](https://docs.microsoft.com/azure/kusto/query/)
