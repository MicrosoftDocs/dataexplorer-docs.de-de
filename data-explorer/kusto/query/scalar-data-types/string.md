---
title: 'Der Zeichen folgen-Datentyp: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den String-Datentyp in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7c043a9b4d8b141d2dc45e88022e191e6483c35
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264809"
---
# <a name="the-string-data-type"></a>Der Zeichen folgen-Datentyp.

Der- `string` Datentyp stellt eine Unicode-Zeichenfolge dar. Kusto-Zeichen folgen werden in UTF-8 codiert und sind standardmäßig auf 1 MB beschränkt.

## <a name="string-literals"></a>Zeichenfolgenliterale

Es gibt mehrere Möglichkeiten, Literale des `string` Datentyps zu codieren.

* Schließen Sie die Zeichenfolge in doppelte Anführungszeichen ( `"` ) ein:`"This is a string literal. Single quote characters (') don't require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* Schließen Sie die Zeichenfolge in einfache Anführungszeichen ( `'` ) ein:`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

In den beiden obigen Darstellungen gibt der umgekehrte Schrägstrich ( `\` ) Escapezeichen an.
Es wird verwendet, um die einschließenden Anführungszeichen, Tabstopp Zeichen (), Zeilen vorzeichenfolge ( `\t` `\n` ) und selbst () mit Escapezeichen zu versehen `\\` .

Ausführliche Zeichen folgen Literale werden ebenfalls unterstützt. In dieser Form steht der umgekehrte Schrägstrich ( `\` ) für sich selbst und nicht als Escapezeichen.

* In doppelte Anführungszeichen () einschließen `"` :`@"This is a verbatim string literal that ends with a backslash\"`
* In einfache Anführungszeichen () einschließen `'` :`@'This is a verbatim string literal that ends with a backslash\'`

Zwei Zeichen folgen Literale in der Abfrage, für die nichts zwischen Ihnen besteht oder die nur durch Leerräume und Kommentare getrennt sind, werden automatisch mit einem neuen zeichenfolgenliteralvorgang verknüpft.
Die folgenden Ausdrücke geben beispielsweise alle die Länge zurück `13` .

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>Beispiele

```kusto
// Simple string notation
print s1 = 'some string', s2 = "some other string"

// Strings that include single or double-quotes can be defined as follows
print s1 = 'string with " (double quotes)',
          s2 = "string with ' (single quotes)"

// Strings with '\' can be prefixed with '@' (as in c#)
print myPath1 = @'C:\Folder\filename.txt'

// Escaping using '\' notation
print s = '\\n.*(>|\'|=|\")[a-zA-Z0-9/+]{86}=='
```

Wenn eine Zeichenfolge in doppelten Anführungszeichen () eingeschlossen ist `"` , muss das einfache Anführungszeichen () nicht mit Escapezeichen versehen werden `'` . Diese Methode vereinfacht das Anführungszeichen von Zeichen folgen gemäß Kontext.

## <a name="obfuscated-string-literals"></a>Verborgene Zeichen folgen Literale

Das System verfolgt Abfragen und speichert Sie zu Telemetrie-und Analysezwecken.
Beispielsweise kann der Abfragetext dem Cluster Besitzer zur Verfügung gestellt werden. Wenn der Abfragetext geheime Informationen (z. b. Kenn Wörter) enthält, werden möglicherweise Informationen, die privat aufbewahrt werden sollten, nicht berücksichtigt. Um zu verhindern, dass ein derartiger Vorgang durchgeführt wird, kennzeichnet der Abfrage Autor möglicherweise bestimmte Zeichenfolgenliterale als verborgene **Zeichen folgen Literale**.
Solche Literale im Abfragetext werden automatisch durch eine Reihe von Stern `*` Zeichen () ersetzt, sodass Sie für die spätere Analyse nicht verfügbar sind.

> [!IMPORTANT]
> Markieren Sie alle Zeichen folgen Literale, die geheime Informationen enthalten, als verborgene Zeichen folgen Literale.

Ein verborgenes Zeichenfolgenliteralzeichen kann gebildet werden, indem ein reguläres Zeichenfolgenliteralzeichen als vorangestellt wird `h` `H` . 

Beispiel:

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> In vielen Fällen ist nur ein Teil des Zeichenfolgenliterals geheim. Teilen Sie in diesen Fällen das Literale in einen nicht geheimen Teil und einen geheimen Teil auf. Markieren Sie dann nur den geheimen Teil als verborgen.

Beispiel:

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```
