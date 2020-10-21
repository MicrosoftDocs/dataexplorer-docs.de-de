---
title: parse_user_agent ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird parse_user_agent () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: a7261f6996aecdf10446c990dac54afa4638147e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246300"
---
# <a name="parse_user_agent"></a>parse_user_agent()

Interpretiert eine Benutzer-Agent-Zeichenfolge, die den Browser des Benutzers identifiziert und bestimmte Systemdetails für Server bereitstellt, die die vom Benutzer besucht Websites hosten. Das Ergebnis wird als zurückgegeben [`dynamic`](./scalar-data-types/dynamic.md) . 

## <a name="syntax"></a>Syntax

`parse_user_agent(`*Benutzer-Agent-Zeichenfolge*, *Suchen nach*`)`

## <a name="arguments"></a>Argumente

* *User-Agent-String*: ein Ausdruck vom Typ `string` , der eine Benutzer-Agent-Zeichenfolge darstellt.

* *Suchen nach*: ein Ausdruck vom Typ `string` oder `dynamic` , der die Funktion darstellt, nach der die Funktion in der Benutzeragentzeichenfolge suchen soll (Ziel des Ziels). Mögliche Optionen: "Browser", "Betriebssystem", "Gerät". Wenn nur ein einzelnes Ziel für die Verarbeitung erforderlich ist, kann ein Parameter übergeben werden `string` .
Wenn zwei oder drei erforderlich sind, können Sie als () `dynamic array` .

## <a name="returns"></a>Rückgabe

Ein Objekt vom Typ `dynamic` , das die Informationen zu den angeforderten erteilungszielen enthält.

Browser: Family, MajorVersion, MinorVersion, Patch                 

OperatingSystem: Family, MajorVersion, MinorVersion, Patch, patchminor             

Gerät: Familie, Marke, Modell

> [!WARNING]
> Die Funktions Implementierung basiert auf Regex-Überprüfungen der Eingabe Zeichenfolge für eine große Anzahl von vordefinierten Mustern. Daher ist die erwartete Zeit und CPU-Nutzung hoch.
Wenn die Funktion in einer Abfrage verwendet wird, stellen Sie sicher, dass Sie auf mehreren Computern verteilter Art und Weise ausgeführt wird.
Wenn Abfragen mit dieser Funktion häufig verwendet werden, empfiehlt es sich, die Ergebnisse über die [Aktualisierungs Richtlinie](../management/updatepolicy.md)vorab zu erstellen, aber Sie müssen berücksichtigen, dass durch die Verwendung dieser Funktion innerhalb der Update Richtlinie die Erfassungs Latenz erhöht wird.
 
## <a name="example"></a>Beispiel

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

Erwartetes Ergebnis ist ein dynamisches Objekt:

{"Browser": {"Family": "AdobeAir", "MajorVersion": "2", "MinorVersion": "5", "Patch": "1"}}

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

Erwartetes Ergebnis ist ein dynamisches Objekt:

{"Browser": {"Family": "Nokia OSS Browser", "MajorVersion": "3", "MinorVersion": "1", "Patch": ""}, "OperatingSystem": {"Family": "Symbian OS", "MajorVersion": "9", "MinorVersion": "2", "Patch": "", "patchminor": ""}, "Device": {"Family": "Nokia N81", "Brand": "Nokia", "Model": "N81-3"}}