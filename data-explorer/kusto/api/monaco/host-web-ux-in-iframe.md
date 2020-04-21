---
title: Einbetten der Webbenutzeroberfläche in einen iframe - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie die Webbenutzeroberfläche in einem iframe in Azure Data Explorer eingebettet wird.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9c5469a577c3e09cd433ec250f9215e5f450d5f5
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524462"
---
# <a name="embed-web-ui-in-an-iframe"></a>Einbetten der Webbenutzeroberfläche in einen iframe

Die Azure Data Explorer-Webbenutzeroberfläche kann in einen iframe eingebettet und in Websites von Drittanbietern gehostet werden.

![alt text](../images/web-ux.jpg "Azure Data Explorer-Webbenutzeroberfläche")

Das Einbetten der Azure Data Explorer Web UX in Ihre Website ermöglicht ihren Benutzern Folgendes:

- Bearbeiten von Abfragen (enthält alle Sprachfeatures wie Farbgebung und Intellisense)
- Tabellenschemas visuell untersuchen
- Authentifizieren bei AAD
- Ausführen von Abfragen
- Anzeigen von Abfrageausführungsergebnissen
- Erstellen mehrerer Registerkarten
- Abfragen lokal speichern
- Freigeben von Abfragen per E-Mail

Alle Funktionen sind auf Barrierefreiheit getestet und unterstützen dunkles und helles Thema.

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>Verwenden Sie Monaco-Kusto oder betten Sie die Web-Benutzeroberfläche ein?

Monaco-Kusto bietet Ihnen eine Bearbeitungserfahrung wie Vervollständigung, Farbgebung, Umgestaltung, Umbenennung und Go-to-Definition, es erfordert, dass Sie eine Lösung für die Authentifizierung, Abfrageausführung, Ergebnisanzeige und Schemaexploration um sie herum erstellen. Auf der anderen Seite erhalten Sie volle Flexibilität, um die Benutzererfahrung zu gestalten, die Ihren Bedürfnissen entspricht.

Das Einbetten der Azure Data Explorer-Webbenutzeroberfläche bietet Ihnen umfangreiche Funktionen mit geringem Aufwand, bietet jedoch nur begrenzte Flexibilität in Bezug auf die Benutzererfahrung. Es gibt einen festen Satz von Abfrageparametern, die eine eingeschränkte Kontrolle über das Aussehen und Verhalten des Systems ermöglichen.

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>Einbetten der Webbenutzeroberfläche in einen iframe

### <a name="host-the-website-in-iframe"></a>Hosten der Website in iframe

Fügen Sie Den folgenden Code zu Ihrer Website hinzu:

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

Der `ibizaPortal` Abfrageparameter weist die Azure Data Explorer-Webbenutzeroberfläche an, _nicht_ umzuleiten, um ein Authentifizierungstoken abzurufen. Dies ist erforderlich, da die Hosting-Website für die Bereitstellung eines Authentifizierungstokens für den eingebetteten iframe verantwortlich ist.

Ersetzen `<cluster>` Sie dies durch den Hostnamen des Clusters, den Sie in den Verbindungsbereich laden möchten (für `example: help.kusto.windows.net`). Standardmäßig bietet der iframe-eingebettete Modus keine Möglichkeit, Cluster aus der Benutzeroberfläche hinzuzufügen, da davon ausgegangen wird, dass die Hosting-Website den erforderlichen Cluster kennt.

### <a name="handle-authentication"></a>Behandeln der Authentifizierung

1. Wenn auf 'iframe-Modus' (`ibizaPortal=true`) festgelegt, versucht die Azure Data Explorer-Webbenutzeroberfläche nicht, für die Authentifizierung umzuleiten. Die Webbenutzeroberfläche verwendet den Nachrichtenbuchungsmechanismus, den Browser zum Anfordern und Empfangen eines Tokens verwenden. Während des Ladens der Seite wird die folgende Meldung im übergeordneten Fenster gesendet:

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

