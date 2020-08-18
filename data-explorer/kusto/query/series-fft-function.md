---
title: series_fft ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fft ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 32b3b978f57f1a346ccd697fa2d95d962b558a15
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502136"
---
# <a name="series_fft"></a>series_fft ()

Wendet die schnelle Fourier-Transformation (FFT) auf eine Reihe an.  

Die series_fft ()-Funktion nimmt eine Reihe komplexer Zahlen in der Zeit/räumlichen Domäne und wandelt Sie mithilfe der [schnellen Fourier-Transformation](https://en.wikipedia.org/wiki/Fast_Fourier_transform)in die Frequenzdomäne um. Die transformierte komplexe Reihe stellt die Größe und die Phase der Frequenzen dar, die in der ursprünglichen Reihe angezeigt werden. Verwenden Sie die komplementäre Funktion [series_ifft](series-ifft-function.md) , um von der Häufigkeits Domäne zurück in die Zeit/räumliche Domäne umzuwandeln.

## <a name="syntax"></a>Syntax

`series_fft(`*x_real* [ `,` *x_imaginary*]`)`

## <a name="arguments"></a>Argumente

* *x_real*: dynamisches Array von numerischen Werten, die die reelle Komponente der zu transformierenden Reihe darstellen.
* *x_imaginary*: ein ähnliches dynamisches Array, das die imaginäre Komponente der Reihe darstellt. Dieser Parameter ist optional und sollte nur angegeben werden, wenn die Eingabe Reihe komplexe Zahlen enthält.

## <a name="returns"></a>Rückgabe

Die-Funktion gibt den komplexen umgekehrten "FFT" in zwei Reihen zurück. Die erste Reihe für die reale Komponente und die zweite Reihe für die imaginäre Komponente.

## <a name="example"></a>Beispiel

* Generieren Sie eine komplexe Reihe, bei der die reale und die imaginären Komponenten reine Sinuswellen in unterschiedlichen Frequenzen sind. Verwenden Sie FFT, um es in die Frequency-Domäne umzuwandeln:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | render linechart with(ysplit=panels)
    ```
    
    Diese Abfrage gibt *fft_y_real* und *fft_y_imag*zurück:  
    
    :::image type="content" source="images/series-fft-function/series-fft.png" alt-text="Reihe-FFT" border="false":::
    
* Transformieren Sie eine Reihe in die Frequency-Domäne, und wenden Sie dann die umgekehrte Transformation an, um die ursprüngliche Reihe wieder aufzunehmen:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | extend (y_real2, y_image2) = series_ifft(fft_y_real, fft_y_imag)
    | project-away fft_y_real, fft_y_imag   //  too many series for linechart with panels
    | render linechart with(ysplit=panels)
    ```
    
    Diese Abfrage gibt *y_real2* und * y_imag2 zurück, die mit *y_real* und *y_imag*identisch sind:  
    
    :::image type="content" source="images/series-fft-function/series-ifft.png" alt-text="Reihe IFFT" border="false":::
    