---
title: 'Skalarfunktionen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden skalare Funktionen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 3a6897bc7acce8cf216c0f21cd46251c2f033d6c
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712135"
---
# <a name="scalar-function-types"></a>Skalarfunktionstypen

## <a name="binary-functions"></a>Binäre Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|Gibt das Ergebnis der bitweisen and-Operation zwischen zwei Werten zurück.|
|[binary_not()](binary-notfunction.md)|Gibt eine bitweise Negation des Eingabe Werts zurück.|
|[binary_or()](binary-orfunction.md)|Gibt das Ergebnis der bitweisen OR-Operation der beiden Werte zurück.|
|[binary_shift_left()](binary-shift-leftfunction.md)|Gibt einen binären Shift Left-Vorgang für ein Zahlenpaar zurück: a << n.|
|[binary_shift_right()](binary-shift-rightfunction.md)|Gibt den binären Verschiebungs rechten Vorgang für ein Zahlenpaar zurück: a >> n.|
|[binary_xor()](binary-xorfunction.md)|Gibt das Ergebnis der bitweisen XOR-Operation der beiden Werte zurück.|
|[bitset_count_ones()](bitset-count-onesfunction.md)|Gibt die Anzahl der Satz Bits in der binären Darstellung einer Zahl zurück.|

## <a name="conversion-functions"></a>Konvertierungsfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|Konvertiert Eingaben in eine boolesche Darstellung (8-Bit-Darstellung mit Vorzeichen).|
|[todatetime()](todatetimefunction.md)|Konvertiert die Eingabe in einen DateTime-skalaren Wert.|
|["gedouble ()"/toreal ()](todoublefunction.md)|Konvertiert die Eingabe in einen Wert vom Typ "Real". ("gedouble ()" und "toreal ()" sind Synonyme.)|
|[tostring()](tostringfunction.md)|Konvertiert Eingaben in eine Zeichen folgen Darstellung.|
|[totimespan()](totimespanfunction.md)|Konvertiert Eingaben in TimeSpan Scalar.|

## <a name="datetimetimespan-functions"></a>DateTime/TimeSpan-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|Subtrahiert den angegebenen Zeitraum von der aktuellen UTC-Uhrzeit.|
|[datetime_add()](datetime-addfunction.md)|Berechnet einen neuen DateTime-Wert aus einem angegebenen datepart-Wert multipliziert mit einem angegebenen Wert, der einem angegebenen DateTime-Wert hinzugefügt wird.|
|[datetime_part()](datetime-partfunction.md)|Extrahiert den angeforderten Datums Teil als ganzzahligen Wert.|
|[datetime_diff()](datetime-difffunction.md)|Gibt das Ende des Jahres zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[dayofmonth()](dayofmonthfunction.md)|Gibt die ganzzahlige Zahl zurück, die die Tagesnummer des angegebenen Monats darstellt.|
|[dayofweek()](dayofweekfunction.md)|Gibt die ganzzahlige Anzahl von Tagen seit dem vorangegangenen Sonntag als Zeitspanne zurück.|
|[dayofyear()](dayofyearfunction.md)|Gibt die Ganzzahl zurück, die die Tagesnummer des angegebenen Jahrs darstellt.|
|[endofday()](endofdayfunction.md)|Gibt das Ende des Tages zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[endofmonth()](endofmonthfunction.md)|Gibt das Ende des Monats zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[endofweek()](endofweekfunction.md)|Gibt das Ende der Woche zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[endofyear()](endofyearfunction.md)|Gibt das Ende des Jahres zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[format_datetime()](format-datetimefunction.md)|Formatiert einen DateTime-Parameter basierend auf dem Format Muster Parameter.|
|[format_timespan()](format-timespanfunction.md)|Formatiert einen Format-TimeSpan-Parameter basierend auf dem Format Muster Parameter.|
|[getmonth()](getmonthfunction.md)|Rufen Sie die Monatsnummer (1-12) aus einem datetime-Wert ab.|
|[getyear()](getyearfunction.md)|Gibt den Jahres Teil des datetime-Arguments zurück.|
|[hourofday()](hourofdayfunction.md)|Gibt die ganzzahlige Zahl zurück, die die Stundenzahl des angegebenen Datums darstellt.|
|[make_datetime()](make-datetimefunction.md)|Erstellt einen DateTime-Skalarwert aus dem angegebenen Datum und der angegebenen Uhrzeit.|
|[make_timespan()](make-timespanfunction.md)|Erstellt einen TimeSpan-Skalarwert aus dem angegebenen Zeitraum.|
|[monthofyear()](monthofyearfunction.md)|Gibt die Ganzzahl zurück, die die Monatsnummer des angegebenen Jahrs darstellt.|
|[now()](nowfunction.md)|Gibt die aktuelle UTC-Uhrzeit (optional) um einen bestimmten TimeSpan-Wert zurück.|
|[startofday()](startofdayfunction.md)|Gibt den Anfang des Tages zurück, der das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[startofmonth()](startofmonthfunction.md)|Gibt den Anfang des Monats zurück, der das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[startofweek()](startofweekfunction.md)|Gibt den Anfang der Woche zurück, der das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[startofyear()](startofyearfunction.md)|Gibt den Anfang des Jahres zurück, der das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.|
|[todatetime()](todatetimefunction.md)|Konvertiert die Eingabe in einen DateTime-skalaren Wert.|
|[totimespan()](totimespanfunction.md)|Konvertiert Eingaben in TimeSpan Scalar.|
|[unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)|Konvertiert Unix-Epoche-Mikrosekunden in UTC-DateTime.|
|[unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)|Konvertiert Unix-Epoch-Millisekunden in UTC-DateTime.|
|[unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)|Konvertiert Unix-Epoch Nanosekunden in UTC-DateTime.|
|[unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)|Konvertiert Unix-Epoch seconds in UTC-DateTime.|
|[weekofyear()](weekofyearfunction.md)|Gibt eine ganze Zahl zurück, die die Wochen Nummer darstellt.|


