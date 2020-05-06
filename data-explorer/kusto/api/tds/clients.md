---
title: MS-TDS-Clients und Kusto-Azure-Daten-Explorer
description: In diesem Artikel werden MS-TDS-Clients und Kusto in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5e2de0c29c58959ce683518b03bef9164fa9543c
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799627"
---
# <a name="ms-tds-clients-and-azure-data-explorer"></a>MS-TDS-Clients und Azure-Daten-Explorer

Azure Daten-Explorer implementiert TDS-kompatible Endpunkte für MS-SQL-Clients. Die Kompatibilität erfolgt auf Protokollebene. Alle Bibliotheken oder Anwendungen, die eine Verbindung mit der SQL Azure Datenbank mit Azure Active Directory (Azure AD)-Authentifizierung herstellen können, funktionieren mit dem Azure Daten-Explorer-Server. Daher können Sie den Server Domänen Namen wie den SQL Azure Server verwenden.

Azure Daten-Explorer implementiert eine Teilmenge von T-SQL und eine Teilmenge der SQL Server-Emulation. Weitere Informationen finden Sie unter [bekannte Probleme](./sqlknownissues.md) bei den Unterschieden zwischen der SQL Server-Implementierung von T-SQL und Azure Daten-Explorer.

## <a name="net-sql-client"></a>.NET SQL-Client

