---
title: series_ifft ()-Azure Daten-Explorer
description: In diesem Artikel wird series_ifft ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: c5862e9c18959726f27ea7d4237058f36a408b5c
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502133"
---
# <a name="series_ifft"></a>series_ifft ()

Wendet die umgekehrte schnelle Fourier-Transformation (IFFT) für eine Reihe an.  

Die series_ifft ()-Funktion nimmt eine Reihe komplexer Zahlen in der Häufigkeits Domäne und wandelt Sie mithilfe der [schnellen Fourier-Transformation](https://en.wikipedia.org/wiki/Fast_Fourier_transform)wieder in die Zeit-/räumliche Domäne um. Diese Funktion ist die ergänzende Funktion von [series_fft](series-fft-function.md). In der Regel wird die ursprüngliche Reihe in die Häufigkeits Domäne für die Verarbeitung von Daten und anschließend in die Zeit/räumliche Domäne transformiert.

## <a name="syntax"></a>Syntax

`series_ifft(`*fft_real* [ `,` *fft_imaginary*]`)`

## <a name="arguments"></a>Argumente

* *fft_real*: dynamisches Array von numerischen Werten, die die reelle Komponente der zu transformierenden Reihe darstellen.
* *fft_imaginary*: ein ähnliches dynamisches Array, das die imaginäre Komponente der Reihe darstellt. Dieser Parameter ist optional und sollte nur angegeben werden, wenn die Eingabe Reihe komplexe Zahlen enthält.

## <a name="returns"></a>Rückgabe

Die-Funktion gibt den komplexen umgekehrten "FFT" in zwei Reihen zurück. Die erste Reihe für die reale Komponente und die zweite Reihe für die imaginäre Komponente.

## <a name="example"></a>Beispiel

Siehe [series_fft](series-fft-function.md#example)
