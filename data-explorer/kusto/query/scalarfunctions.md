---
title: Skalarfunktionen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Scalar-Funktionen in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: ef807c93f9ae9b125439f55f32a2bb1eb21d46b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509553"
---
# <a name="scalar-functions"></a>Skalarfunktionen

## <a name="binary-functions"></a>Binäre Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|Gibt ein Ergebnis des Bitwise und der Operation zwischen zwei Werten zurück.|
|[binary_not()](binary-notfunction.md)|Gibt eine bitweise Negation des Eingabewerts zurück.|
|[binary_or()](binary-orfunction.md)|Gibt ein Ergebnis des Bitwise oder der Operation der beiden Werte zurück.|
|[binary_shift_left()](binary-shift-leftfunction.md)|Gibt binäre Verschiebung links Operation auf einem Paar von Zahlen: ein << n.|
|[binary_shift_right()](binary-shift-rightfunction.md)|Gibt binäre Verschiebung rechts Operation auf einem Paar von Zahlen: ein >> n.|
|[binary_xor()](binary-xorfunction.md)|Gibt ein Ergebnis der bitweisen xor-Operation der beiden Werte zurück.|
|[bitset_count_ones()](bitset-count-onesfunction.md)|Gibt die Anzahl der gesetzten Bits in der binären Darstellung einer Zahl zurück.|

## <a name="conversion-functions"></a>Konvertierungsfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|Konvertiert Die Eingabe in eine boolesche (signierte 8-Bit) Darstellung.|
|[todatetime()](todatetimefunction.md)|Konvertiert Die Eingabe in datetime skalar.|
|[todouble()/toreal()](todoublefunction.md)|Konvertiert die Eingabe in einen Wert vom Typ real. (todouble() und toreal() sind Synonyme.)|
|[tostring()](tostringfunction.md)|Konvertiert Die Eingabe in eine Zeichenfolgendarstellung.|
|[totimespan()](totimespanfunction.md)|Konvertiert Die Eingabe in Zeitspanskala skalar.|


## <a name="datetimetimespan-functions"></a>DateTime/Timespan-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|Subtrahiert den angegebenen Zeitraum von der aktuellen UTC-Uhrzeit.|
|[datetime_add()](datetime-addfunction.md)|Berechnet eine neue Datumsuhr aus einem angegebenen Datumsteil multipliziert mit einem angegebenen Betrag, der zu einer angegebenen Datumszeit hinzugefügt wird.|
|[datetime_part()](datetime-partfunction.md)|Extrahiert den angeforderten Datumsteil als Ganzzahlwert.|
|[datetime_diff()](datetime-difffunction.md)|Gibt das Ende des Jahres zurück, das das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[dayofmonth()](dayofmonthfunction.md)|Gibt die ganze Zahl zurück, die die Tagesnummer des angegebenen Monats darstellt.|
|[dayofweek()](dayofweekfunction.md)|Gibt die ganze Zahl der Tage seit dem vorherigen Sonntag als Zeitraum zurück.|
|[tagofyear()](dayofyearfunction.md)|Gibt die ganze Zahl an, die die Tagesnummer des angegebenen Jahres darstellt.|
|[endofday()](endofdayfunction.md)|Gibt das Ende des Tages zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[endofmonth()](endofmonthfunction.md)|Gibt das Ende des Monats zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[endofweek()](endofweekfunction.md)|Gibt das Ende der Woche zurück, in der das Datum enthalten ist, verschoben um einen Offset, sofern angegeben.|
|[endofyear()](endofyearfunction.md)|Gibt das Ende des Jahres zurück, das das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[format_datetime()](format-datetimefunction.md)|Formatiert einen Datetime-Parameter basierend auf dem Formatmusterparameter.|
|[format_timespan()](format-timespanfunction.md)|Formatiert einen Parameter format-timespan basierend auf dem Formatmusterparameter.|
|[getmonth()](getmonthfunction.md)|Rufen Sie die Monatsnummer (1-12) aus einem datetime-Wert ab.|
|[getyear()](getyearfunction.md)|Gibt den Jahresteil des datetime-Arguments zurück.|
|[hourofday()](hourofdayfunction.md)|Gibt die ganze Zahl zurück, die die Stundennummer des angegebenen Datums darstellt.|
|[make_datetime()](make-datetimefunction.md)|Erstellt einen Datumsskalawert für Datumszeiten aus dem angegebenen Datum und der angegebenen Uhrzeit.|
|[make_timespan()](make-timespanfunction.md)|Erstellt einen Skalarwert für die Zeitspanne aus dem angegebenen Zeitraum.|
|[monthofyear()](monthofyearfunction.md)|Gibt die ganze Zahl an, die die Monatsnummer des angegebenen Jahres darstellt.|
|[now()](nowfunction.md)|Gibt die aktuelle UTC-Uhrzeit zurück, die optional durch eine bestimmte Zeitspanne ausgeglichen wird.|
|[startofday()](startofdayfunction.md)|Gibt den Anfang des Tages zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[Anfang des Monats()](startofmonthfunction.md)|Gibt den Anfang des Monats zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[Startofweek()](startofweekfunction.md)|Gibt den Anfang der Woche zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[startofyear()](startofyearfunction.md)|Gibt den Anfang des Jahres zurück, das das Datum enthält, verschoben um einen Offset, sofern angegeben.|
|[todatetime()](todatetimefunction.md)|Konvertiert Die Eingabe in datetime skalar.|
|[totimespan()](totimespanfunction.md)|Konvertiert Die Eingabe in Zeitspanskala skalar.|
|[weekofyear()](weekofyearfunction.md)|Gibt eine ganze Zahl zurück, die die Wochennummer darstellt.|


