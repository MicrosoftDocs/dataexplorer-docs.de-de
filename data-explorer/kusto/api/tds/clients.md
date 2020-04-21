---
title: MS-TDS-Clients und Kusto - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden MS-TDS-Clients und Kusto in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 233eec65c14d76b25b76cc85c7ddd190e5171dfe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523493"
---
# <a name="ms-tds-clients-and-kusto"></a>MS-TDS-Clients und Kusto

Kusto implementiert TDS-konformen Endpunkt für MS-SQL-Clients. Da die Kompatibilität auf Protokollebene erfolgt, würde jede Bibliothek oder Anwendung, die mit der Azure-Azure-Datenbank mit Azure Active Directory-Authentifizierung verbunden werden kann, mit dem Kusto-Server, orthogonal mit dem Betriebssystem oder der verwendeten Laufzeit arbeiten. Verwenden Sie einfach den Kusto-Serverdomänennamen wie den SQL Azure-Server.

Auf SQL-Sprachebene implementiert Kusto eine Teilmenge von T-SQL und eine Teilmenge der SQL-Serveremulation. Sehen Sie [bekannte Probleme](./sqlknownissues.md) für einige der Hauptunterschiede zwischen der Implementierung von T-SQL und Kusto durch SQL Server.

## <a name="net-sql-client"></a>.NET SQL-Client

Kusto unterstützt die Azure Active Directory-Authentifizierung für SQL-Clients.

