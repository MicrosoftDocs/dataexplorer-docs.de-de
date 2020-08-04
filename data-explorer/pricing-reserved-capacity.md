---
title: Vorauszahlen für Azure Data Explorer-Markup zum Einsparen von Kosten
description: Erfahren Sie, wie Sie reservierte Azure Data Explorer-Kapazität kaufen, um Kosten für Azure Data Explorer zu sparen.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: e954b66c59e480d7fc713841e9a029da61dabd8f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350535"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Vorauszahlen für Azure Data Explorer-Markupeinheiten mit reservierter Azure Data Explorer-Kapazität

Sparen Sie mit Azure Data Explorer Geld im Vergleich zur nutzungsbasierten Bezahlung, indem Sie die Markupeinheiten im Voraus bezahlen. Mit reservierter Azure Data Explorer-Kapazität legen Sie sich für die Nutzung von Azure Data Explorer für einen Zeitraum von einem oder drei Jahren fest und profitieren dafür von einem immensen Rabatt auf Azure Data Explorer-Markupkosten. Wenn Sie reservierte Azure Data Explorer-Kapazität erwerben möchten, müssen Sie lediglich die Laufzeit angeben. Diese gilt für alle Bereitstellungen von Azure Data Explorer in allen Regionen.

Beim Kauf einer Reservierung bezahlen Sie im Voraus die Markupkosten für einen Zeitraum von einem oder drei Jahren. Sobald Sie eine Reservierung gekauft haben, werden die Azure Data Explorer-Markupgebühren, die den Reservierungsattributen entsprechen, nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. Azure Data Explorer-Cluster, die bereits ausgeführt oder neu bereitgestellt werden, profitieren automatisch von dem Vorteil. Diese Reservierung deckt keine mit den Clustern verbundenen Compute-, Netzwerk- oder Speichergebühren ab. Reservierte Kapazität für diese Ressourcen muss separat erworben werden. Nach Ablauf der Reservierungslaufzeit erlischt der Abrechnungsvorteil, und die Azure Data Explorer-Markupeinheiten werden mit den Preisen für die nutzungsbasierte Bezahlung in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert. Preisinformationen finden Sie auf der Seite [Azure Data Explorer – Preise](https://azure.microsoft.com/pricing/details/data-explorer/).

Sie können reservierte Azure Data Explorer-Kapazität über das [Azure-Portal](https://portal.azure.com) erwerben. Zum Kaufen von reservierter Azure Data Explorer-Kapazität ist Folgendes erforderlich:

* Sie müssen der Besitzer mindestens eines Enterprise-Abonnements oder eines Abonnements mit nutzungsbasierter Bezahlung sein.
* Bei Enterprise-Abonnements muss im **EA-Portal** die Option [Reservierte Instanzen hinzufügen](https://ea.azure.com) aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
* Für das Cloud Solution Provider-Programm (CSP) können nur die Administrator- oder Vertriebs-Agents reservierte Azure Data Explorer-Kapazität erwerben.

Ausführliche Informationen dazu, wie Reservierungskäufe bei Kunden mit einem Enterprise-Abonnement und Kunden mit einem Abonnement mit nutzungsbasierter Bezahlung in Rechnung gestellt werden, finden Sie in den folgenden Artikeln:
* [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise-Registrierung](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 
* [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)

## <a name="determine-the-right-markup-usage-before-purchase"></a>Bestimmen der richtigen Markupnutzung vor dem Kauf

Die Reservierungsgröße sollte auf der Gesamtanzahl der Azure Data Explorer-Markupeinheiten basieren, die von den vorhandenen oder demnächst bereitgestellten Azure Data Explorer-Clustern verwendet werden. Die Anzahl der Markupeinheiten entspricht der Anzahl der Azure Data Explorer-Engine-Clusterkerne in der Produktionsumgebung (ohne Dev/Test-SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Kaufen reservierter Azure Data Explorer-Kapazität

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Dienste** > **Reservierungen** > **Jetzt kaufen** aus. Wählen Sie **Hinzufügen** aus.
1. Wählen Sie im Bereich **Produkttyp auswählen** die Option **Azure Data Explorer** aus, um eine neue Reservierung für Azure Data Explorer-Markupeinheiten zu erwerben. 
1. Wählen Sie **Kaufen** aus.
1. Füllen Sie die erforderlichen Felder aus. 

    ![Kaufseite](media/pricing-reserved-capacity/purchase-page.png)

1. Überprüfen Sie die Kosten für die Reservierung von Azure Data Explorer-Kapazität im Abschnitt **Kosten**.
1. Wählen Sie die Option **Kaufen**.
1. Klicken Sie auf **Diese Reservierung anzeigen**, um den Status des Einkaufs anzuzeigen.

## <a name="cancellations-and-exchanges"></a>Stornierungen und Umtausch

Wenn Sie Ihre Reservierung von Azure Data Explorer-Kapazität stornieren möchten, wird unter Umständen eine Gebühr für die vorzeitige Kündigung in Höhe von 12 % berechnet. Rückerstattungen basieren auf dem niedrigsten Preis Ihres Kaufpreises oder dem aktuellen Preis der Reservierung. Rückerstattungen sind auf 50.000 US-Dollar pro Jahr beschränkt. Die Rückerstattung, die Sie erhalten, umfasst den verbleibenden anteiligen Saldo abzüglich der Gebühr für die vorzeitige Kündigung in Höhe von 12%. Navigieren Sie zum Anfordern einer Stornierung im Azure-Portal zu der Reservierung, und wählen Sie **Erstattung** aus, um eine Supportanfrage zu erstellen.

Wenn Sie die Reservierung von Azure Data Explorer-Kapazität in eine andere Laufzeit ändern möchten, können Sie sie gegen eine andere Reservierung austauschen, die den gleichen oder einen höheren Wert hat. Das Startdatum der Laufzeit für die neue Reservierung wird nicht von der umgetauschten Reservierung übernommen. Die Laufzeit von einem oder drei Jahren beginnt ab der Erstellung der neuen Reservierung. Navigieren Sie zum Anfordern eines Umtauschs im Azure-Portal zu der Reservierung, und wählen Sie **Umtausch** aus, um eine Supportanfrage zu erstellen.

Weitere Informationen zum Umtausch oder zur Rückerstattung von Reservierungen finden Sie unter [Reservierungsumtausch und -rückerstattung](/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="manage-your-reserved-capacity-reservation"></a>Verwalten der Kapazitätsreservierung

Der Rabatt auf die Reservierung von Azure Data Explorer-Markupeinheiten wird automatisch auf die Anzahl der Markupeinheiten angewendet, die dem Reservierungsumfang und den Attributen der Azure Data Explorer-Kapazitätsreservierung entsprechen. 


> [!NOTE]
> * Sie können den Umfang der Azure Data Explorer-Kapazitätsreservierung über das [Azure-Portal](https://portal.azure.com), PowerShell, die CLI oder die API aktualisieren.
> * Informationen zum Verwalten der reservierten Azure Data Explorer-Kapazität finden Sie im Abschnitt zur [Verwaltung der reservierten Azure Data Explorer-Kapazität](/azure/cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

* [Was sind Azure-Reservierungen?](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
* [Verwalten von Azure-Reservierungen](/azure/cost-management-billing/reservations/manage-reserved-vm-instance)
* [Grundlegendes zum Rabatt für Azure-Reservierungen](/azure/cost-management-billing/reservations/understand-reservation-charges)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)
* [Verkaufen Microsoft Azure Reserved Instances](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