## <a name="dynamicarray-functions"></a>Dynamische/Array-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|Verkettet eine Reihe dynamischer Arrays mit einem einzelnen Array.|
|[array_iif()](arrayifffunction.md)|Wendet die elementweisen iif-Funktion auf Arrays an.|
|[array_index_of()](arrayindexoffunction.md)|Durchsucht das Array nach dem angegebenen Element und gibt dessen Position zurück.|
|[array_length()](arraylengthfunction.md)|Berechnet die Anzahl der Elemente in einem dynamischen Array.|
|[array_slice()](arrayslicefunction.md)|Extrahiert ein Segment eines dynamischen Arrays.|
|[array_split()](arraysplitfunction.md)|Erstellt ein Array von Arrays, die vom Eingabearray geteilt sind.|
|[bag_keys()](bagkeysfunction.md)|Zählt alle Stammschlüssel in einem dynamischen Property-Bag-Objekt auf.|
|[Pack()](packfunction.md)|Erstellt ein dynamisches Objekt (Eigenschaftsbeutel) aus einer Liste von Namen und Werten.|
|[pack_all()](packallfunction.md)|Erstellt ein dynamisches Objekt (Eigenschaftsbeutel) aus allen Spalten des tabellenförmigen Ausdrucks.|
|[pack_array()](packarrayfunction.md)|Packt alle Eingabewerte in ein dynamisches Array.|
|[wiederholen()](repeatfunction.md)|Generiert ein dynamisches Array mit einer Reihe gleicher Werte.|
|[set_difference()](setdifferencefunction.md)|Gibt ein Array des Satzes aller unterschiedlichen Werte zurück, die sich im ersten Array befinden, sich aber nicht in anderen Arrays befinden.|
|[set_has_element()](sethaselementfunction.md)|Bestimmt, ob das angegebene Array das angegebene Element enthält.|
|[set_intersect()](setintersectfunction.md)|Gibt ein Array des Satzes aller unterschiedlichen Werte zurück, die sich in allen Arrays befinden.|
|[set_union()](setunionfunction.md)|Gibt ein Array des Satzes aller unterschiedlichen Werte zurück, die sich in einem der bereitgestellten Arrays befinden.|
|[treepath()](treepathfunction.md)|Listet alle Path-Ausdrücke auf, die Verzweigungen in einem dynamischen Objekt identifizieren.|
|[reißverschluss()](zipfunction.md)|Die ZIP-Funktion akzeptiert eine beliebige Anzahl dynamischer Arrays und gibt ein Array zurück, dessen Elemente jeweils ein Array sind, das die Elemente der Eingabearrays desselben Indexes enthält.|


