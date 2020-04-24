---
title: parse_user_agent() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_user_agent() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ebe85c622dda116366e30ba22e2e495e4cb76ac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511729"
---
# <a name="parse_user_agent"></a>parse_user_agent()

Interpretiert eine Benutzer-Agent-Zeichenfolge, die den Browser des Benutzers identifiziert und bestimmte Systemdetails für Server bereitstellt, die die Websites hosten, die der Benutzer besucht. Das Ergebnis wird [`dynamic`](./scalar-data-types/dynamic.md)als zurückgegeben. 

**Syntax**

`parse_user_agent(`*user-agent-string*, *suchen*`)`

**Argumente**

* *user-agent-string*: Ein `string`Ausdruck vom Typ , der eine User-Agent-Zeichenfolge darstellt.

* *look-for*: Ein `string` Ausdruck `dynamic`vom Typ oder , der darstellt, wonach die Funktion in der Benutzer-Agent-Zeichenfolge (Analyseziel) gesucht werden soll. Die möglichen Optionen: "Browser", "os", "Gerät". Wenn nur ein einzelnes Analyseziel erforderlich `string` ist, kann ein Parameter übergeben werden.
Wenn zwei oder drei erforderlich sind, `dynamic array`können sie als übergeben werden.

**Rückgabe**

Ein Objekt `dynamic` vom Typ, das die Informationen zu den angeforderten Analysezielen enthält.

Browser: Familie, MajorVersion, MinorVersion, Patch                 

Betriebssystem: Familie, MajorVersion, MinorVersion, Patch, PatchMinor             

Gerät: Familie, Marke, Modell

> [!WARNING]
> Die Funktionsimplementierung basiert auf regex-Prüfungen der Eingabezeichenfolge gegen eine große Anzahl vordefinierter Muster. Daher ist die erwartete Zeit und CPU-Verbrauch hoch.
Wenn die Funktion in einer Abfrage verwendet wird, stellen Sie sicher, dass sie auf mehreren Computern verteilt ausgeführt wird.
Wenn Abfragen mit dieser Funktion häufig verwendet werden, sollten Sie die Ergebnisse über [die Aktualisierungsrichtlinie](../management/updatepolicy.md)vorab erstellen, aber Sie müssen berücksichtigen, dass die Verwendung dieser Funktion innerhalb der Aktualisierungsrichtlinie die Aufnahmelatenz erhöht.
 
**Beispiel**

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

Erwartetes Ergebnis ist ein dynamisches Objekt:

" Browser": "Familie": "AdobeAIR", "MajorVersion": "2", "MinorVersion": "5", "Patch": "1"

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

Erwartetes Ergebnis ist ein dynamisches Objekt:

" Browser": "Familie": "Nokia OSS Browser", "MajorVersion": "3", "MinorVersion": "1", "Patch": "" "OperatingSystem": "Familie": "Symbian OS", "MajorVersion": "9", "MinorVersion": "2", "Patch": "", "PatchMinor": "" , "Device": "Familie": "Nokia N81", "Brand": "Nokia", "Model": "N81-3"