---
title: Verwaltungsrichtlinien Verwaltung für Kusto-Streaming-Azure Daten-Explorer
description: In diesem Artikel wird die Richtlinien Verwaltung für die Streaminglösung in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 6f7efe2916bc5c5344bf4a6c4fa4a60bfaa167b7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967348"
---
# <a name="streaming-ingestion-policy-command"></a>Befehl zum Übernehmen der streamingrichtlinie

Die Richtlinie für die streamingansung kann für eine Tabelle festgelegt werden, um die streamingerfassung in diese Tabelle zuzulassen. Die Richtlinie kann auch auf Datenbankebene festgelegt werden, um dieselbe Einstellung auf aktuelle und zukünftige Tabellen anzuwenden.

Weitere Informationen finden Sie unter [streamingansung](../../ingest-data-streaming.md). Weitere Informationen zur Richtlinie für die streamingansung finden Sie unter Streaming-Erfassungs [Richtlinie](streamingingestionpolicy.md).

## <a name="display-the-policy"></a>Anzeigen der Richtlinie

Der `.show policy streamingingestion` Befehl zeigt die Richtlinie für die streamingansung der Datenbank oder Tabelle an.
 
**Syntax**

`.show``{database|table}` &lt; Entitäts Name &gt; `policy``streamingingestion`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit den folgenden Spalten zurück:

|Spalte    |type    |BESCHREIBUNG
|---|---|---
|PolicyName|`string`|Der Richtlinien Name: streamingingestionpolicy
|EntityName|`string`|Datenbank-oder Tabellenname
|Policy    |`string`|[Richtlinien Objekt für die streamingerfassung](#streaming-ingestion-policy-object)

**Beispiele**

```kusto
.show database DB1 policy streamingingestion

.show table T1 policy streamingingestion
```

|PolicyName|EntityName|Policy|Childentities|EntityType|
|---|---|---|---|---|
|Streamingingestionpolicy|DB1|{"Isaktivierte": true, "hintallocatedrate": NULL}

## <a name="change-the-policy"></a>Ändern der Richtlinie

Die- `.alter[-merge] policy streamingingestion` Befehls Familie bietet die Möglichkeit zum Ändern der Richtlinie für die streamingansung der Datenbank oder Tabelle.

**Syntax**

* `.alter``{database|table}` &lt; Entitäts &gt; `policy` Name `streamingingestion``[enable|disable]`

* `.alter``{database|table}` &lt; &gt; `policy` `streamingingestion` &lt; [Richtlinien Objekt](#streaming-ingestion-policy-object) zur Erfassung von Entitäts Namen Streaming&gt;

* `.alter-merge``{database|table}` &lt; &gt; `policy` `streamingingestion` &lt; [Richtlinien Objekt](#streaming-ingestion-policy-object) zur Erfassung von Entitäts Namen Streaming&gt;

> [!Note]
>
> * Ermöglicht das Ändern des aktivierten/deaktivierten Zustands der streamingerfassung, ohne andere Eigenschaften der Richtlinie zu ändern oder die Eigenschaften auf die Standardwerte festzulegen, wenn die Richtlinie zuvor nicht in der Entität definiert
>
> * Ermöglicht das Ersetzen der gesamten streamingaberfassungs-Richtlinie für die Entität. Das [Richtlinien Objekt für die streamingansung](#streaming-ingestion-policy-object) muss alle obligatorischen Eigenschaften enthalten.
>
> * Ermöglicht das ersetzen nur der angegebenen Eigenschaften der streamingerfassungs Richtlinie für die Entität. Das [Richtlinien Objekt für die streamingerfassung](#streaming-ingestion-policy-object) kann einige oder keine der obligatorischen Eigenschaften enthalten.

**Rückgabe**

Der Befehl ändert das Tabellen-oder Daten Bank `streamingingestion` Richtlinien [ `.show policy` `streamingingestion` ](#display-the-policy) Objekt und gibt dann die Ausgabe des entsprechenden Befehls zurück.

**Beispiele**

```kusto
.alter database DB1 policy streamingingestion enable

.alter table T1 policy streamingingestion disable

.alter database DB1 policy streamingingestion '{"IsEnabled": true, "HintAllocatedRate": 2.1}'

.alter table T1 streamingingestion '{"IsEnabled": true}'

.alter-merge database DB1 policy streamingingestion '{"IsEnabled": false}'

.alter-merge table T1 policy streamingingestion '{"HintAllocatedRate": 1.5}'
```

## <a name="delete-the-policy"></a>Löschen der Richtlinie

Der `.delete policy streamingingestion` Befehl löscht die streamingingestion-Richtlinie aus der Datenbank oder Tabelle.

**Syntax**

`.delete``{database|table}` &lt; Entitäts Name &gt; `policy``streamingingestion`

**Rückgabe**

Mit dem Befehl wird das Richtlinien Objekt "Table" oder "Database streamingingestion" gelöscht und anschließend die Ausgabe des entsprechenden Befehls " [. Show Policy streamingingestion](#display-the-policy) " zurückgegeben.

**Beispiele**

```kusto
.delete database DB1 policy streamingingestion

.delete table T1 policy streamingingestion
```

### <a name="streaming-ingestion-policy-object"></a>Richtlinien Objekt für die streamingerfassung

In der Eingabe und Ausgabe von Verwaltungs Befehlen ist das Richtlinien Objekt für die streamingerfassung eine JSON-formatierte Zeichenfolge, die die folgenden Eigenschaften enthält.

|Eigenschaft|type|BESCHREIBUNG|Erforderlich/optional
|---|---|---|---
|isEnabled|`bool`|Ist die streamingansung für die Entität aktiviert| Erforderlich
|Hintallocatedrate|`double`|Geschätzte Datenübertragungsrate in GB/Stunde|Optional
