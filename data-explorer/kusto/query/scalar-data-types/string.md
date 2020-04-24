---
title: Der Zeichenfolgendatentyp - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Zeichenfolgendatentyp in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c040045ba7146cf56487ca3a8729372084d3bf2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509621"
---
# <a name="the-string-data-type"></a>Der Zeichenfolgendatentyp

Der `string` Datentyp stellt eine Unicode-Zeichenfolge dar. (Kusto-Zeichenfolgen sind in UTF-8 codiert und standardmäßig auf 1 MB begrenzt.)

## <a name="string-literals"></a>Zeichenfolgenliterale

Es gibt mehrere Möglichkeiten, Literale `string` des Datentyps zu codieren:

* Durch Einschließen der Zeichenfolge in`"`Doppelanführungszeichen ( ):`"This is a string literal. Single quote characters (') do not require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* Durch Einschließen der Zeichenfolge in`'`einfache Anführungszeichen ( ):`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

In den beiden obigen Darstellungen`\`zeigt das umgekehrte Schrägstrich ( ) das Entkommen an.
Es wird verwendet, um die einschließenden`\t`Zitatzeichen zu`\n`entkommen,`\\`Tab-Zeichen ( ), Zeilenumzufolge - und sich selbst ( ).

Verbatim String-Literale werden ebenfalls unterstützt. In dieser Form steht das`\`umgekehrte Schrägstrichzeichen ( ) für sich selbst, nicht als Escape-Zeichen:

* In Doppelnotierungen`"`( )`@"This is a verbatim string literal that ends with a backslash\"`
* Eingeschlossen in einfache`'`Notierungen ( ):`@'This is a verbatim string literal that ends with a backslash\'`

Zwei Zeichenfolgenliterale im Abfragetext, deren Verbindung nichts enthält oder nur durch Leerzeichen und Kommentare getrennt ist, werden automatisch zu einem neuen Zeichenfolgenliteral verkettet (bis eine solche Ersetzung nicht vorgenommen werden kann).
Die folgenden Ausdrücke ergeben `13`z. B. alle:

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

Wie man sieht, erfordert das Single-Quote (`"``'`)-Zeichen keine Flucht und umgekehrt, wenn eine Zeichenfolge in Doppelanführungszeichen ( eingeschlossen ist. Dies erleichtert das Anzitieren von Zeichenfolgen nach Kontext.

## <a name="obfuscated-string-literals"></a>Verschleierte Stringliterale

Das System verfolgt Abfragen und speichert sie zu Telemetrie- und Analysezwecken.
Der Abfragetext kann z. B. dem Clusterbesitzer zur Verfügung gestellt werden. Wenn der Abfragetext geheime Informationen (z. B. Kennwörter) enthält, kann dies Informationen austreten, die vertraulich behandelt werden sollten. Um dies zu verhindern, kann der Abfrageautor bestimmte Zeichenfolgenliterale als **verschleierte Zeichenfolgenliterale**markieren.
Solche Literale im Abfragetext werden automatisch durch`*`eine Anzahl von Sternzeichen ( ) ersetzt, sodass sie für eine spätere Analyse nicht verfügbar sind.

> [!IMPORTANT]
> Es wird **dringend empfohlen,** alle Zeichenfolgenliterale, die geheime Informationen enthalten, als verschleierte Zeichenfolgenliterale zu kennzeichnet.

Ein verschleiertes Zeichenfolgenliteral kann gebildet werden, indem ein "reguläres" `h` Zeichenfolgenliteral verwendet wird und ein oder ein `H` Zeichen davor voransteht. Beispiel:

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> In vielen Fällen ist nur ein Teil des Zeichenfolgenliterals geheim. In diesen Fällen ist es sehr nützlich, das Literal in einen nicht geheimen Teil und einen geheimen Teil aufzuteilen und dann nur den geheimen Teil als verschleiert zu markieren. Beispiel:

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```