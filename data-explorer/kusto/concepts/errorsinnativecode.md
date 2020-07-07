---
title: 'Fehler in nativem Code: Azure Daten-Explorer'
description: In diesem Artikel werden Fehler in nativem Code in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: f1c076ba2c85ee18474372e4e19b285c133633d7
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013838"
---
# <a name="errors-in-native-code"></a>Fehler in nativem Code

Die Kusto-Engine ist in nativem Code geschrieben und meldet Fehler mithilfe negativer `HRESULT` Werte. Diese Fehler sind bei einer programmgesteuerten API ungewöhnlich, können aber auftreten. Vorgangs Fehler können z. b. den Status " `Exception from HRESULT:` *HRESULT-Code*" anzeigen.

Systemeigene Kusto-Fehlercodes werden mithilfe eines Windows- `MAKE-HRESULT` Makros mit definiert:

* Schweregrad festgelegt auf`1`
* Einrichtung festgelegt auf`0xDA`
  
Die folgenden Fehlercodes sind definiert.

|Manifest-Konstante                  |Code  |Wert       |Bedeutung                                                                                                        |
|-----------------------------------|------|------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|Der Datenblock konnte nicht geladen werden.                                                                                 |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|Die Abfrage Ausführung wurde abgebrochen, da Sie die zulässigen Ressourcen überschreitet                                              |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|Ein Datenstrom kann nicht analysiert werden, da er falsch formatiert ist.                                                     |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|Das Resultset für diese Abfrage überschreitet die zulässigen Datensätze/Größenbeschränkungen.                                         |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|Ein Ergebnisdaten Strom kann nicht analysiert werden, da seine Version unbekannt ist.                                                 |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|Fehler in der eingebetteten Schlüssel-Wert-Speicherkomponente                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|Abfrage wurde abgebrochen.                                                                                             |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|Es ist wenig Arbeitsspeicher verfügbar.                                                                                  |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|Falsche Anzahl von Feldern im Eingabestream                                                                 |
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|Die Eingabe Größe eines Felds/Datensatzes/Datenstroms hat die zulässige Länge überschritten.                                          |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|Die angeforderte Entität wurde nicht gefunden                                                                              |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|Ein für die Erfassung gesendete CSV-Datenstrom weist ein Feld mit fehlendem Anführungszeichen auf.                                          |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|Stellt einen arithmetischen Überlauf Fehler dar. Das Ergebnis einer Berechnung ist zu groß für den Zieltyp.     |
|`E_INCOMPATIBLE_TOKENIZERS`        | `13` |`0x80DA000D`|Blöcke bei mergefehlern aufgrund inkompatibler Anwendungs-und Zusammenführungs Indizes                                    |
|`E_DYNAMIC_PROPERTY_BAG_TOO_LARGE` | `14` |`0x80DA000E`|Die kombinierte Größe der eindeutigen Schlüssel des Eigenschaften Behälters ist zu groß.                                             |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|Es wurde versucht, auf einen blockierten rowstore-Schlüssel zuzugreifen.                                                           |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|Der rowstore wird heruntergefahren.                                                                                      |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|Der lokale Datenträger Speicher für rowstore ist voll.                                                                        |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|Fehler beim Lesen aus dem Write-Ahead-Log-Speicher des rowstores.                                                           |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|Fehler beim Abrufen von Werten aus dem rowstore-Speicher.                                                               |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|Rowstore wird initialisiert.                                                                                       |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|Das Einfügen von Werten in einen rowstore wurde gedrosselt.                                                                    |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|Rowstore ist im schreibgeschützten Zustand angefügt.                                                                        |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|Rowstore ist zurzeit nicht verfügbar.                                                                              |
|`E_RS_DOES_NOT_EXIST_ERROR`        | `110`|`0x80DA006E`|Rowstore ist nicht vorhanden.                                                                                         |
|`E_SB_QUERY_THROTTLED_ERROR`       | `200`|`0x80DA00C8`|Sandbox-Abfrage wurde gedrosselt.                                                                                  |
|`E_SB_QUERY_CANCELLED`             | `201`|`0x80DA00C9`|Sandbox-Abfrage wurde abgebrochen.                                                                                   |
|`E_UNSUPPORTED_INSTRUCTION_SET`    | `202`|`0x80DA00CA`|Der erforderliche Anweisungs Satz der Engine wird von dieser CPU nicht unterstützt.                                                   |
