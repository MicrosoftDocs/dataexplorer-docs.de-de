---
title: Fehler im systemeigenen Code - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Fehler im systemeigenen Code in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/12/2019
ms.openlocfilehash: fa36bec3586a5e316b08ebc2949617268f0270ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523255"
---
# <a name="errors-in-native-code"></a>Fehler in nativem Code

Das Kusto-Modul wird in systemeigenem Code `HRESULT` geschrieben und meldet Fehler mithilfe negativer Werte. Diese Fehler werden normalerweise nicht mit einer programmgesteuerten API angezeigt, können jedoch auftreten. Betriebsfehler können z. B.`Exception from HRESULT:` den Status " *HRESULT-CODE*" darstellen.

Kusto native Fehlercodes werden `MAKE-HRESULT` mit Windows-Makro definiert mit:

* Schweregrad auf`1`
* Fazilität festgelegt, um`0xDA`
  
Folgende Fehlercodes sind definiert:

|Manifestkonstante                  |Code |Wert        |Bedeutung                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|Datenshard konnte nicht geladen werden                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|Die Abfrageausführung wurde abgebrochen, da die zulässigen Ressourcen überschritten wurden.                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|Ein Datenstrom kann nicht analysiert werden, da er schlecht formatiert ist.                                                      |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|Das Resultset für diese Abfrage überschreitet die zulässigen Datensatz-/Größengrenzen.                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|Ein Ergebnisstream kann nicht analysiert werden, da seine Version unbekannt ist.                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|Fehler beim Ausführen eines Schlüssel-/Wertdatenbankvorgangs                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|Abfrage wurde abgebrochen                                                                                            |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|Der Vorgang wurde abgebrochen, weil der verfügbare Prozessspeicher nicht verfügbar war.                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|Ein csv-Dokument, das zur Aufnahme übermittelt wird, weist einen Datensatz mit der falschen Anzahl von Feldern auf (im Verhältnis zu anderen Datensätzen)|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|Das zur Einnahme übermittelte Dokument hat die zulässige Länge überschritten.                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|Die angeforderte Entität wurde nicht gefunden.                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|Ein csv-Dokument, das zur Aufnahme übermittelt wird, hat ein Feld mit einem fehlenden Angebot.                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|Stellt einen arithmetischen Überlauffehler dar (das Ergebnis einer Berechnung ist zu groß für den Zieltyp)    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|Es wurde versucht, auf einen blockierten Zeilenspeicherschlüssel zuzugreifen.                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|Zeilenspeicher wird heruntergefahren                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|Der zugewiesene Speicherplatz für den Zeilenspeicherspeicher ist voll                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|Das Lesen aus dem Zeilenspeicherspeicher ist fehlgeschlagen.                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|Fehler beim Abrufen von Werten aus dem Zeilenspeicherspeicher                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|Zeilenspeicher initialisiert                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|Werteinfügung in einen Zeilenspeicher wurde gedrosselt                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|Zeilenspeicher ist im schreibgeschützten Zustand angefügt                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|Zeilenspeicher ist derzeit nicht verfügbar                                                                             |