## <a name="window-scalar-functions"></a>Fenster-Skalar-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[next()](nextfunction.md)|Gibt für den serialisierten Zeilensatz den Wert einer angegebenen Spalte aus der späteren Zeile entsprechend dem Offset zurück.|
|[prev()](prevfunction.md)|Gibt für den serialisierten Zeilensatz den Wert einer angegebenen Spalte aus der vorherigen Zeile entsprechend dem Offset zurück.|
|[row_cumsum()](rowcumsumfunction.md)|Berechnet die kumulative Summe einer Spalte.|
|[row_number()](rownumberfunction.md)|Gibt die Nummer einer Zeile im serialisierten Zeilensatz zurück - fortlaufende Zahlen, die von einem bestimmten Index oder standardmäßig von 1 beginnen.|

## <a name="flow-control-functions"></a>Flow Control-Funktionen

|Funktionsname            |Beschreibung                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|Gibt einen skalaren konstanten Wert des ausgewerteten Ausdrucks zurück.|

## <a name="mathematical-functions"></a>Mathematische Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[abs()](abs-function.md)|Berechnet den absoluten Wert der Eingabe.|
|[acos()](acosfunction.md)|Gibt den Winkel zurück, dessen Kosinus die angegebene Zahl ist (die inverse Operation von cos()).|
|[asin()](asinfunction.md)|Gibt den Winkel zurück, dessen Sinus die angegebene Zahl ist (die inverse Operation von sin()).|
|[atan()](atanfunction.md)|Gibt den Winkel zurück, dessen Tangente die angegebene Zahl ist (die inverse Operation von tan()).|
|[atan2()](atan2function.md)|Berechnet den Winkel in Bogenmaßen zwischen der positiven x-Achse und dem Strahl vom Ursprung bis zum Punkt (y, x).|
|[beta_cdf()](beta-cdffunction.md)|Gibt die standardmäßige kumulative Betaverteilungsfunktion zurück.|
|[beta_inv()](beta-invfunction.md)|Gibt die Umkehrung der Beta-Funktion für kumulative Wahrscheinlichkeit beta-Dichte zurück.|
|[beta_pdf()](beta-pdffunction.md)|Gibt die Betafunktion der Wahrscheinlichkeitsdichte zurück.|
|[cos()](cosfunction.md)|Gibt die Kosinusfunktion zurück.|
|[Kinderbett()](cotfunction.md)|Berechnet die trigonometrische Kotangung des angegebenen Winkels in Bogenmaß.|
|[Grad()](degreesfunction.md)|Konvertiert den Winkelwert in Bogenmaß in Wert in Grad, mit Formel Grad = (180 / PI) * Winkel-in-Radians.|
|[exp()](exp-function.md)|Die base-e exponentielle Funktion von x, die auf die Leistung x: ex erhöht wird.|
|[exp10()](exp10-function.md)|Die Base-10-Exponentialfunktion von x, die 10 auf die Leistung x: 10x erhöht wird.|
|[exp2()](exp2-function.md)|Die base-2-Exponentialfunktion von x, die 2 auf die Leistung x: 2x erhöht ist.|
|[gamma()](gammafunction.md)|Berechnet die Gammafunktion.|
|[hash()](hashfunction.md)|Gibt einen Hashwert für den Eingabewert zurück.|
|[hash_combine()](hash_combinefunction.md)|Kombiniert zwei oder mehr Hashwerte.|
|[hash_many()](hash_manyfunction.md)|Gibt einen kombinierten Hashwert mehrerer Werte zurück.|
|[isfinite()](isfinitefunction.md)|Gibt zurück, ob die Eingabe ein endlicher Wert ist (ist weder unendlich noch NaN).|
|[isinf()](isinffunction.md)|Gibt zurück, ob die Eingabe ein unendlicher (positiver oder negativer) Wert ist.|
|[isnan()](isnanfunction.md)|Gibt zurück, ob die Eingabe Not-a-Number (NaN)-Wert ist.|
|[log()](log-function.md)|Gibt die natürliche Logarithmusfunktion zurück.|
|[log10()](log10-function.md)|Retuns die comon (base-10) Logarithmus-Funktion.|
|[log2()](log2-function.md)|Gibt die Base-2-Logarithmusfunktion zurück.|
|[loggamma()](loggammafunction.md)|Berechnet das Protokoll des absoluten Wertes der Gammafunktion.|
|[nicht()](notfunction.md)|Kehrt den Wert des bool-Arguments um.|
|[pi()](pifunction.md)|Gibt den konstanten Wert von Pi () zurück.|
|[pow()](powfunction.md)|Gibt ein Ergebnis der Erhöhung an die Macht zurück.|
|[Bogenmaß()](radiansfunction.md)|Konvertiert den Winkelwert in Grad in Wert in Bogenmaß, mit Formel-Bogenians = (PI / 180 ) * Winkel-in-Grad.|
|[rand()](randfunction.md)|Gibt eine Zufallszahl zurück.|
|[Bereich()](rangefunction.md)|Generiert ein dynamisches Array mit einer Reihe gleich verteilter Werte.|
|[rund()](roundfunction.md)|Gibt die abgerundete Quelle auf die angegebene Genauigkeit zurück.|
|[sign()](signfunction.md)|Vorzeichen eines numerischen Ausdrucks.|
|[sin()](sinfunction.md)|Gibt die Sinusfunktion zurück.|
|[sqrt()](sqrtfunction.md)|Gibt die Quadratwurzelfunktion zurück.|
|[tan()](tanfunction.md)|Gibt die Tangentenfunktion zurück.|
|[welch_test()](welch-testfunction.md)|Berechnet den p-Wert der [Welch-Testfunktion](https://en.wikipedia.org/wiki/Welch%27s_t-test).|


## <a name="metadata-functions"></a>Metadatenfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|Verwendet einen Spaltennamen als Zeichenfolge und einen Standardwert. Gibt einen Verweis auf die Spalte zurück, falls vorhanden, andernfalls - gibt den Standardwert zurück.|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|Gibt den aktuellen Cluster zurück, der die Abfrage ausführt.|
|[current_database()](current-database-function.md)|Gibt den Namen der Datenbank im Gültigkeitsbereich zurück.|
|[current_principal()](current-principalfunction.md)|Gibt den aktuellen Prinzipal zurück, der diese Abfrage ausführt.|
|[current_principal_details()](current-principal-detailsfunction.md)|Gibt Details des Prinzipals zurück, der die Abfrage ausführt.|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|Überprüft die Gruppenmitgliedschaft oder die Hauptidentität des aktuellen Prinzipals, der die Abfrage ausführt.|
|[cursor_after()](cursorafterfunction.md)|Wird verwendet, um auf die Datensätze zuzugreifen, die nach dem vorherigen Wert des Cursors aufgenommen wurden.|
|[estimate_data_size()](estimate-data-sizefunction.md)|Gibt eine geschätzte Datengröße der ausgewählten Spalten des Tabellenausdrucks zurück.|
|[extent_id()](extentidfunction.md)|Gibt einen eindeutigen Bezeichner zurück, der den Daten-Shard ("Extent") identifiziert, in dem sich der aktuelle Datensatz befindet.|
|[extent_tags()](extenttagsfunction.md)|Gibt ein dynamisches Array mit den Tags des Daten-Shards ("Extent") zurück, in dem sich der aktuelle Datensatz befindet.|
|[ingestion_time()](ingestiontimefunction.md)|Ruft die $IngestionTime ausgeblendeten Datetime-Spalte des Datensatzes oder NULL ab.|


## <a name="rounding-functions"></a>Rundungsfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist.|
|[bin_at()](binatfunction.md)|Rundet Werte auf einen "Bin" fester Größe mit der Kontrolle über den Startpunkt des Lagerplatzes. (Siehe auch bin-Funktion.)|
|[Decke()](ceilingfunction.md)|Berechnet die kleinste ganze Zahl größer oder gleich dem angegebenen numerischen Ausdruck.|
|[Boden()](floorfunction.md)|Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist.|


## <a name="conditional-functions"></a>Bedingte Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[Fall()](casefunction.md)|Bewertet eine Liste von Prädikaten und gibt den ersten Ergebnisausdruck zurück, dessen Prädikat erfüllt ist.|
|[koalesce()](coalescefunction.md)|Wertet eine Liste von Ausdrücken aus und gibt den ersten Ausdruck nicht NULL (oder nicht leer für Zeichenfolgen) zurück.|
|[iif()/iff()](iiffunction.md)|Bewertet das erste Argument (das Prädikat) und gibt den Wert des zweiten oder dritten Arguments zurück, je nachdem, ob das Prädikat als true (zweites) oder false (drittes) ausgewertet wurde.|
|[max_of()](max-offunction.md)|Gibt den Maximalwert mehrerer ausgewerteter numerischer Ausdrücke zurück.|
|[min_of()](min-offunction.md)|Gibt den Mindestwert mehrerer ausgewerteter numerischer Ausdrücke zurück.|

## <a name="series-element-wise-functions"></a>Serie Element-weise Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|Berechnet die elementmäßige Hinzufügung von zwei numerischen Reiheneingaben.|
|[series_divide()](series-dividefunction.md)|Berechnet die elementweise Aufteilung zweier numerischer Reiheneingaben.|
|[series_equals()](series-equalsfunction.md)|Berechnet die elementmäßige Gleichheitsoperation (`==`) von zwei numerischen Reiheneingaben.|
|[series_greater()](series-greaterfunction.md)|Berechnet die elementmäßig größere`>`( ) Logikoperation von zwei numerischen Reiheneingaben.|
|[series_greater_equals()](series-greater-equalsfunction.md)|Berechnet die elementmäßig eisern`>=`größere oder gleiche ( ) Logikoperation von zwei numerischen Reiheneingaben.|
|[series_less()](series-lessfunction.md)|Berechnet die elementmäßig weniger`<`( ) Logikoperation von zwei numerischen Reiheneingaben.|
|[series_less_equals()](series-less-equalsfunction.md)|Berechnet die elementmäßige Logikoperation`<=`von zwei numerischen Reiheneingaben.|
|[series_multiply()](series-multiplyfunction.md)|Berechnet die elementbezogene Multiplikation von zwei numerischen Reiheneingaben.|
|[series_not_equals()](series-not-equalsfunction.md)|Berechnet die elementmäßige Nicht-Gleich- (`!=`) Logikoperation von zwei numerischen Reiheneingaben.|
|[series_subtract()](series-subtractfunction.md)|Berechnet die elementbezogene Subtraktion von zwei numerischen Reiheneingaben.|

## <a name="series-processing-functions"></a>Verarbeitungsfunktionen der Serie

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|Führt eine Zerlegung der Serie in Komponenten aus.|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|Findet Anomalien in einer Serie, die auf der Zersetzung von Serien basiert.|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|Prognose basierend auf Serienzerlegung.|
|[series_fill_backward()](series-fill-backwardfunction.md)|Führt die Rückwärtsfüllinterpolation fehlender Werte in einer Reihe aus.|
|[series_fill_const()](series-fill-constfunction.md)|Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert.|
|[series_fill_forward()](series-fill-forwardfunction.md)|Führt die Vorwärtsfüllinterpolation fehlender Werte in einer Reihe durch.|
|[series_fill_linear()](series-fill-linearfunction.md)|Führt eine lineare Interpolation fehlender Werte in einer Reihe durch.|
|[series_fir()](series-firfunction.md)|Wendet einen Finite Impulse Response-Filter auf eine Serie an.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Wendet zwei Segmente lineare Regression auf eine Reihe an und gibt mehrere Spalten zurück.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Wendet zwei Segmente lineare Regression auf eine Reihe an und gibt dynamisches Objekt zurück.|
|[series_fit_line()](series-fit-linefunction.md)|Wendet die lineare Regression auf eine Reihe an und gibt mehrere Spalten zurück.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Wendet die lineare Regression auf eine Reihe an und gibt dynamisches Objekt zurück.|
|[series_iir()](series-iirfunction.md)|Wendet einen Infinite Impulse Response-Filter auf eine Serie an.|
|[series_outliers()](series-outliersfunction.md)|Erzielt Anomaliepunkte in einer Serie.|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|Berechnet den Pearson-Korrelationskoeffizienten zweier Reihen.|
|[series_periods_detect()](series-periods-detectfunction.md)|Sucht die wichtigsten Zeiträume, die in einer Zeitreihe vorhanden sind.|
|[series_periods_validate()](series-periods-validatefunction.md)|Überprüft, ob eine Zeitreihe periodische Muster bestimmter Längen enthält.|
|[series_seasonal()](series-seasonalfunction.md)|Sucht die saisonale Komponente der Serie.|
|[series_stats()](series-statsfunction.md)|Gibt Statistiken für eine Reihe in mehreren Spalten zurück.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Gibt Statistiken für eine Reihe in dynamischen Objekten zurück.|

## <a name="string-functions"></a>Zeichenfolgenfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|Kodiert eine Zeichenfolge als base64-Zeichenfolge.|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|Dekodiert eine base64-Zeichenfolge zu einer UTF-8-Zeichenfolge.|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|Decodiert eine base64-Zeichenfolge in ein Array langer Werte.|
|[countof()](cotfunction.md)|Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Einfache Zeichenfolgenübereinstimmungen überlappen sich möglicherweise; bei regex-Übereinstimmungen ist dies nicht Fall.|
|[Auszug()](extractfunction.md)|Ruft eine Übereinstimmung für einen regulären Ausdruck aus einer Textzeichenfolge ab.|
|[extract_all()](extractallfunction.md)|Abrufen aller Übereinstimmungen für einen regulären Ausdruck aus einer Textzeichenfolge.|
|[extractjson()](extractjsonfunction.md)|Rufen Sie ein angegebenes Element aus einem JSON-Text mit einem Pfadausdruck ab.|
|[indexof()](indexoffunction.md)|Function meldet den nullbasierten Index des ersten Vorkommens einer angegebenen Zeichenfolge innerhalb der Eingabezeichenfolge.|
|[isempty()](isemptyfunction.md)|Gibt true zurück, wenn das Argument eine leere Zeichenfolge oder null ist.|
|[isnotempty()](isnotemptyfunction.md)|Gibt true zurück, wenn das Argument keine leere Zeichenfolge oder ein NULL-Zeichen ist.|
|[isnotnull()](isnotnullfunction.md)|Gibt true zurück, wenn das Argument nicht null ist.|
|[isnull()](isnullfunction.md)|Bewertet das einzige Argument und gibt einen bool-Wert zurück, der angibt, ob das Argument zu einem NULL-Wert ausgewertet wird.|
|[parse_csv()](parsecsvfunction.md)|Teilt eine bestimmte Zeichenfolge, die durch Kommas getrennte Werte darstellt, und gibt ein Zeichenfolgenarray mit diesen Werten zurück.|
|[parse_ipv4()](parse-ipv4function.md)|Konvertiert Die Eingabe in eine lange (signierte 64-Bit) Zahlendarstellung.|
|[parse_json()](parsejsonfunction.md)|Interpretiert eine Zeichenfolge als JSON-Wert) und gibt den Wert als dynamisch zurück.|
|[parse_url()](parseurlfunction.md)|Analysiert eine absolute URL-Zeichenfolge und gibt ein dynamisches Objekt zurück, das alle Teile der URL enthält.|
|[parse_urlquery()](parseurlqueryfunction.md)|Analysiert eine URL-Abfragezeichenfolge und gibt ein dynamisches Objekt zurück, das die Abfrageparameter enthält.|
|[parse_version()](parse-versionfunction.md)|Konvertiert die Darstellung der Eingabezeichenfolge der Version in eine vergleichbare Dezimalzahl.|
|[ersetzen()](replacefunction.md)|Ersetzen Sie alle regex-Übereinstimmungen mit einer anderen Zeichenfolge.|
|[reverse()](reversefunction.md)|Funktion macht umgekehrt von Eingabezeichenfolge.|
|[split()](splitfunction.md)|Teilt eine bestimmte Zeichenfolge nach einem bestimmten Trennzeichen und gibt ein Zeichenfolgenarray mit den enthaltenen Teilzeichenfolgen zurück.|
|[strcat()](strcatfunction.md)|Verkettet zwischen 1 und 64 Argumenten.|
|[strcat_delim()](strcat-delimfunction.md)|Verkettet zwischen 2 und 64 Argumente mit Trennzeichen, die als erstes Argument angeführt werden.|
|[strcmp()](strcmpfunction.md)|Vergleicht zwei Zeichenfolgen.|
|[strlen()](strlenfunction.md)|Gibt die Länge der Eingabezeichenfolge in Zeichen zurück.|
|[strrep()](strrepfunction.md)|Wiederholt die angegebene Zeichenfolge, die die Anzahl der Zeiten angegeben hat (Standard - 1).|
|[substring()](substringfunction.md)|Extrahiert eine Teilzeichenfolge aus einer Quellzeichenfolge, beginnend mit einem Index bis zum Ende der Zeichenfolge.|
|[toupper()](toupperfunction.md)|Konvertiert eine Zeichenfolge in Großbuchstaben.|
|[übersetzen()](translatefunction.md)|Ersetzt einen Satz von Zeichen ('searchList') durch einen anderen Satz von Zeichen ('replacementList') in einer bestimmten Zeichenfolge.|
|[trim()](trimfunction.md)|Entfernt alle führenden und nachgestellten Übereinstimmungen des angegebenen regulären Ausdrucks.|
|[trim_end()](trimendfunction.md)|Entfernt die nachfolgende Übereinstimmung des angegebenen regulären Ausdrucks.|
|[trim_start()](trimstartfunction.md)|Entfernt die führende Übereinstimmung des angegebenen regulären Ausdrucks.|
|[url_decode()](urldecodefunction.md)|Die Funktion konvertiert codierte URL in eine reguläre URL-Darstellung.|
|[url_encode()](urlencodefunction.md)|Die Funktion konvertiert Zeichen der Eingabe-URL in ein Format, das über das Internet übertragen werden kann.|