## <a name="dynamicarray-functions"></a>Dynamic/Array-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|Verkettet eine Reihe dynamischer Arrays mit einem einzelnen Array.|
|[array_iif()](arrayifffunction.md)|Wendet die Element Weise IIf-Funktion auf Arrays an.|
|[array_index_of()](arrayindexoffunction.md)|Durchsucht das Array nach dem angegebenen Element und gibt seine Position zurück.|
|[array_length ()](arraylengthfunction.md)|Berechnet die Anzahl der Elemente in einem dynamischen Array.|
|[array_rotate_left()](array_rotate_leftfunction.md)|Rotiert Werte innerhalb eines dynamischen Arrays nach links.|
|[array_rotate_right()](array_rotate_rightfunction.md)|Rotiert Werte innerhalb eines dynamischen Arrays nach rechts.|
|[array_shift_left()](array_shift_leftfunction.md)|Verschiebt Werte innerhalb eines dynamischen Arrays nach links.|
|[array_shift_right()](array_shift_rightfunction.md)|Verschiebt Werte innerhalb eines dynamischen Arrays nach rechts.|
|[array_slice()](arrayslicefunction.md)|Extrahiert einen Slice eines dynamischen Arrays.|
|[array_split()](arraysplitfunction.md)|Erstellt ein Array von Arrays, die aus dem Eingabe Array geteilt werden.|
|[bag_keys()](bagkeysfunction.md)|Listet alle Stamm Schlüssel in einem dynamischen Eigenschaften Behälter Objekt auf.|
|[bag_merge()](bag-merge-function.md)|Führt dynamische Eigenschaften Behälter in einem dynamischen Eigenschaften Behälter zusammen, wobei alle Eigenschaften zusammengeführt werden.|
|[bag_remove_keys ()](bag-remove-keys-function.md)|Entfernt Schlüssel und zugehörige Werte aus einem dynamischen Eigenschaften Behälter.|
|[pack()](packfunction.md)|Erstellt ein dynamisches Objekt (Eigenschaften Behälter) aus einer Liste von Namen und Werten.|
|[pack_all()](packallfunction.md)|Erstellt ein dynamisches Objekt (Eigenschaften Behälter) aus allen Spalten des tabellarischen Ausdrucks.|
|[pack_array()](packarrayfunction.md)|Packt alle Eingabewerte in ein dynamisches Array.|
|[repeat()](repeatfunction.md)|Generiert ein dynamisches Array, das eine Reihe gleicher Werte enthält.|
|[set_difference()](setdifferencefunction.md)|Gibt ein Array der Menge aller unterschiedlichen Werte zurück, die sich im ersten Array befinden, jedoch nicht in anderen Arrays sind.|
|[set_has_element()](sethaselementfunction.md)|Bestimmt, ob das angegebene Array das angegebene Element enthält.|
|[set_intersect()](setintersectfunction.md)|Gibt ein Array der Menge aller unterschiedlichen Werte zurück, die in allen Arrays vorliegen.|
|[set_union()](setunionfunction.md)|Gibt ein Array der Menge aller unterschiedlichen Werte zurück, die in einem der angegebenen Arrays enthalten sind.|
|[treepath()](treepathfunction.md)|Listet alle Path-Ausdrücke auf, die Verzweigungen in einem dynamischen Objekt identifizieren.|
|[zip()](zipfunction.md)|Die ZIP-Funktion akzeptiert eine beliebige Anzahl dynamischer Arrays. Gibt ein Array zurück, dessen Elemente jeweils ein Array mit den Elementen der Eingabe Arrays desselben Indexes sind.|

