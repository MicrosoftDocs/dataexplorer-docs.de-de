---
title: Einbetten der Webbenutzer Oberfläche in ein **iframe** -Azure-Daten-Explorer
description: In diesem Artikel wird die Einbettung der Webbenutzer Oberfläche in einem **iframe** in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: c6b9d1d5cb971b5c9c51cf9f9918562f12323a03
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799661"
---
# <a name="embed-web-ui-in-an-iframe"></a>Einbetten der Webbenutzer Oberfläche in einen iframe

Die Azure Daten-Explorer-Webbenutzer Oberfläche kann in einen iframe eingebettet und in Websites von Drittanbietern gehostet werden.

![alt text](../images/web-ux.jpg "Azure Daten-Explorer-Webbenutzer Oberfläche")

Durch das Einbetten der Azure Daten-Explorer Web-UX auf Ihrer Website können Ihre Benutzer Folgendes tun:

- Bearbeiten von Abfragen (enthält alle sprach Features wie z. b. farbige Farbgebung und IntelliSense)
- Visuelles Durchsuchen von Tabellen Schemas
- Authentifizieren bei Azure AD
- Ausführen von Abfragen
- Anzeigen der Ergebnisse der Abfrage Ausführung
- Erstellen mehrerer Registerkarten
- Lokales Speichern von Abfragen
- Freigeben von Abfragen per e-Mail

Alle Funktionen werden auf Barrierefreiheit getestet und unterstützen dunkle und helle Designs auf dem Bildschirm.

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>Verwenden Sie Monaco-Kusto, oder Betten Sie die Webbenutzer Oberfläche ein?

Monaco-Kusto verbessert die Bearbeitungsfunktionen mit Vervollständigung, Farbgebung, Umgestaltung, umbenennen und gehe zu Definition. Damit können Sie eine Lösung für die Authentifizierung, die Abfrage Ausführung, die Ergebnisanzeige und die Schema Untersuchung erstellen. Monaco-Kusto bietet Ihnen außerdem volle Flexibilität, um die Benutzer Leistung zu gestalten, die Ihren Anforderungen entspricht.

Durch das Einbetten der Azure-Daten-Explorer-Webbenutzer Oberfläche werden umfassende Funktionen mit geringem Aufwand geboten. Das Einbetten führt jedoch auch zu einer begrenzten Flexibilität bei der Benutzer Leistung. Es gibt einen festgelegten Satz von Abfrage Parametern, die Ihnen die eingeschränkte Kontrolle über das Aussehen und Verhalten des Systems ermöglicht.

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>Einbetten der Webbenutzer Oberfläche in einen iframe

### <a name="host-the-website-in-an-iframe"></a>Hosten der Website in einem IFRAME

Fügen Sie Ihrer Website den folgenden Code hinzu:

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

Der `ibizaPortal` Abfrage Parameter weist die Azure Daten-Explorer-Webbenutzer Oberfläche an, *keine* Umleitung zum Abruf eines Authentifizierungs Tokens durchzusetzen. Diese Aktion ist erforderlich, da die Hostingwebsite für die Bereitstellung eines Authentifizierungs Tokens für den eingebetteten iframe zuständig ist.

Ersetzen `<cluster>` Sie dies durch den Hostnamen des Clusters, den Sie in den Verbindungsbereich laden möchten (z `help.kusto.windows.net`. b.). Der iframe-Embedded-Modus bietet standardmäßig keine Möglichkeit, Cluster über die Benutzeroberfläche hinzuzufügen, da die Hostingwebsite den erforderlichen Cluster kennt.

### <a name="handle-authentication"></a>Behandeln der Authentifizierung

1. Wenn der Wert "iframe Mode" (`ibizaPortal=true`) festgelegt ist, versucht die Azure Daten-Explorer-Webbenutzer Oberfläche nicht, zur Authentifizierung umzuleiten. Die Webbenutzer Oberfläche verwendet den von den Browsern verwendeten nachrichtenbereitstellungsmechanismus zum Anfordern und Empfangen eines Tokens. Beim Laden der Seite wird die folgende Meldung an das übergeordnete Fenster gesendet:

   ```javascript
   window.parent.postMessage(
     {
       signature: "queryExplorer",
       type: "getToken"
     },
     "*"
   );
   window.addEventListener(
     "message",
     event => this.handleIncomingMessage(event),
     false
   );
   ```