## <a name="ip-v4-functions"></a>IP v4-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|Vergleicht zwei IPv4-Zeichenfolgen.|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|Entspricht zwei IPv4-Zeichenfolgen.|
|[parse_ipv4()](parse-ipv4function.md)|Konvertiert die Eingabezeichenfolge in eine lange (signierte 64-Bit) Zahlendarstellung.|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|Konvertiert Die Eingabezeichenfolge und die IP-Präfixmaske wird in eine lange (signierte 64-Bit) Zahlendarstellung konvertiert.|

## <a name="type-functions"></a>Typfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|Gibt den Laufzeittyp des einzelnen Arguments zurück.|


## <a name="scalar-aggregation-functions"></a>Skalar-Aggregationsfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|Berechnet die dcount aus hll-Ergebnissen (die durch hll oder hll-merge generiert wurden).|
|[hll_merge()](hllmergefunction.md)|Führt hll-Ergebnisse zusammen (skalare Version der Aggregatversion hll-merge()).|
|[percentile_tdigest()](percentile-tdigestfunction.md)|Berechnet das Perzentilergebnis aus tdigest-Ergebnissen (die durch tdigest oder merge-tdigests generiert wurden).|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|Berechnet die prozentuale Rangfolge eines Werts in einem Dataset.|
|[rank_tdigest()](rank-tdigest.md)|Berechnet den relativen Rang eines Werts in einer Menge.|
|[tdigest_merge()](tdigest-mergefunction.md)|Führt tdigest-Ergebnisse zusammen (skalare Version der Aggregatversion tdigest-merge()).|

## <a name="geo-spatial-functions"></a>Georäumliche Funktionen

|Funktionsname|Beschreibung|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|Berechnet den kürzesten Abstand zwischen zwei Geokoordinaten auf der Erde.|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|Berechnet die räumlichen Koordinaten, die den Mittelpunkt eines rechteckigen Geohash-Bereichs darstellen.|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|Berechnet, ob sich die räumlichen Koordinaten innerhalb eines Kreises auf der Erde befinden.|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|Berechnet den Geohash-Zeichenfolgenwert für eine geografische Position.|