## <a name="window-scalar-functions"></a>Skalare Fenster Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[next()](nextfunction.md)|Für den serialisierten Zeilen Satz wird der Wert einer angegebenen Spalte aus der nachfolgenden Zeile entsprechend dem Offset zurückgegeben.|
|[prev()](prevfunction.md)|Für das serialisierte Zeilen Satz gibt den Wert einer angegebenen Spalte aus der früheren Zeile entsprechend dem Offset zurück.|
|[row_cumsum()](rowcumsumfunction.md)|Berechnet die kumulierte Summe einer Spalte.|
|[row_number()](rownumberfunction.md)|Gibt die Nummer einer Zeile in den serialisierten Zeilen Satz-aufeinander folgenden Zahlen zurück, beginnend mit einem angegebenen Index oder standardmäßig von 1.|

## <a name="flow-control-functions"></a>Fluss Steuerungsfunktionen

|Funktionsname            |Beschreibung                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|Gibt einen skalaren Konstanten Wert des ausgewerteten Ausdrucks zurück.|

## <a name="mathematical-functions"></a>Mathematische Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[Abs ()](abs-function.md)|Berechnet den absoluten Wert der Eingabe.|
|[acos()](acosfunction.md)|Gibt den Winkel zurück, dessen Kosinus die angegebene Zahl ist (die umgekehrte Operation von cos ()).|
|[asin()](asinfunction.md)|Gibt den Winkel zurück, dessen Sinus die angegebene Zahl ist (die umgekehrte Operation von Sin ()).|
|[atan()](atanfunction.md)|Gibt den Winkel zurück, dessen Tangens die angegebene Zahl ist (die umgekehrte Operation von Tan ()).|
|[atan2()](atan2function.md)|Berechnet den Winkel im Bogenmaße zwischen der positiven x-Achse und dem Strahl vom Ursprung bis zum Punkt (y, x).|
|[beta_cdf()](beta-cdffunction.md)|Gibt die standardmäßige kumulative Beta Verteilungsfunktion zurück.|
|[beta_inv()](beta-invfunction.md)|Gibt die Umkehrung der kumulativen Beta-Wahrscheinlichkeitsfunktion der Beta-Funktion zurück.|
|[beta_pdf()](beta-pdffunction.md)|Gibt die Beta Funktion der Wahrscheinlichkeitsdichte zurück.|
|[cos()](cosfunction.md)|Gibt die Cosinus-Funktion zurück.|
|[cot()](cotfunction.md)|Berechnet den trigonometrischen Kotangens des angegebenen Winkels im Bogenmaße.|
|[degrees()](degreesfunction.md)|Konvertiert den Winkelwert in Bogenmaß mit Formel Grad = (180/PI) * Winkel-in-Bogenmaß.|
|[exp()](exp-function.md)|Die exponentielle base-e-Funktion von x, die e für Power x: e ^ x ausgelöst wird.|
|[exp10()](exp10-function.md)|Die exponentielle Base-10-Funktion von x, die 10 auf Power x: 10 ^ x fest.|
|[exp2()](exp2-function.md)|Die exponentielle Base-2-Funktion von x, die 2 für Power x: 2 ^ x ist.|
|[gamma()](gammafunction.md)|Berechnet die Gamma Funktion.|
|[isfinite()](isfinitefunction.md)|Gibt zurück, ob die Eingabe ein endlicher Wert (nicht unendlich oder NaN) ist.|
|[isinf()](isinffunction.md)|Gibt zurück, ob die Eingabe ein unendlicher (positiver oder negativer) Wert ist.|
|[isnan()](isnanfunction.md)|Gibt zurück, ob die Eingabe ein NaN-Wert (not-a-Number) ist.|
|[log()](log-function.md)|Gibt die natürliche Logarithmus Funktion zurück.|
|[log10()](log10-function.md)|Gibt die allgemeine Logarithmus Funktion (Basis 10) zurück.|
|[log2()](log2-function.md)|Gibt die Logarithmus Funktion der Basis 2 zurück.|
|[loggamma()](loggammafunction.md)|Berechnet das Protokoll des absoluten Werts der Gamma Funktion.|
|[not()](notfunction.md)|Kehrt den Wert des bool-Arguments um.|
|[pi()](pifunction.md)|Gibt den konstanten Wert von Pi (μ) zurück.|
|[pow()](powfunction.md)|Gibt das Ergebnis der Erhöhung der Stromversorgung zurück.|
|[radians()](radiansfunction.md)|Konvertiert den Winkelwert in Grad in den Wert im Bogenmaß mit Formel Bogenmaß = (PI/180) * Winkel in Grad.|
|[rand()](randfunction.md)|Gibt eine Zufallszahl zurück.|
|[Bereich ()](rangefunction.md)|Generiert ein dynamisches Array, das eine Reihe von gleichmäßig getrennten Werten enthält.|
|[round()](roundfunction.md)|Gibt die abgerundete Quelle auf die angegebene Genauigkeit zurück.|
|[Sign ()](signfunction.md)|Zeichen eines numerischen Ausdrucks.|
|[sin()](sinfunction.md)|Gibt die Sinusfunktion zurück.|
|[sqrt()](sqrtfunction.md)|Gibt die Quadratwurzel Funktion zurück.|
|[tan()](tanfunction.md)|Gibt die Tangens Funktion zurück.|
|[welch_test()](welch-testfunction.md)|Berechnet den p-Wert der [Welch-Test-Funktion](https://en.wikipedia.org/wiki/Welch%27s_t-test).|

## <a name="metadata-functions"></a>Metadatenfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|Nimmt einen Spaltennamen als Zeichenfolge und einen Standardwert an. Gibt einen Verweis auf die Spalte zurück, wenn Sie vorhanden ist, andernfalls wird der Standardwert zurückgegeben.|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|Gibt den aktuellen Cluster zurück, der die Abfrage ausgeführt hat.|
|[current_database()](current-database-function.md)|Gibt den Namen der Datenbank im Bereich zurück.|
|[current_principal()](current-principalfunction.md)|Gibt den aktuellen Prinzipal zurück, der diese Abfrage ausführen.|
|[current_principal_details()](current-principal-detailsfunction.md)|Gibt Details des Prinzipals zurück, der die Abfrage ausgeführt hat.|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|Überprüft die Gruppenmitgliedschaft oder die Prinzipal Identität des aktuellen Prinzipals, der die Abfrage ausführt.|
|[cursor_after()](cursorafterfunction.md)|Wird für den Zugriff auf die Datensätze verwendet, die nach dem vorherigen Wert des Cursors erfasst wurden.|
|[estimate_data_size()](estimate-data-sizefunction.md)|Gibt eine geschätzte Datengröße der ausgewählten Spalten des tabellarischen Ausdrucks zurück.|
|[extent_id()](extentidfunction.md)|Gibt einen eindeutigen Bezeichner zurück, der den datenshard ("Block") angibt, in dem sich der aktuelle Datensatz befindet.|
|[extent_tags()](extenttagsfunction.md)|Gibt ein dynamisches Array mit den Tags des datenshards ("Block") zurück, in dem sich der aktuelle Datensatz befindet.|
|[ingestion_time()](ingestiontimefunction.md)|Ruft die $IngestionTime verborgene datetime-Spalte des Datensatzes oder NULL ab.|

## <a name="rounding-functions"></a>Rundungs Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist.|
|[bin_at()](binatfunction.md)|Rundet Werte auf einen "bin" mit fester Größe, wobei die Steuerung über dem Ausgangspunkt der bin gesteuert wird. (Siehe auch bin-Funktion.)|
|[ceiling()](ceilingfunction.md)|Berechnet die kleinste Ganzzahl, die größer oder gleich dem angegebenen numerischen Ausdruck ist.|
|[floor()](floorfunction.md)|Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist.|

## <a name="conditional-functions"></a>Bedingte Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[case()](casefunction.md)|Wertet eine Liste von Prädikaten aus und gibt den ersten Ergebnis Ausdruck zurück, dessen Prädikat erfüllt ist.|
|[coalesce()](coalescefunction.md)|Wertet eine Liste von Ausdrücken aus und gibt den ersten Ausdruck zurück, der nicht NULL ist (oder nicht leer für eine Zeichenfolge).|
|[IIf ()/IFF ()](iiffunction.md)|Wertet das erste Argument (das Prädikat) aus und gibt den Wert des zweiten oder dritten Arguments zurück, abhängig davon, ob das Prädikat als true (Second) oder false (dritte) ausgewertet wurde.|
|[max_of()](max-offunction.md)|Gibt den maximalen Wert von mehreren ausgewerteten numerischen Ausdrücken zurück.|
|[min_of()](min-offunction.md)|Gibt den minimalen Wert von mehreren ausgewerteten numerischen Ausdrücken zurück.|

## <a name="series-element-wise-functions"></a>Reihen Element Weise Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|Berechnet die Element Weise Addition von zwei numerischen Reihen Eingaben.|
|[series_divide()](series-dividefunction.md)|Berechnet die Element Weise Division von zwei numerischen Reihen Eingaben.|
|[series_equals()](series-equalsfunction.md)|Berechnet die Element Weise Gleichheits `==` logische Operation von zwei numerischen Reihen Eingaben.|
|[series_greater()](series-greaterfunction.md)|Berechnet die Element Weise größere ( `>` ) Logik Operation von zwei numerischen Reihen Eingaben.|
|[series_greater_equals()](series-greater-equalsfunction.md)|Berechnet die Element Weise größere oder Gleichheits `>=` logische Operation von zwei numerischen Reihen Eingaben.|
|[series_less()](series-lessfunction.md)|Berechnet die Element Weise Less ( `<` )-Logik Operation von zwei numerischen Reihen Eingaben.|
|[series_less_equals()](series-less-equalsfunction.md)|Berechnet die Element Weise weniger oder gleichmäßige `<=` Logik Operation von zwei numerischen Reihen Eingaben.|
|[series_multiply()](series-multiplyfunction.md)|Berechnet die Element Weise Multiplikation von zwei numerischen Reihen Eingaben.|
|[series_not_equals()](series-not-equalsfunction.md)|Berechnet die Element Weise not Gleichheits ( `!=` )-Logik Operation von zwei numerischen Reihen Eingaben.|
|[series_subtract()](series-subtractfunction.md)|Berechnet die Element Weise Subtraktion von zwei numerischen Reihen Eingaben.|

## <a name="series-processing-functions"></a>Reihen Verarbeitungsfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|Führt eine Zerlegung der Reihe in-Komponenten durch.|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|Findet Anomalien in einer Reihe, die auf der Reihen Zerlegung basiert.|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|Vorhersage basierend auf der Reihen Zerlegung.|
|[series_fill_backward()](series-fill-backwardfunction.md)|Führt eine rückwärts Füll interpolung von fehlenden Werten in einer Reihe aus.|
|[series_fill_const()](series-fill-constfunction.md)|Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert.|
|[series_fill_forward()](series-fill-forwardfunction.md)|Führt vorwärts Füll Interpolationen von fehlenden Werten in einer Reihe aus.|
|[series_fill_linear()](series-fill-linearfunction.md)|Führt eine lineare interpolung von fehlenden Werten in einer Reihe aus.|
|[series_fft()](series-fft-function.md)|Wendet die schnelle Fourier-Transformation (FFT) auf eine Reihe an.|
|[series_fir()](series-firfunction.md)|Wendet einen endlichen Impuls Antwort Filter auf eine Reihe an.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Wendet die lineare Regression zweier Segmente auf eine Reihe an und gibt mehrere Spalten zurück.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Wendet die lineare Regression zweier Segmente auf eine Reihe an und gibt ein dynamisches Objekt zurück.|
|[series_fit_line()](series-fit-linefunction.md)|Wendet die lineare Regression für eine Reihe an und gibt mehrere Spalten zurück.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Wendet die lineare Regression für eine Reihe an und gibt ein dynamisches Objekt zurück.|
|[series_ifft()](series-ifft-function.md)|Wendet die umgekehrte schnelle Fourier-Transformation (IFFT) für eine Reihe an.|
|[series_iir()](series-iirfunction.md)|Wendet einen unendlichen Impuls Antwort Filter auf eine Reihe an.|
|[series_outliers()](series-outliersfunction.md)|Bewertet anomaliepunkte in einer Reihe.|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|Berechnet den Pearson-Korrelationskoeffizienten Zweierreihen.|
|[series_periods_detect()](series-periods-detectfunction.md)|Sucht die signifikantesten Zeiträume, die in einer Zeitreihe vorhanden sind.|
|[series_periods_validate()](series-periods-validatefunction.md)|Überprüft, ob eine Zeitreihe periodische Muster mit angegebenen Längen enthält.|
|[series_seasonal()](series-seasonalfunction.md)|Sucht die Saison Komponente der Reihe.|
|[series_stats()](series-statsfunction.md)|Gibt Statistiken für eine Reihe in mehreren Spalten zurück.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Gibt Statistiken für eine Reihe im dynamischen Objekt zurück.|

## <a name="string-functions"></a>Zeichenfolgenfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|Codiert eine Zeichenfolge als base64-Zeichenfolge.|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|Decodiert eine base64-Zeichenfolge in eine UTF-8-Zeichenfolge.|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|Decodiert eine base64-Zeichenfolge in ein Array von Long-Werten.|
|[count ()](countoffunction.md)|Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Einfache Zeichen folgen Übereinstimmungen können sich überlappen die Regex-Übereinstimmungen sind nicht.|
|[extract()](extractfunction.md)|Ruft eine Übereinstimmung für einen regulären Ausdruck aus einer Textzeichenfolge ab.|
|[extract_all()](extractallfunction.md)|Alle Übereinstimmungen für einen regulären Ausdruck aus einer Text Zeichenfolge erhalten.|
|[extractjson()](extractjsonfunction.md)|Rufen Sie ein angegebenes Element aus einem JSON-Text mit einem Pfadausdruck ab.|
|[indexof()](indexoffunction.md)|Die Funktion meldet den NULL basierten Index des ersten Vorkommens einer angegebenen Zeichenfolge in der Eingabe Zeichenfolge.|
|[isempty()](isemptyfunction.md)|Gibt "true" zurück, wenn das Argument eine leere Zeichenfolge oder NULL ist.|
|[isnotempty()](isnotemptyfunction.md)|Gibt true zurück, wenn das Argument keine leere Zeichenfolge oder NULL ist.|
|[isnotnull()](isnotnullfunction.md)|Gibt "true" zurück, wenn das Argument nicht NULL ist.|
|[isnull()](isnullfunction.md)|Wertet das einzige Argument aus und gibt einen booleschen Wert zurück, der angibt, ob das Argument zu einem NULL-Wert ausgewertet wird.|
|[parse_command_line()](parse-command-line.md)|Analysiert eine Unicode-Befehlszeilen Zeichenfolge und gibt ein Array mit den Befehlszeilen Argumenten zurück.|
|[parse_csv()](parsecsvfunction.md)|Teilt eine angegebene Zeichenfolge, die durch Trennzeichen getrennte Werte darstellt, und gibt ein Zeichen folgen Array mit diesen Werten zurück.|
|[parse_ipv4()](parse-ipv4function.md)|Konvertiert Eingaben in eine Long-Darstellung (signed 64-Bit).|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|Konvertiert die Eingabe Zeichenfolge und die IP-Präfix Maske in eine Long-Darstellung (signed 64-Bit).|
|[parse_ipv6()](parse-ipv6function.md)|Konvertiert IPv6-oder IPv4-Zeichen folgen in eine kanonische IPv6-Zeichen folgen Darstellung.|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|Konvertiert IPv6-oder IPv4-Zeichen folgen und Netzmaske in eine kanonische IPv6-Zeichen folgen Darstellung.|
|[parse_json ()](parsejsonfunction.md)|Interpretiert eine Zeichenfolge als JSON-Wert und gibt den Wert als Dynamic zurück.|
|[parse_url()](parseurlfunction.md)|Analysiert eine absolute URL Zeichenfolge und gibt ein dynamisches Objekt zurück, das alle Teile der URL enthält.|
|[parse_urlquery()](parseurlqueryfunction.md)|Analysiert eine URL-Abfrage Zeichenfolge und gibt ein dynamisches Objekt zurück, das die Abfrage Parameter enthält.|
|[parse_version()](parse-versionfunction.md)|Konvertiert die Darstellung der Eingabe Zeichenfolge in eine vergleichbare Dezimalzahl.|
|[Replace ()](replacefunction.md)|Ersetzen Sie alle regex-Übereinstimmungen mit einer anderen Zeichenfolge.|
|[reverse()](reversefunction.md)|Die Funktion bewirkt eine Umkehrung der Eingabe Zeichenfolge.|
|[split()](splitfunction.md)|Teilt eine angegebene Zeichenfolge gemäß einem angegebenen Trennzeichen und gibt ein Zeichen folgen Array mit den enthaltenen Teil Zeichenfolgen zurück.|
|[strcat()](strcatfunction.md)|Verkettet zwischen 1 und 64 Argumenten.|
|[strcat_delim()](strcat-delimfunction.md)|Verkettet zwischen zwei und 64 Argumenten mit Trennzeichen, die als erstes Argument bereitgestellt werden.|
|[strcmp()](strcmpfunction.md)|Vergleicht zwei Zeichenfolgen.|
|[strlen()](strlenfunction.md)|Gibt die Länge der Eingabe Zeichenfolge in Zeichen zurück.|
|[strrep()](strrepfunction.md)|Wiederholt die angegebene Zeichenfolge mehrmals (Standardwert: 1).|
|[substring()](substringfunction.md)|Extrahiert eine Teil Zeichenfolge aus einer Quell Zeichenfolge beginnend mit einem Index bis zum Ende der Zeichenfolge.|
|[toupper()](toupperfunction.md)|Konvertiert eine Zeichenfolge in Großbuchstaben.|
|[translate()](translatefunction.md)|Ersetzt einen Zeichensatz ("SEARCHLIST") durch einen anderen Zeichensatz ("REPLACEMENTLIST") in einer angegebenen Zeichenfolge.|
|[Trim ()](trimfunction.md)|Entfernt alle führenden und nachfolgenden Übereinstimmungen des angegebenen regulären Ausdrucks.|
|[trim_end()](trimendfunction.md)|Entfernt die nachfolgende Entsprechung des angegebenen regulären Ausdrucks.|
|[trim_start()](trimstartfunction.md)|Entfernt die führende Entsprechung des angegebenen regulären Ausdrucks.|
|[url_decode()](urldecodefunction.md)|Die-Funktion konvertiert die codierte URL in eine reguläre URL-Darstellung.|
|[url_encode()](urlencodefunction.md)|Die-Funktion konvertiert Zeichen der Eingabe-URL in ein Format, das über das Internet übermittelt werden kann.|

## <a name="ipv4ipv6-functions"></a>IPv4/IPv6-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|Vergleicht zwei IPv4-Zeichen folgen.|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|Entspricht zwei IPv4-Zeichen folgen.|
|[parse_ipv4()](parse-ipv4function.md)|Konvertiert die Eingabe Zeichenfolge in eine Long-Darstellung (signed 64-Bit).|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|Konvertiert die Eingabe Zeichenfolge und die IP-Präfix Maske in eine Long-Darstellung (signed 64-Bit).|
|[ipv6_compare()](ipv6-comparefunction.md)|Vergleicht zwei IPv4-oder IPv6-Zeichen folgen.|
|[ipv6_is_match()](ipv6-is-matchfunction.md)|Entspricht zwei IPv4-oder IPv6-Zeichen folgen.|
|[parse_ipv6()](parse-ipv6function.md)|Konvertiert IPv6-oder IPv4-Zeichen folgen in eine kanonische IPv6-Zeichen folgen Darstellung.|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|Konvertiert IPv6-oder IPv4-Zeichen folgen und Netzmaske in eine kanonische IPv6-Zeichen folgen Darstellung.|
|[format_ipv4()](format-ipv4-function.md)|Analysiert Eingaben mit einer Netzmaske und gibt eine Zeichenfolge zurück, die die IPv4-Adresse darstellt.|
|[format_ipv4_mask()](format-ipv4-mask-function.md)|Analysiert Eingaben mit einer Netzmaske und gibt eine Zeichenfolge zurück, die die IPv4-Adresse als CIDR-Notation darstellt.|

## <a name="type-functions"></a>Typfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|Gibt den Lauf Zeittyp des einzelnen Arguments zurück.|

## <a name="scalar-aggregation-functions"></a>Skalare Aggregations Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|Berechnet die DCount aus HLL-Ergebnissen (die von HLL oder HLL-Merge generiert wurden).|
|[hll_merge()](hllmergefunction.md)|Führt HLL-Ergebnisse aus (skalare Version der Aggregat Version HLL-Merge ()).|
|[percentile_tdigest()](percentile-tdigestfunction.md)|Berechnet das Perzentil-Ergebnis aus tdigest-Ergebnissen (das von tdigest oder Merge-tdigests generiert wurde).|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|Berechnet den Prozentsatz eines Werts in einem DataSet.|
|[rank_tdigest()](rank-tdigest.md)|Berechnet den relativen Rang eines Werts in einer Menge.|
|[tdigest_merge()](tdigest-mergefunction.md)|Führt tdigest-Ergebnisse aus (skalare Version der Aggregat Version tdigest-Merge ()).|

## <a name="geospatial-functions"></a>Geofunktionen

|Funktionsname|Beschreibung|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|Berechnet den kürzesten Abstand zwischen zwei geografischen Koordinaten auf der Erde.|
|[geo_distance_point_to_line()](geo-distance-point-to-line-function.md)|Berechnet den kürzesten Abstand zwischen einer Koordinate und einer Linie auf der Erde.|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|Berechnet, ob die georäumlichen Koordinaten sich innerhalb eines Kreises auf der Erde befinden.|
|[geo_point_in_polygon()](geo-point-in-polygon-function.md)|Berechnet, ob sich die geografischen Koordinaten innerhalb eines Polygons oder eines multipolygons auf der Erde befinden.|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|Berechnet den GeoHash-Zeichen folgen Wert für einen geografischen Standort.|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|Berechnet die räumlichen Koordinaten, die den Mittelpunkt eines GeoHash-rechteckigen Bereichs darstellen.|
|[geo_point_to_s2cell()](geo-point-to-s2cell-function.md)|Berechnet den S2-zelltokenzeichenfolgen-Wert für einen geografischen Standort.|
|[geo_s2cell_to_central_point()](geo-s2cell-to-central-point-function.md)|Berechnet die räumlichen Koordinaten, die den Mittelpunkt einer S2-Zelle darstellen.|
|[geo_polygon_to_s2cells()](geo-polygon-to-s2cells-function.md)|Berechnet S2-zelltokens, die ein Polygon oder MultiPolygon auf der Erde abdecken. Nützliches Georäumliches jointool.|
|[geo_line_densify()](geo-line-densify-function.md)|Konvertiert planare Zeilen Kanten durch Hinzufügen von zwischen Punkten in geodäk.|
|[geo_polygon_densify()](geo-polygon-densify-function.md)|Konvertiert Polygon-oder MultiPolygon-planare Ränder in geodäme durch Hinzufügen von zwischen Punkten.|

## <a name="hash-functions"></a>Hash Funktionen

|Funktionsname|Beschreibung|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[Hash ()](hashfunction.md)|Gibt einen Hashwert für den Eingabe Wert zurück.|
|[hash_combine()](hash_combinefunction.md)|Kombiniert zwei oder mehr Hashwerte.|
|[hash_many()](hash_manyfunction.md)|Gibt einen kombinierten Hashwert mit mehreren Werten zurück.|
|[hash_md5()](md5hashfunction.md)|Gibt einen MD5-Hashwert für den Eingabe Wert zurück.|
|[hash_sha256()](sha256hashfunction.md)|Gibt einen SHA256-Hashwert für den Eingabe Wert zurück.|