Azure Daten-Explorer unterstützt Azure AD Authentifizierung für SQL-Clients. Weitere Informationen finden Sie unter [.NET SQL-Client (Benutzerauthentifizierung)](./aad.md#net-sql-client-user) und [.NET SQL-Client (Anwendungs Authentifizierung)](./aad.md#net-sql-client-application) .

## <a name="jdbc"></a>JDBC

Der Microsoft JDBC-Treiber kann zum Herstellen einer Verbindung mit Azure Daten-Explorer mit Azure AD Authentifizierung verwendet werden.

Erstellen Sie eine Anwendung, um eine der Versionen von " *MSSQL-JDBC* jar" und " *adal4j* jar" und all ihre Abhängigkeiten zu verwenden.
Beispiel:

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

Erstellen Sie eine Anwendung, um die JDBC-Treiber Klasse *com. Microsoft. SqlServer. JDBC. SQLServerDriver*zu verwenden.

Verwenden Sie eine Verbindungs Zeichenfolge wie die folgende.

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

> [!NOTE]
> Wenn Sie den integrierten Authentifizierungsmodus Azure Active Directory verwenden möchten, ersetzen Sie *activedirectorypassword* durch *activedirectoryintegrated*. Weitere Informationen finden Sie unter [JDBC (Benutzerauthentifizierung)](./aad.md#jdbc-user) und [JDBC (Anwendungs Authentifizierung)](./aad.md#jdbc-application).

## <a name="odbc"></a>ODBC

Anwendungen, die ODBC unterstützen, können eine Verbindung mit Azure Daten-Explorer herstellen.

Erstellen Sie eine ODBC-Datenquelle:

1. Starten Sie den ODBC-Datenquellen-Administrator.
2. Wählen Sie **Hinzufügen** aus, um eine neue Datenquelle zu erstellen, und legen Sie *ODBC Driver 17 auf SQL Server*fest.
3. Benennen Sie die Datenquelle, und geben Sie im Feld **Server** den Namen des Azure-Daten-Explorer Clusters an. Beispielsweise *mykusto.Kusto.Windows.net*.
4. Legen Sie für die Authentifizierungs Option **Active Directory integriert**fest.
5. Wählen Sie **weiter** aus, um die Datenbank festzulegen.
7. Sie können einfach die Standardeinstellungen für alle anderen Einstellungen auf den folgenden Registerkarten überlassen.
8. Wählen Sie **Fertig** stellen aus, um das Fenster Datenquellen Zusammenfassung zu öffnen, in dem die Verbindung getestet werden kann.

Nun können Sie die ODBC-Datenquelle mit den Anwendungen verwenden.

Wenn die ODBC-Anwendung anstelle von oder zusätzlich zu DSN eine Verbindungs Zeichenfolge akzeptieren kann, verwenden Sie Folgendes:

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

Einige ODBC-Anwendungen funktionieren nicht gut mit `NVARCHAR(MAX)` dem Typ. Weitere Informationen finden Sie unter https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver. 

Die häufige Problem Umgehung besteht darin, die zurückgegebenen Daten in *nvarchar (n)* umzuwandeln, wobei ein Wert für n festgelegt ist. Beispiel: *nvarchar (4000)*. Eine solche Problem Umgehung funktioniert jedoch nicht für Azure-Daten-Explorer, da Azure Daten-Explorer nur einen String-Typ aufweist und für SQL-Clients als *nvarchar (max)* codiert ist.

Azure Daten-Explorer bietet eine andere Problem Umgehung. Sie können Azure Daten-Explorer so konfigurieren, dass alle Zeichen folgen als *nvarchar (n)* über eine Verbindungs Zeichenfolge codiert werden. Das Feld Sprache in der Verbindungs Zeichenfolge kann zum Angeben von Optimierungs Optionen im Format, * language@OptionName1:OptionValue1, OptionName2: OptionValue2*verwendet werden.

Beispielsweise weist die folgende Verbindungs Zeichenfolge Azure Daten-Explorer an, Zeichen folgen als *nvarchar (8000)* zu codieren.

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

Im folgenden finden Sie ein Beispiel für das PowerShell-Skript, das den ODBC-Treiber verwendet.

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  
```

## <a name="linqpad"></a>LINQPad

Eine LINQ-Anwendung kann mit Azure Daten-Explorer verwendet werden, indem Sie wie ein SQL-Server verbunden wird.
Verwenden Sie LINQPad, um die LINQ-Kompatibilität zu untersuchen und Azure Daten-Explorer zu durchsuchen. Außerdem können SQL-Abfragen ausgeführt werden. Dies ist das empfohlene Tool für die Untersuchung von Azure Daten-Explorer TDS-Endpunkten (SQL).

Verbinden Sie sich wie Sie mit dem Microsoft SQL Server. LINQPad unterstützt Active Directory-Authentifizierung.

1. Wählen Sie **Verbindung hinzufügen** aus.
2. Legen Sie den **builddatenkontext automatisch**fest.
3. Legen Sie **default (LINQ to SQL)**, den LINQPad-Treiber fest.
4. Legen Sie **SQL Azure**fest.
5. Geben Sie für den Server den Namen des Azure-Daten-Explorer Clusters an. Beispielsweise *mykusto.Kusto.Windows.net*.
6. Legen Sie die **Windows-Authentifizierung (Active Directory)** für die Anmeldung fest.
7. Wählen Sie **Test** , um die Konnektivität zu überprüfen
8. Klicken Sie auf **OK**. Im Browserfenster wird die Strukturansicht mit den Datenbanken angezeigt.
9. Sie können die Datenbanken, Tabellen und Spalten durchsuchen.
10. Sie können SQL-Abfragen im Abfragefenster ausführen. Geben Sie die SQL-Sprache an, und wählen Sie eine Verbindung mit der Datenbank aus.
11. Sie können auch LINQ-Abfragen im Abfragefenster ausführen. Wählen Sie beispielsweise im Browserfenster eine Tabelle aus. Wählen Sie **Anzahl**aus, und führen Sie Sie aus.

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio (1.3.4 und höher)

Erstellen Sie eine neue Verbindung.

1. Legen Sie den Verbindungstyp auf **Microsoft SQL Server**fest.
2. Geben Sie den Namen des Azure-Daten-Explorer Clusters als Servernamen an. Beispielsweise *mykusto.Kusto.Windows.net*.
3. Legen Sie den Authentifizierungstyp **Azure Active Directory-Universal mit MFA-Unterstützung**fest.
4. Geben Sie das Konto an, das in der Azure AD bereitgestellt wird. Beispiel: *myname@contoso.com* Fügen Sie das Konto zum ersten Mal hinzu.
5. Verwenden Sie die Datenbankauswahl, um die Datenbank auszuwählen.
6. Wählen Sie **verbinden** aus, um zum Dashboard der Datenbank zu gelangen und die Verbindung festzulegen.
7. Wählen Sie **neue Abfrage** aus, um das Abfragefenster zu öffnen, oder wählen Sie im Dashboard die Aufgabe **neue Abfrage** aus.

## <a name="power-bi-desktop"></a>Power BI-Desktop

Verbinden Sie sich wie Sie mit der SQL Azure Datenbank.

1. Wählen Sie unter **Daten erhalten**die Option **Weitere**aus.
2. Legen Sie **Azure**und dann **Azure SQL-Datenbank**fest.
3. Geben Sie den Namen des Azure Daten-Explorer Servers an. Beispielsweise *mykusto.Kusto.Windows.net*.
4. Wählen Sie **directquery**aus.
5. Legen Sie **Microsoft-Konto** Authentifizierung (nicht **Windows**) fest, und wählen Sie **Anmelden aus**.
6. Die Datenbankauswahl zeigt die verfügbaren Datenbanken. Mit einem echten SQL Server können Sie wie gewünscht fortfahren.

## <a name="excel"></a>Excel

Verbinden Sie sich wie Sie mit der SQL Azure Datenbank.

1. Wählen **Sie** auf der Registerkarte **Daten** Daten aus, und legen Sie dann **aus Azure** und **aus Azure SQL-Datenbank**fest.
2. Geben Sie den Namen des Azure Daten-Explorer Servers an. Beispielsweise *mykusto.Kusto.Windows.net*.
3. Legen Sie **Microsoft-Konto** Authentifizierung (nicht **Windows**) fest, und wählen Sie **Anmelden aus**.
5. Die Datenbankauswahl zeigt die verfügbaren Datenbanken. Mit einem echten SQL Server können Sie wie gewünscht fortfahren.
4. Nachdem Sie sich angemeldet haben, wählen Sie **verbinden**aus.
5. Die Datenbankauswahl zeigt die verfügbaren Datenbanken. Mit einem echten SQL Server können Sie wie gewünscht fortfahren.

## <a name="tableau"></a>Tableau

Erstellen Sie eine ODBC-Datenquelle. Weitere Informationen finden Sie im Abschnitt zu [ODBC](./clients.md#odbc) .

1. Stellen Sie eine Verbindung über **andere Datenbanken (ODBC)** her.
2. Legen Sie die ODBC-Datenquelle in **DSN**fest.
3. Wählen Sie **verbinden** aus, um eine Verbindung herzustellen.
4. Wählen Sie **Anmelden aus**, sobald die Schaltfläche verfügbar ist, und melden Sie sich bei Azure Daten-Explorer an.

## <a name="dbeaver-533-and-above"></a>Dbeaver (5.3.3 und höher)

Konfigurieren Sie dbeaver für die Verarbeitung von Resultsets in einer Weise, die mit Azure-Daten-Explorer kompatibel ist.

1. Wählen Sie im Menü **Fenster** die Option **Einstellungen** aus.
2. Wählen Sie im Abschnitt **Editoren** den **Daten-Editor** aus.
3. Stellen Sie sicher, dass beim **Lesen der nächsten Seite Daten aktualisieren** markiert ist.

Erstellen Sie eine Verbindung mit der Azure-Daten-Explorer-Datenbank.

1. Wählen Sie im Menü **Datenbank** die Option **neue Verbindung** aus.
2. Suchen Sie nach **Azure** , und legen Sie **Azure SQL-Datenbank**fest. Wählen Sie **Weiter** aus.
3. Geben Sie den Host an. Beispielsweise *mykusto.Kusto.Windows.net*.
4. Geben Sie die Datenbank an. Beispiel: *MyDatabase*.

> [!WARNING]
> Verwenden Sie *Master* nicht als Datenbanknamen. Azure Daten-Explorer erfordert eine Verbindung mit einer bestimmten Datenbank.

5. Legen Sie **Active Directory-Kennwort** für die *Authentifizierung*fest.
6. Geben Sie die Anmelde Informationen des Active Directory-Benutzers an. Beispielsweise *myname@contoso.com*, und legen Sie das entsprechende Kennwort für diesen Benutzer fest.
7. Wählen Sie **Verbindung testen... aus.** , um zu überprüfen, ob die Verbindungsdetails richtig sind.

## <a name="microsoft-sql-server-management-studio-v18x"></a>Microsoft SQL Server Management Studio (V18. x)

1. Wählen Sie **verbinden**aus, und klicken Sie dann unter **Objekt-Explorer**auf **Datenbank-Engine** .
2. Geben Sie den Namen des Azure-Daten-Explorer Clusters als Servernamen an. Beispielsweise *mykusto.Kusto.Windows.net*.
3. Legen Sie für die Authentifizierung **Active Directory integriert** fest.
4. Wählen Sie **Optionen**aus.
5. Wählen Sie unter **mit Datenbank verbinden** die Option **Durchsuchen Server** aus, um verfügbare Datenbanken
6. Wählen Sie **Ja** aus, um den Browser fortzusetzen
7. Das Fenster zeigt eine Strukturansicht mit allen verfügbaren Datenbanken an. Wählen Sie eine aus, um eine Verbindung mit der Datenbank herzustellen.
8. Eine weitere Möglichkeit besteht darin, unter **mit Datenbank verbinden**die Option **Standard** auszuwählen und dann **verbinden**auszuwählen. Im Objekt-Explorer werden alle Datenbanken angezeigt.

> [!NOTE]
> Das Durchsuchen von Datenbankobjekten über SSMS wird noch nicht unterstützt, da SSMS Unterabfragen korrelieren, um das Datenbankschema zu durchsuchen.
> Korrelierte Unterabfragen werden von Azure Daten-Explorer nicht unterstützt. Weitere Informationen finden Sie unter [korrelierte Unterabfragen](./sqlknownissues.md#correlated-sub-queries).

10. Wählen Sie **neue Abfrage** aus, um das Abfragefenster zu öffnen und Ihre Datenbank festzulegen.

Sie können benutzerdefinierte SQL-Abfragen im Abfragefenster ausführen.

## <a name="matlab-via-jdbc"></a>MATLAB (über JDBC)

Fügen Sie die erforderlichen JAR-Dateien am Anfang des statischen classpath von MATLAB hinzu, indem Sie eine *"javaclasspath. txt"* -Datei in Ihrem Einstellungs Verzeichnis erstellen.

1. Führen Sie den folgenden Befehl im Befehlsfenster von MATLAB aus.

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

2. Fügen Sie die vollständigen Pfade zu den erforderlichen JAR-Dateien hinzu.

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

> [!NOTE]
> Sie benötigen die <, **bevor** Sie am Anfang>, damit diese Dateien am Anfang des Classpath hinzugefügt werden. Ersetzen Sie außerdem *c:\full\path\to* durch die eigentlichen vollständigen Pfade zu diesen Dateien.

3. Starten Sie MATLAB neu, um sicherzustellen, dass diese Klassen geladen werden.

4. Bevor Sie versuchen, eine Verbindung herzustellen, führen Sie den folgenden Befehl aus (MATLAB-Befehlsfenster).

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> [!NOTE]
> Dadurch wird **TransformerFactory** auf den Standardwert zurückgesetzt (MATLAB überlädt dies normalerweise mit **Saxon**, aber dies ist nicht mit **ADAL4J**kompatibel).

5. Stellen Sie mithilfe des folgenden Befehls (MATLAB-Befehlsfenster) eine Verbindung mit dem Azure Daten-Explorer TDS-Endpunkt her.

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> [!NOTE]
> Es ist in Ordnung, mit **"Database ="** zu enden und dann ohne Wert. Die *Daten Bank* Funktion fügt automatisch die erste Eingabe, den Datenbanknamen, an diese Zeichenfolge an.
> Wenn Sie Azure Active Directory integrierten Authentifizierungsmodus verwenden möchten, ersetzen Sie *activedirectorypassword* durch *activedirectoryintegrated*.

6. Testen Sie die Verbindung, und führen Sie eine Beispiel Abfrage aus. Senden Sie die folgenden Befehle (MATLAB-Befehlsfenster).

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> [!NOTE]
> Ersetzen Sie *KUSTO_TABLE* durch eine vorhandene Tabelle in Azure Daten-Explorer.

## <a name="sending-t-sql-queries-over-the-rest-api"></a>Senden von T-SQL-Abfragen über die Rest-API

Die [Azure Daten-Explorer-Rest-API](../rest/index.md) kann T-SQL-Abfragen akzeptieren und ausführen.

1. Senden Sie die Anforderung an den Abfrage Endpunkt, wobei die **CSL** -Eigenschaft auf den Text der T-SQL-Abfrage festgelegt ist.
2. Legen Sie die **[Anforderungs Eigenschaft](../netfx/request-properties.md)** **optionquerylanguage** auf **SQL**fest.