1. Anschließend wird eine Nachricht mit der folgenden Struktur überwacht:

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. Das bereitgestellte Token muss ein [JWT-Token](https://tools.ietf.org/html/rfc7519) sein, das vom [[Aad-Authentifizierungs Endpunkt]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)abgerufen wird.

> [!IMPORTANT]
> Das Hostingfenster muss das Token vor dem Ablauf aktualisieren und denselben Mechanismus verwenden, um das aktualisierte Token für die Anwendung bereitzustellen. Andernfalls tritt bei Dienst aufrufen ein Fehler auf, wenn das Token abläuft.

### <a name="feature-flags"></a>Featureflags

Die Hostinganwendung möchte möglicherweise bestimmte Aspekte der Benutzerumgebung steuern, z. b. den Verbindungsbereich ausblenden oder das Herstellen einer Verbindung mit anderen Clustern deaktivieren.
In diesem Szenario unterstützt der Webexplorer Merkmals Flags.

Ein Merkmals Flag kann in der URL als Abfrage Parameter verwendet werden. Wenn die Host Anwendung z. b. das Hinzufügen anderer Cluster deaktivieren möchte, sollten Siehttps://dataexplorer.azure.com/?ShowConnectionButtons=false

| Einstellung                 | BESCHREIBUNG                    | Standardwert |
| ----------------------- | ------------------------------ | ------------- |
| Showsharemenu           | Menü Element "freigeben" anzeigen       | true          |
| Showconnectionbuttons   | Schaltfläche " **Verbindung hinzufügen** " anzeigen, die einen neuen Cluster hinzufügt                                                                                                              | true          |
| Showopennewwindowbutton | Zeigt die Schaltfläche **in Web öffnen an, mit** der ein neues Browserfenster geöffnet wird. Das Fenster zeigt auf https://dataexplorer.azure.com mit dem richtigen Cluster und der richtigen Datenbank im Gültigkeitsbereich.                                                                                                                        | false         |
| Showfilemenu            | Menü "Datei" anzeigen (**herunterladen**, **Tab**, **Inhalt**usw.)                                                                                                      | true          |
| Showtos                 | Anzeigen des **Links zu den Nutzungsbedingungen für Azure Daten-Explorer über** das Dialogfeld "Einstellungen"                                                                                  | true          |
| Showpersona             | Zeigen Sie den Benutzernamen im Menü "Einstellungen" in der oberen rechten Ecke an.                                                                                                      | true          |
| Iframeauth              | Wenn true, erwartet der Webexplorer, dass der iframe die Authentifizierung behandelt und ein Token über eine Nachricht bereitstellt. Dieser Prozess ist für iframe-Szenarien immer "true".      | false         |
| Persistaftereachrun     | Normalerweise wird der Webexplorer im Entlade Ereignis beibehalten (Beachten Sie, dass beim Hosting in iframes nicht immer ausgelöst wird). Dieses Flag löst dann das Beibehalten des **lokalen Zustands** nach der Ausführung jeder Abfrage aus. Jeglicher auftretende Datenverlust wirkt sich nur auf Text aus, der nie ausgeführt wurde, und schränkt seine Auswirkung ein. | false         |
| Showsmooththingestion     | Bei "true" wird die einklickerfassungs-Erfassung angezeigt, wenn Sie mit der rechten Maustaste auf eine Datenbank klicken                                                                                        | true          |
| Aktuerfrischendes Verbindung       | True gibt an, dass das Schema beim Laden der Seite immer aktualisiert und nie vom lokalen Speicher abhängig ist.                                                                          | false         |
| Showpageheader          | Wenn der Wert true ist, wird der Seitenkopf (einschließlich der Titel und Einstellungen der Azure-Daten-Explorer) angezeigt.                                                                              | true          |
| Hideconnectionpane      | True gibt an, dass der linke Verbindungsbereich nicht angezeigt wird.                                                                                                                      | false         |
| Skipmonacofocusoninit   | Korrigiert des Fokus Problems beim Hosten auf IFRAME                                                                                                                            | false         |

### <a name="feature-flag-presets"></a>Funktions Kennzeichen-Voreinstellungen

Voreinstellungen legen eine Reihe von featureflags gleichzeitig fest.
Zurzeit gibt es nur eine einzige Voreinstellung.

`IbizaPortal`: Ändert die folgenden Flags aus den Standardwerten.

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> Wenn Sie eine Voreinstellung verwenden, können Sie darüber hinaus keine weiteren featureflags hinzufügen. Wenn Sie diese Flexibilität benötigen, sollten Sie einzelne Merkmals Flags verwenden.