1. Anschließend wird auf eine Nachricht mit der folgenden Struktur gewartet:

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. Das bereitgestellte Token sollte ein [JWT-Token](https://tools.ietf.org/html/rfc7519) sein, das vom [[AAD-Authentifizierungsendpunkt]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)abgerufen wird.

> [!IMPORTANT]
> Es liegt in der Verantwortung des Hostingfensters, das Token vor Ablauf zu aktualisieren und den gleichen Mechanismus zu verwenden, um das aktualisierte Token für die Anwendung bereitzustellen. Andernfalls schlagen Dienstaufrufe nach Ablauf des Tokens immer fehl.

### <a name="feature-flags"></a>Featureflags

Die Hostinganwendung möchte möglicherweise bestimmte Aspekte der Benutzererfahrung zusammenstellen. Beispiel: Blenden Sie den Verbindungsbereich aus, oder deaktivieren Sie die Verbindung mit einem anderen Cluster.
Für dieses Szenario unterstützt der Web explorer Feature-Flags.

Ein Feature-Flag kann in der URL als Abfrageparameter verwendet werden. Wenn die Hostinganwendung z. B. das Hinzufügen eines anderen Clusters deaktivieren möchte, sollten siehttps://dataexplorer.azure.com/?ShowConnectionButtons=false

| Festlegen                 | Beschreibung                                                                                                                                                                                                                                                                                       | Standardwert |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| ShowShareMenu           | Anzeigen des Freigabemenüelements                                                                                                                                                                                                                                                                          | true          |
| ShowConnectionButtons   | Anzeigen der Schaltfläche Verbindung hinzufügen, um einen neuen Cluster hinzuzufügen                                                                                                                                                                                                                                               | true          |
| ShowOpenNewWindowButton | Zeigen Sie "In Web-UI-Schaltfläche öffnen. Dadurch wird ein neues Browserfenster https://dataexplorer.azure.com geöffnet, auf das mit dem richtigen Cluster und der richtigen Datenbank im Bereich                                                                                                                                   | False         |
| ShowFileMenu            | Dateimenü anzeigen (Tab-Inhalt herunterladen ETC)                                                                                                                                                                                                                                                     | true          |
| ShowtoS                 | Link zu den Nutzungsbedingungen für den azure-Daten-Explorer aus dem Einstellungsdialog anzeigen                                                                                                                                                                                                                | true          |
| ShowPersona             | Anzeigen des Benutzernamens oben rechts und im Einstellungsmenü                                                                                                                                                                                                                                        | true          |
| IFrameAuth              | Wenn true, erwartet web explorer, dass der iframe die Authentifizierung verarbeitet und ein Token per Nachricht bereitstellt. Dies gilt immer für iframe-Szenarien                                                                                                                                              | False         |
| PersistAfterEachRun     | Normalerweise bleibt der Webexplorer im onunload-Ereignis erhalten. Beim Hosten in iframes wird in einigen Fällen nicht gezündet. Daher löst dieses Flag nach jeder Abfrageausführung den persitierenden lokalen Status aus. Daher wirkt sich jeder Datenverlust, der auftritt, nur auf Text aus, der nie ausgeführt wurde, wodurch die Auswirkungen begrenzt werden. | False         |
| ShowSmoothIngestion     | Wenn true, zeigen Sie die 1-Klick-Aufnahme erfahrung, wenn Sie mit der rechten Maustaste auf eine Datenbank klicken                                                                                                                                                                                                                  | true          |
| RefreshConnection       | Wenn true, aktualisiert immer das Schema beim Laden der Seite und hängt nie vom lokalen Speicher ab.                                                                                                                                                                                                      | False         |
| ShowPageHeader          | Wenn true, wird der Seitenkopf angezeigt (der den Titel, die Einstellungen und andere Azure Data Explorer enthält)                                                                                                                                                                                                 | true          |
| HideConnectionPane      | Wenn true, zeigt nicht den linken Verbindungsbereich an                                                                                                                                                                                                                                                | False         |
| SkipMonacoFocusOnInit   | Behebt fokussieren Problem beim Hosting auf iframe                                                                                                                                                                                                                                                          | False         |

### <a name="feature-flag-presets"></a>Feature-Flag-Presets

Presets legen eine Reihe von Feature-Flags auf einmal fest.
Heute haben wir eine einzige Voreinstellung.

`IbizaPortal`- Ändert die folgenden Flags von den Standardeinstellungen:

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> Wenn Sie eine Voreinstellung verwenden, können Sie keine zusätzlichen Feature-Flags darüber hinzufügen. Wenn Sie diese Flexibilität benötigen, sollten Sie einzelne Feature-Flags verwenden.
