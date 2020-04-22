---
title: Datenerfassung – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Datenerfassung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: f41304ae4ac51081cd61c41856ed5e7e08ed6f7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490377"
---
# <a name="data-ingestion"></a>Datenerfassung

Der Prozess, bei dem Daten einer Tabelle hinzugefügt und für Abfragen verfügbar gemacht wird.
Für den Prozess sind [Berechtigungen vom Typ „database admin“ (Datenbankadministrator), „database ingestor“ (Datenbankerfasser), „database user“ (Datenbankbenutzer) oder „table admin“ (Tabellenadministrator)](../access-control/role-based-authorization.md) erforderlich – je nachdem, ob die Tabelle bereits vorhanden ist.

Der Datenerfassungsprozess umfasst mehrere Schritte:

1. Abrufen der Daten aus der Datenquelle
1. Analysieren und Überprüfen der Daten
1. Abgleichen des Schemas der Daten mit dem Schema der Kusto-Zieltabelle oder Erstellen der Zieltabelle, falls sie noch nicht vorhanden ist
1. Strukturieren der Daten in Spalten
1. Indizieren der Daten
1. Codieren und Komprimieren der Daten
1. Persistentes Speichern der resultierenden Kusto-Speicherartefakte
1. Ausführen aller relevanten Aktualisierungsrichtlinien (sofern vorhanden)
1. Committen der Datenerfassung, um sie für Abfragen verfügbar zu machen

> [!NOTE]
> Einige der obigen Schritte können je nach Szenario ggf. übersprungen werden.
> Bei der Datenerfassung über den Streamingerfassungsendpunkt werden beispielsweise die Schritte 4, 5, 6 und 9 übersprungen. Diese werden während der Datenbereinigung im Hintergrund ausgeführt.
> Ein anderes Beispiel: Wenn es sich bei der Datenquelle um die Ergebnisse einer Kusto-Abfrage für den gleichen Cluster handelt, müssen die Daten nicht analysiert und überprüft werden.

> [!WARNING]
> Für Daten, die in einer Tabelle in Kusto erfasst werden, gilt die effektive **Aufbewahrungsrichtlinie** der Tabelle.
> Wurde für eine Tabelle nicht explizit eine effektive Aufbewahrungsrichtlinie festgelegt, wird sie von der Aufbewahrungsrichtlinie der Datenbank abgeleitet. Daher sollten Sie beim Erfassen von Daten in Kusto sicherstellen, dass die Aufbewahrungsrichtlinie der Datenbank Ihren Anforderungen entspricht. Ist dies nicht der Fall, überschreiben Sie sie explizit auf Tabellenebene. Ansonsten werden die Daten aufgrund der Aufbewahrungsrichtlinie der Datenbank unter Umständen automatisch gelöscht. Weitere Informationen finden Sie in [Aufbewahrungsrichtlinie](https://kusto.azurewebsites.net/docs/concepts/retentionpolicy.html).

Informationen zu den Eigenschaften der Datenerfassung finden Sie unter [Eigenschaften der Datenerfassung](https://docs.microsoft.com/azure/data-explorer/ingestion-properties).
Eine Liste der unterstützten Formate für die Datenerfassung finden Sie unter [Datenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).



## <a name="ingestion-methods"></a>Erfassungsmethoden

Für die Datenerfassung stehen unterschiedliche Methoden zur Verfügung:

1. **Inline-Erfassung (Push):** An die Engine wird ein Steuerungsbefehl ([.ingest inline](./ingest-inline.md)) gesendet, und die zu erfassenden Daten werden direkt im Befehlstext angegeben.
   Diese Methode ist in erster Linie für Ad-hoc-Tests vorgesehen und sollte nicht in der Produktion verwendet werden.
1. **Erfassung aus einer Abfrage:** An die Engine wird ein Steuerungsbefehl ([„.set“, „.append“, „.set-or-append“ oder „.set-or-replace“](./ingest-from-query.md)) gesendet, und die Daten werden indirekt als die Ergebnisse einer Abfrage oder eines Befehls angegeben.
   Diese Methode ermöglicht die Erstellung von Berichtstabellen aus Tabellen mit unformatierten Daten sowie die Erstellung kleiner temporärer Tabellen für die weitere Analyse.
1. **Erfassung aus dem Speicher (Pull):** An die Engine wird ein Steuerungsbefehl ([.ingest into](./ingest-from-storage.md)) gesendet, und die Daten sind in einem externen Speicher (beispielsweise in Azure Blob Storage) gespeichert, auf den die Engine zugreifen kann und auf den durch den Befehl verwiesen wird.
   Diese Methode ermöglicht eine effiziente Sammelerfassung von Daten. Es muss jedoch darauf geachtet werden, dass der Client, der die Erfassung durchführt, den Cluster nicht mit gleichzeitigen Erfassungen überlastet. (Andernfalls werden ggf. alle Clusterressourcen durch die Datenerfassung beansprucht, was die Abfrageleistung beeinträchtigt.)
1. **Erfassung aus der Warteschlange:** Daten werden in den externen Speicher hochgeladen (z. B. Azure Blob Storage). Eine Benachrichtigung wird an eine Warteschlange gesendet (z. B. Azure Queue oder Event Hub).
   Dies ist die bevorzugte Methode für Produktionsumgebungen, da sie sich durch eine sehr hohe Verfügbarkeit auszeichnet, der Client die Kapazitätsverwaltung nicht selbst bewältigen muss und sie auch für Massenanfügevorgänge geeignet ist. Sie wird gelegentlich auch als native Erfassung bezeichnet.


|Methode             |Latency                 |Bereitstellung|Massenvorgang|Verfügbarkeit|Synchronität|
|-------------------|------------------------|----------|----|------------|-------------|
|Inline-Erfassung   |Sekunden + Erfassungszeit   |Nein        |Nein  |Kusto-Engine|Synchron  |
|Erfassung aus der Abfrage  |Abfragezeit + Erfassungszeit|Ja       |Ja |Kusto-Engine|Synchron  |
|Erfassung aus dem Speicher|Sekunden + Erfassungszeit   |Ja       |Ja |Kusto-Engine|Beide         |
|Erfassung aus der Warteschlange   |Minuten                 |Ja       |Ja |Storage     |Asynchron |

## <a name="validation-policy-during-ingestion"></a>Überprüfungsrichtlinie während der Erfassung

Bei der Erfassung aus dem Speicher werden die Quelldaten im Rahmen der Analyse überprüft.
Die Überprüfungsrichtlinie gibt an, wie auf Fehler bei der Analyse reagiert werden soll. Sie umfasst zwei Eigenschaften:

* `ValidationOptions`: `0` bedeutet hier, dass keine Überprüfung durchgeführt werden soll. Bei Angabe von `1` wird überprüft, ob alle Datensätze über die gleiche Anzahl von Feldern verfügen, was bei CSV-Dateien und ähnlichen Formaten hilfreich ist. Bei Angabe von `2` werden Felder ohne doppelte Anführungszeichen ignoriert.

* `ValidationImplications`: `0` gibt an, dass Überprüfungsfehler einen Fehler für die gesamte Erfassung zur Folge haben sollen. `1` gibt an, dass Überprüfungsfehler ignoriert werden sollen.