Weitere Informationen finden Sie unter [.NET SQL Client (Benutzerauthentifizierung)](./aad.md#net-sql-client-user) und [.NET SQL Client (Anwendungsauthentifizierung)](./aad.md#net-sql-client-application)



## <a name="jdbc"></a>JDBC

Der Microsoft JDBC-Treiber kann verwendet werden, um eine Verbindung mit Kusto mit AAD-Authentifizierung herzustellen.

Stellen Sie eine Anwendung auf, um eine der Versionen von *mssql-jdbc* JAR und *adal4j* JAR und all ihren Abhängigkeiten zu verwenden. Beispiel:

```s
mssql-jdbc-7.0.0.jre8.jar
adal4j-1.6.3.jar
accessors-smart-1.2.jar
activation-1.1.jar
asm-5.0.4.jar
commons-codec-1.11.jar
commons-lang3-3.5.jar
gson-2.8.0.jar
javax.mail-1.6.1.jar
jcip-annotations-1.0-1.jar
json-smart-2.3.jar
lang-tag-1.4.4.jar
nimbus-jose-jwt-6.5.jar
oauth2-oidc-sdk-5.64.4.jar
slf4j-api-1.7.21.jar
```

Appliation zur Verwendung der JDBC-Treiberklasse erstellen`com.microsoft.sqlserver.jdbc.SQLServerDriver`

Verwenden Sie Verbindungszeichenfolge wie:

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

Wenn Sie den integrierten AAD-Modus verwenden möchten, ersetzen Sie *ActiveDirectoryPassword* durch *ActiveDirectoryIntegrated*

Weitere Informationen finden Sie unter [JDBC (Benutzerauthentifizierung)](./aad.md#jdbc-user) und [JDBC (Anwendungsauthentifizierung)](./aad.md#jdbc-application)

## <a name="odbc"></a>ODBC

Anwendungen, die ODBC unterstützen, können eine Verbindung zu Kusto herstellen.

ODBC-Datenquelle erstellen:
1. Starten Sie den ODBC-Datenquellenadministrator.
2. Erstellen Einer neuen `Add`Datenquelle (Schaltfläche ).
3. Klicken Sie auf `ODBC Driver 17 for SQL Server`.
3. Geben Sie der Datenquelle einen Namen und `Server` geben Sie den Kusto-Clusternamen im Feld an, z. B.`mykusto.kusto.windows.net`
4. Wählen `Active Directory Integrated` Sie die Authentifizierungsoption aus.
5. Auf der nächsten Registerkarte können Sie die Datenbank auswählen.
7. Kann Die Standardeinstellungen für alle anderen Einstellungen auf den folgenden Registerkarten belassen.
8. `Finish`die Schaltfläche öffnet das Dialogfeld Datenquellenzusammenfassung, in dem die Verbindung getestet werden kann.
9. ODBC-Quelle kann jetzt mit Anwendungen verwendet werden.

Wenn die ODBC-Anwendung stattdessen oder zusätzlich zu DSN eine Verbindungszeichenfolge akzeptieren kann, verwenden Sie die folgende Verbindungszeichenfolge:
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

Einige ODBC-Anwendungen funktionieren nicht gut mit dem Typ https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver NVARCHAR(MAX) (weitere Informationen finden Sie unter). Die übliche Problemumgehung, die für eine solche Anwendung verwendet wird, besteht darin, zurückgegebene Daten in NVARCHAR(n) zu überführen, mit einem gewissen Wert für n, z. B. NVARCHAR(4000). Eine solche Problemumgehung würde für Kusto nicht funktionieren, da Kusto nur über einen Zeichenfolgentyp verfügt und für SQL-Clients als NVARCHAR(MAX) codiert ist. Kusto bietet verschiedene Problemumgehungen für solche Anwendungen. Es ist möglich, Kusto so zu konfigurieren, dass alle Zeichenfolgen als NVARCHAR(n) über eine Verbindungszeichenfolge kodiert werden. Das Sprachfeld in der Verbindungszeichenfolge kann verwendet werden, um Optimierungsoptionen in einem Format anzugeben: `language@OptionName1:OptionValue1,OptionName2:OptionValue2`. 

Die folgende Verbindungszeichenfolge weist Kusto beispielsweise an, Zeichenfolgen als NVARCHAR(8000) zu codieren:
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

Beispiel für PowerShell-Skript, das ODBC-Treiber verwendet:

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  

```



## <a name="linqpad"></a>LINQPad

Es ist möglich, Linq-Anwendungen mit Kusto zu verwenden, indem Sie eine Verbindung zu Kusto herstellen, wie es SQL-Server ist.
LINQPad kann verwendet werden, um die Linq-Kompatibilität zu untersuchen. Es kann auch verwendet werden, um Kusto zu durchsuchen und SQL-Abfragen auszuführen.
LINQPad ist das empfohlene Tool zum Untersuchen des Kusto TDS (SQL)-Endpunkts.

Stellen Sie eine Verbindung her, wie Sie eine Verbindung mit Microsoft SQL Server herstellen. Beachten Sie, dass LINQPad die Active Directory-Authentifizierung unterstützt.

1. Klicken Sie auf `Add connection`.
2. Wählen Sie die Option `Build data context automatically`.
3. Wählen Sie LINQPad Treiber `Default (LINQ to SQL)`.
4. Wählen Sie `SQL Azure`als Anbieter .
5. Geben Sie für den Server den Namen des Kusto-Clusters an, z.B.`mykusto.kusto.windows.net`
6. Für Login `Windows Authentication (Active Directory)`können wählen .
7. Klicken `Test` Sie auf die Schaltfläche, um die Konnektivität zu überprüfen.
8. Klicken `OK` Sie auf die Schaltfläche. Das Browserfenster zeigt die Strukturansicht mit Datenbanken an.
9. Sie können Datenbanken, Tabellen und Spalten durchsuchen.
10. Im Abfragefenster können Sie SQL-Abfragen ausführen. Geben Sie die SQL-Sprache an, und wählen Sie die Verbindung zur Datenbank aus.
11. Im Abfragefenster können auch LINQ-Abfragen ausgeführt werden. Klicken Sie z.B. mit der rechten Maustaste auf eine Tabelle im Browserfenster. Pick-Option. `Count` Lassen Sie es laufen.

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio (1.3.4 und höher)

1. Neue Verbindung.
2. Verbindungstyp auswählen: `Microsoft SQL Server`.
3. Geben Sie den Namen des Kusto-Clusters als Servernamen an, z. B.`mykusto.kusto.windows.net`
4. Authentifizierungstyp `Azure Active Directory - Universal with MFA support`auswählen: .
5. Geben Sie das in AAD bereitgestellte Konto an, z. B. `myname@contoso.com` (Konto beim ersten Mal hinzufügen).
6. Die Datenbankauswahl kann verwendet werden, um die Datenbank auszuwählen.
7. `Connect`-Taste zum Datenbank-Dashboard.
8. Klicken Sie mit `New Query` der rechten Maustaste `New Query` auf die Verbindung und wählen Sie diese Option aus, um die Abfrageregisterkarte zu öffnen oder auf die Aufgabe im Dashboard zu klicken.

## <a name="power-bi-desktop"></a>Power BI Desktop

Stellen Sie eine Verbindung her, wie Sie eine Verbindung mit SQL Azure Database herstellen.

1. Wählen `Get Data` `More`Sie `Azure` dann , und dann`Azure SQL Database`
2. Geben Sie den Kusto-Servernamen an, z.B.`mykusto.kusto.windows.net`
3. Verwenden Sie die Option "DirectQuery".
4. Wählen `Microsoft account` Sie `Windows`Authentifizierung `sign in`(nicht ) und klicken Sie auf .
5. Die Auswahl zeigt verfügbare Datenbanken an. Fahren Sie so fort, wie Sie es mit einem echten SQL-Server tun würden.

## <a name="excel"></a>Excel

Stellen Sie eine Verbindung her, wie Sie eine Verbindung mit SQL Azure Database herstellen.

1. In `Data` `Get Data`Tab, `From Azure`, ,`From Azure SQL Database`
2. Geben Sie den Kusto-Servernamen an, z.B.`mykusto.kusto.windows.net`
3. Wählen `Microsoft account` Sie `Windows`Authentifizierung `sign in`(nicht ) und klicken Sie auf .
4. Nach der Anmeldung `Connect`klicken Sie auf .
5. Die Auswahl zeigt verfügbare Datenbanken an. Fahren Sie so fort, wie Sie es mit einem echten SQL-Server tun würden.

## <a name="tableau"></a>Tableau

1. Erstellen Sie ODBC-Datenquelle, siehe [ABSCHNITT ODBC.](./clients.md#odbc)
2. Verbinden `Other Databases (ODBC)`Sie sich über .
3. Wählen Sie ODBC-Datenquelle in `DSN`aus.
4. Verwenden `Connect` Sie die Schaltfläche, um eine Verbindung herzustellen.
5. Sobald `Sign In` Button verfügbar ist, melden Sie sich bei Kusto an.

## <a name="dbeaver-533-and-above"></a>DBiber (5.3.3 und höher)

Konfigurieren Sie DBeaver für die Verarbeitung von Resultsets in einer Weise, die mit Kusto kompatibel ist:

1. Wählen `Window` Sie `Preferences`im Menü .
2. Wählen `Editors` Sie `Data Editor`im Abschnitt .
3. Die `Refresh data on next page reading` Option "Sicherstellen" ist aktiviert.

Herstellen einer Verbindung mit der Kusto-Datenbank:

1. Erstellen Sie eine`Database` neue `New Connection` Datenbankverbindung (Menü und Option).
2. Suchen `Azure` und `Azure SQL Database`wählen Sie aus. Drücken Sie `Next`.
3. Geben Sie Host, `mykusto.kusto.windows.net` z.B. und `mydatabase`Datenbank, z.B. an. Lassen Sie Master nicht als Datenbanknamen. Kusto erfordert eine Verbindung zu einer bestimmten Datenbank.
4. Wählen Sie `Active Directory - Password`für die Authentifizierungsoption aus.
5. Geben Sie die Anmeldeinformationen des Active `myname@contoso.com` Directory-Benutzers an, z. B. und das entsprechende Kennwort für diesen Benutzer.
6. Drücken `Test Connection …` Sie , um zu überprüfen, ob die Verbindungsdetails korrekt sind.

## <a name="microsoft-sql-server-management-studio-v18x"></a>Microsoft SQL Server Management Studio (v18.x)

1. In `Object Explorer` `Connect`, `Database Engine`.
2. Geben Sie den Namen des Kusto-Clusters als Servernamen an, z. B.`mykusto.kusto.windows.net`
3. Verwenden `Active Directory - Integrated` Sie die Option für die Authentifizierung.
4. Klicken Sie auf `Options`.
5. In `Connect to database` der Kombination können Sie `Browse Server` verfügbare Datenbanken per Option durchsuchen.
6. Klicken `Yes` Sie hier, um mit dem Browsen fortzufahren.
7. Das Dialogfeld zeigt die Strukturansicht mit allen verfügbaren Datenbanken an. Kann auf eine klicken, um mit der Verbindung zur Datenbank fortzufahren.
8. Alternativ, in `Connect to database` Combo, `default`können wählen . Klicken Sie auf `Connect`. Der Objekt-Explorer zeigt alle Datenbanken an.
9. Das Durchsuchen von Datenbankobjekten über SSMS wird noch nicht unterstützt, da SSMS Korrelat-Unterabfragen verwendet, um das Datenbankschema zu durchsuchen. Korrelierte Unterabfragen werden von Kusto nicht unterstützt, siehe [korrelierte Unterabfragen](./sqlknownissues.md#correlated-sub-queries).
10. Klicken Sie auf Ihre Datenbank. Klicken `New Query` Sie auf die Option, um das Abfragefenster zu öffnen.
11. Kann benutzerdefinierte SQL-Abfragen aus dem Abfragefenster ausführen.

## <a name="matlab-via-jdbc"></a>MATLAB (über JDBC)

Fügen Sie die erforderlichen JAR-Dateien an der Vorderseite des statischen Klassenpfads von MATLAB hinzu. Erstellen Sie dazu eine *Datei "javaclasspath.txt"* in Ihrem Präferenzverzeichnis. Führen Sie den folgenden Befehl im Befehlsfenster von Matlab aus: 

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

Und fügen Sie die vollständigen Pfade zu den erforderlichen JAR-Dateien hinzu: 

``` s
<before>
c:\full\path\to\accessors-smart-1.2.jar
c:\full\path\to\activation-1.1.jar
c:\full\path\to\adal4j-1.6.3.jar
c:\full\path\to\asm-5.0.4.jar
c:\full\path\to\commons-codec-1.11.jar
c:\full\path\to\commons-lang3-3.5.jar
c:\full\path\to\gson-2.8.0.jar
c:\full\path\to\javax.mail-1.6.1.jar
c:\full\path\to\jcip-annotations-1.0-1.jar
c:\full\path\to\json-smart-2.3.jar
c:\full\path\to\lang-tag-1.4.4.jar
c:\full\path\to\mssql-jdbc-7.0.0.jre8.jar
c:\full\path\to\nimbus-jose-jwt-6.5.jar
c:\full\path\to\oauth2-oidc-sdk-5.64.4.jar
c:\full\path\to\slf4j-api-1.7.21.jar
```

> Sie benötigen die *<, bevor* sie oben>, um diese Dateien an der Vorderseite des Klassenpfads hinzuzufügen. Ersetzen Sie außerdem die "c:-Voll-Pfad"-Datei durch die eigentlichen vollständigen Pfade zu diesen Dateien. 

Starten Sie MATLAB neu, um sicherzustellen, dass diese Klassen tatsächlich geladen sind.

Führen Sie vor dem Versuch, eine Verbindung herzustellen, den folgenden Befehl (MATLAB-Befehlsfenster) aus:

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> Dadurch wird die *TransformerFactory* auf den Standardwert zurückgesetzt (MATLAB überlastet diese in der Regel mit Saxon, ist aber mit ADAL4J nicht kompatibel).

Um eine Verbindung mit dem Kusto TDS-Endpunkt herzustellen, senden Sie den folgenden Befehl (MATLAB-Befehlsfenster):

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> Es ist hier richtig, dass dies einfach mit *"database="* endet und dann kein Name, die "Datenbank" Funktion wird automatisch die erste Eingabe (der Datenbankname) an diese Zeichenfolge anhängen.
> Wenn Sie den integrierten AAD-Modus verwenden möchten, ersetzen Sie *ActiveDirectoryPassword* durch *ActiveDirectoryIntegrated*

Testen Sie die Verbindung, und führen Sie eine Beispielabfrage aus. Folgende Befehle senden (MATLAB-Befehlsfenster):

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> Ersetzen sie *KUSTO_TABLE* durch eine vorhandene Tabelle in Kusto



## <a name="sending-t-sql-queries-over-the-rest-api"></a>Senden von T-SQL-Abfragen über die REST-API

Die [Kusto REST-API](../rest/index.md) kann T-SQL-Abfragen akzeptieren und ausführen.
Senden Sie dazu die Anforderung an den `csl` Abfrageendpunkt, wobei die Eigenschaft auf den Text der T-SQL-Abfrage selbst festgelegt und die [Anforderungseigenschaft](../netfx/request-properties.md) `OptionQueryLanguage` auf den Wert `sql`festgelegt ist.