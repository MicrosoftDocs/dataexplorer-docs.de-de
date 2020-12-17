---
title: Erfassungsfehlercodes in Azure Data Explorer
description: Dieses Thema enthält eine Liste mit Erfassungsfehlercodes in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: vladikbr
ms.service: data-explorer
ms.topic: reference
ms.date: 11/11/2020
ms.openlocfilehash: 18ac718edd50c804f71b9b82cbffb5b2b7bb2e24
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933774"
---
# <a name="ingestion-error-codes-in-azure-data-explorer"></a>Erfassungsfehlercodes in Azure Data Explorer

Die folgende Liste enthält Fehlercodes, die bei der [Erfassung](ingest-data-overview.md) auftreten können. Wenn Sie für Ihren Cluster [Diagnoseprotokolle](using-diagnostic-logs.md#ingestion-logs-schema) für nicht erfolgreiche Erfassungen aktivieren, werden im Betriebsprotokoll **Failed ingestion** (Nicht erfolgreiche Erfassung) Fehlercodes angezeigt. Sie können auch die [Metrik](using-metrics.md#ingestion-metrics) **Erfassungsergebnis** überwachen, um die **Kategorie** von Erfassungsfehlern (aber nicht den spezifischen Fehler) zu ermitteln. Die folgenden Fehler sind nach diesen Kategorien gegliedert. 

> [!NOTE]
> Im Falle eines vorübergehenden Fehlers ist möglicherweise eine erneute Ausführung der Erfassung erfolgreich.

## <a name="category-badformat"></a>Kategorie: BadFormat

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|Stream_WrongNumberOfFields                        |Inkonsistente Anzahl von Feldern in den Eingabedatensätzen. HRESULT: 0x80DA0008      |Dauerhaft           |
|Stream_ClosingQuoteMissing                        |Ungültiges CSV-Format. Schließendes Anführungszeichen fehlt. HRESULT: 0x80DA000b            |Dauerhaft           |
|BadRequest_InvalidBlob                            |Das Blob ist ungültig.                                                              |Dauerhaft           |
|BadRequest_EmptyArchive                           |Das Archiv ist leer.                                                             |Dauerhaft           |
|BadRequest_InvalidArchive                         |Das Archiv ist ungültig.                                                           |Dauerhaft           |
|BadRequest_InvalidMapping                         |Fehler beim Analysieren der Erfassungszuordnung.<br>Weitere Informationen zum Schreiben von Erfassungszuordnungen finden Sie unter [Datenzuordnungen](./kusto/management/mappings.md).   |Dauerhaft           |
|BadRequest_InvalidMappingReference                |Ungültiger Zuordnungsverweis.            |Dauerhaft           |
|BadRequest_FormatNotSupported                     |Das Format wird nicht unterstützt. Möglicherweise verwenden Sie ein Format, das von einer bestimmten Datenverbindung nicht unterstützt wird. <br>Weitere Informationen zu Datenformaten, die von Azure Data Explorer für die Erfassung unterstützt werden, finden Sie unter [Von Azure Data Explorer für die Erfassung unterstützte Datenformate](ingestion-supported-formats.md). |Dauerhaft          |
|BadRequest_InconsistentMapping                    |Die unterstützte Erfassungszuordnung ist nicht mit dem vorhandenen Tabellenschema konsistent. |Dauerhaft           |
|BadRequest_UnexpectedCharacterInInputStream       |Unerwartetes Zeichen im Eingabestream.                                     |Dauerhaft           |

## <a name="category-badrequest"></a>Kategorie: BadRequest

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|BadRequest_EmptyBlob                              |Das Blob ist leer.                                                               |Dauerhaft           |
|BadRequest_EmptyBlobUri                           |Der Blob-URI ist leer.                                                           |Dauerhaft           |
|BadRequest_DuplicateMapping                       |Die Erfassungseigenschaften umfassen sowohl „ingestionMapping“ als auch „ingestionMappingReference“. Dies ist ungültig.              |Dauerhaft          |
|BadRequest_InvalidOrEmptyTableName                |Der Tabellenname ist leer oder ungültig.<br>Weitere Informationen zur Namenskonvention von Azure Data Explorer finden Sie unter [Entitätsnamen](./kusto/query/schema-entities/entity-names.md).    |Dauerhaft          |
|BadRequest_EmptyDatabaseName                      |Der Datenbankname ist leer.             |Dauerhaft          |
|BadRequest_EmptyMappingReference                  |Einige Formate benötigen Erfassungszuordnungen, um erfasst werden zu können, und der Zuordnungsverweis ist leer.<br>Weitere Informationen zur Zuordnung finden Sie unter [Datenzuordnungen](./kusto/management/mappings.md).        |Dauerhaft           |
|Download_BadRequest                               |Die Quelle konnte aufgrund einer ungültigen Anforderung nicht aus Azure Storage heruntergeladen werden.    |Dauerhaft           |
|BadRequest_MissingMappingtFailure                 |Avro-und JSON-Formate müssen mit dem Parameter „ingestionMapping“ oder „ingestionMappingReference“ erfasst werden.         |Dauerhaft           |
|Stream_NoDataToIngest                             |Es wurden keine Daten für die Erfassung gefunden.<br>Bei Daten im JSON-Format deutet dieser Fehler unter Umständen darauf hin, dass das JSON-Format ungültig war.        |Dauerhaft          |
|Stream_DynamicPropertyBagTooLarge                 |Die Daten enthalten zu große Werte in einer dynamischen Spalte. HRESULT: 0x80DA000E         |Dauerhaft          |
|General_BadRequest                                |Ungültige Anforderung.            |Dauerhaft           |
|BadRequest_CorruptedMessage                       |Die Nachricht ist beschädigt.    |Dauerhaft           |
|BadRequest_SyntaxError                            |Syntaxfehler in der Anforderung.     |Dauerhaft           |
|BadRequest_ZeroRetentionPolicyWithNoUpdatePolicy  |Die Tabelle verfügt über keine Aufbewahrungsrichtlinie und ist nicht die Quelltabelle für eine Updaterichtlinie.    |Dauerhaft           |
|BadRequest_CreationTimeEarlierThanSoftDeletePeriod|Die für die Erfassung angegebene Erstellungszeit liegt nicht innerhalb von `SoftDeletePeriod`.<br>Weitere Informationen zu `SoftDeletePeriod` finden Sie unter [Das Richtlinienobjekt](./kusto/management/retentionpolicy.md#the-policy-object).  |Dauerhaft   |
|BadRequest_NotSupported                           |Die Anforderung wird nicht unterstützt.    |Dauerhaft           |
|Download_SourceNotFound                           |Die Quelle konnte nicht aus Azure Storage heruntergeladen werden. Die Quelle wurde nicht gefunden.       |Dauerhaft       |
|BadRequest_EntityNameIsNotValid                   |Der Entitätsname ist ungültig.<br>Weitere Informationen zur Namenskonvention von Azure Data Explorer finden Sie unter [Entitätsnamen](./kusto/query/schema-entities/entity-names.md).    |Dauerhaft           |
|BadRequest_MalformedIngestionProperty              |Die Erfassungseigenschaft ist nicht wohlgeformt.    |Dauerhaft           |
| BadRequest_IngestionPropertyNotSupportedInThisContext | Die Erfassungseigenschaft wird in diesem Kontext nicht unterstützt.| Dauerhaft

## <a name="category-dataaccessnotauthorized"></a>Kategorie: DataAccessNotAuthorized

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|Download_AccessConditionNotSatisfied              |Die Quelle konnte nicht aus Azure Storage heruntergeladen werden. Die Zugriffsbedingung ist nicht erfüllt.     |Dauerhaft           |
|Download_Forbidden                                |Die Quelle konnte nicht aus Azure Storage heruntergeladen werden. Access forbidden. (Zugriff verweigert.)    |Dauerhaft           |
|Download_AccountNotFound                          |Die Quelle konnte nicht aus Azure Storage heruntergeladen werden. Das Konto wurde nicht gefunden.    |Dauerhaft           |
|BadRequest_TableAccessDenied                      |Der Zugriff auf die Tabelle wurde verweigert.<br>Weitere Informationen finden Sie unter [Rollenbasierte Autorisierung in Kusto](./kusto/management/access-control/role-based-authorization.md).     |Dauerhaft           |
|BadRequest_DatabaseAccessDenied                   |Der Zugriff auf die Datenbank wurde verweigert.<br>Weitere Informationen finden Sie unter [Rollenbasierte Autorisierung in Kusto](./kusto/management/access-control/role-based-authorization.md).                        |Dauerhaft           |

## <a name="category-downloadfailed"></a>Kategorie: DownloadFailed

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|Download_NotTransient                             |Die Quelle konnte nicht aus Azure Storage heruntergeladen werden. Ein nicht vorübergehender Fehler ist aufgetreten.                   |Dauerhaft           |
|Download_UnknownError                             |Die Quelle konnte nicht aus Azure Storage heruntergeladen werden. Unbekannter Fehler ist aufgetreten              |Transient (vorübergehend)           |


## <a name="category-entitynotfound"></a>Kategorie: EntityNotFound

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|BadRequest_MappingReferenceWasNotFound            |Der Zuordnungsverweis wurde nicht gefunden.   |Dauerhaft           |
|BadRequest_DatabaseNotExist                       |Die Datenbank ist nicht vorhanden.          |Dauerhaft           |
|BadRequest_TableNotExist                          |Die Tabelle ist nicht vorhanden.          |Dauerhaft           |
|BadRequest_EntityNotFound                         |Eine Azure Data Explorer-Entität (beispielsweise die Zuordnung, Datenbank oder Tabelle) wurde nicht gefunden.           |Dauerhaft           |

## <a name="category-filetoolarge"></a>Kategorie: FileTooLarge

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|Stream_InputStreamTooLarge                        |Die Gesamtgröße der Eingabedaten oder eines einzelnen Felds in den Daten ist zu groß. HRESULT: 0x80DA0009                 |Dauerhaft          |
|BadRequest_FileTooLarge                           |Die Blobgröße übersteigt das für die Erfassung zulässige Größenlimit.<br>Weitere Informationen zum Größenlimit für die Erfassung finden Sie im [Vergleich der Erfassungsmethoden und -tools](ingest-data-overview.md#comparing-ingestion-methods-and-tools). |Dauerhaft           |

## <a name="category-internalserviceerror"></a>Kategorie: InternalServiceError

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|General_InternalServerError                       |Ein interner Serverfehler ist aufgetreten.                     |Transient (vorübergehend)          |
|General_TransientSchemaMismatch                   |Das Schema der Zieltabelle beim Starten der Erfassung entspricht nicht dem Schema beim Committen der Erfassung.         |Transient (vorübergehend)           |
|Timeout                                            |Der Vorgang wurde aufgrund eines Timeouts abgebrochen.     |Transient (vorübergehend)           |
|BadRequest_MessageExhausted                       |Fehler beim Erfassen von Daten, da für die Erfassung die maximal zulässige Anzahl von Wiederholungsversuchen oder der maximal zulässige Wiederholungszeitraum erreicht wurde.<br>Eine erneute Ausführung der Erfassung ist möglicherweise erfolgreich.   |Transient (vorübergehend)          |

## <a name="category-updatepolicyfailure"></a>Kategorie: UpdatePolicyFailure

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema   |Fehler beim Aufrufen der Updaterichtlinie. Das Abfrageschema entspricht nicht dem Tabellenschema.     |Dauerhaft           |
|UpdatePolicy_FailedDescendantTransaction          |Fehler beim Aufrufen der Updaterichtlinie. Fehler bei der nachfolgenden transaktionsbezogenen Aktualisierungsrichtlinie.    |Transient (vorübergehend)           |
|UpdatePolicy_IngestionError                       |Fehler beim Aufrufen der Updaterichtlinie. Bei der Erfassung ist ein Fehler aufgetreten.<br>Der Fehler wird für die Quelltabelle der Updaterichtlinie gemeldet.     |Transient (vorübergehend)          |
|UpdatePolicy_UnknownError                         |Fehler beim Aufrufen der Updaterichtlinie. Unbekannter Fehler.<br>Der Fehler wird für die Zieltabelle der Updaterichtlinie gemeldet.    |Transient (vorübergehend)           |
|UpdatePolicy_Cyclic_Update_Not_Allowed         |Fehler beim Aufrufen der Updaterichtlinie. Zyklisches Update nicht zulässig.      |Dauerhaft           |

## <a name="category-useraccessnotauthorized"></a>Kategorie: UserAccessNotAuthorized

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|BadRequest_InvalidKustoIdentityToken              |Ungültiges Kusto-Identitätstoken.                           |Dauerhaft           |
|Verboten                                          |Unzureichende Sicherheitsberechtigungen zum Ausführen der Anforderung. | Dauerhaft|

## <a name="category-unknown"></a>Kategorie: Unknown

|Fehlermeldung                                 |BESCHREIBUNG                                           |Dauerhaft/Vorübergehend|
|---|---|---|
|Unbekannt                                            |Unbekannter Fehler.                             |Transient (vorübergehend)          |
