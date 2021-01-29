---
title: Användning av Azure-reservation för en enskild prenumeration
description: Lär dig hur du läser av din användning för att förstå hur Azure-reservationen tillämpas för din enskilda prenumeration med Betala per användning-priser.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f6aad15d70157dda110fce233fbdafb7f3d896ad
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055067"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Förstå användning av Azure-reservationer för din enskilda prenumeration med Betala per användning-priser

Använd sidan ReservationId från [reservation](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) och användnings filen från [Azure Portal](https://portal.azure.com) för att utvärdera reservations användningen.

Om du är en kund med ett Enterprise-avtal kan du läsa i [Förstå reservationsanvändningen i din Enterprise-registrering](understand-reserved-instance-usage-ea.md).

I den här artikeln förutsätter vi att reservationen gäller en enskild prenumeration. Om reservationen gäller fler än en prenumeration kan reservationsförmånen omfatta flera CSV-användningsfiler.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Användning av reserverade VM-instanser

I följande avsnitt antar vi att du kör en Windows-dator av typen Standard_DS1_v2 i regionen USA, östra och att informationen om den reserverade VM-instansen ser ut som i följande tabell:

| Field | Värde |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Kvantitet |1|
|SKU | Standard_DS1_v2|
|Region | eastus |

Den virtuella datorns maskinvara täcks eftersom den distribuerade virtuella datorn matchar reservationens attribut. Du kan se vilka Windows-program som inte täcks av den reserverade VM-instansen [Kostnader för Windows-program i reserverade Azure VM-instanser](reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Informationsavsnittet i CSV-filen för virtuella datorer

I det här avsnittet av CSV-filen ser du den totala användningen av din reservation. Använd filtret i fältet **Mätarunderkategori** som innehåller **Reservation-** . Du bör se någonting som liknar följande skärmbild:

![Skärmbild av filtrerad användningsinformation och avgifter för en reservation](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

På raden **Reserverade instanser – bas-VM** ser du det totala antalet timmar som täcks av reservationen. Den här raden visar $0,00 eftersom den täcks av reservationen. Raden **Reserverade Windows Server-instanser (1 kärna)** täcker kostnaden för Windows-program.

### <a name="daily-usage-section-of-csv-file"></a>Avsnittet med daglig användning i CSV-filen

Filtrera på **Ytterligare information** och skriv in ditt **reservations-ID**. I följande skärmbild ser du fälten som är relaterade till reservationen.

![Skärmbild med information och avgifter för daglig användning](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** i fältet **Ytterligare information** är reservationen som tillämpas på den virtuella datorn.
2. **ConsumptionMeter** är den virtuella datorns mätar-ID.
3. Raden **Reserverade instanser – bas-VM** **Mätarunderkategori** representerar kostnaden $0 i informationsavsnittet. Kostnaden för att köra den här virtuella datorn täcks av reservationen.
4. **Mätar-ID** är reservationens mätar-ID. Kostnaden för den här mätaren är $0. Detta mätar-ID visas för alla virtuella datorer som är kvalificerade för reservationsrabatten.
5. Standard_DS1_v2 är en virtuell dator med vCPU och den virtuella datorn är distribuerad utan Azure Hybrid-förmånen. Den här mätaren täcker alltså den extra kostnaden för Windows-programvaran. Om du vill hitta mätaren som motsvarar den virtuella datorn i D-serien med 1 kärna kan du läsa i [Kostnader för Windows-program i reserverade Azure VM-instanser](reserved-instance-windows-software-costs.md). Om du har Azure Hybrid-förmånen tas inte den här extra avgiften ut.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>Användning av reservationer för SQL Database och Cosmos DB

I följande avsnitt används Azure SQL Database som exempel när vi beskriver användningsrapporten. Du kan hämta användningen för Azure Cosmos DB på samma sätt.

Anta att du kör en SQL Database Gen 4 i regionen USA, östra och att reservationsinformationen ser ut som i följande tabell:

| Field | Värde |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Kvantitet |2|
|Produkt| SQL Database Gen 4 (2 Core)|
|Region | eastus |

### <a name="statement-section-of-csv-file"></a>Informationsavsnittet i CSV-filen

Filtrera på mätarnamnet **Reserverade instanser – användning** och välj **mätarkategorin** – Azure SQL Database eller Azure Cosmos DB. Du bör se någonting som liknar följande skärmbild:

![Skärmbild som visar en post för mätarkategori.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

På raden **Reserverade instanser – användning** ser du det totala antalet kärntimmar som täcks av reservationen. Priset är $0 för den här raden eftersom reservationen täcker kostnaden.

### <a name="detail-section-of-csv-file"></a>Informationsavsnittet i CSV-filen

Filtrera på **Ytterligare information** och skriv in ditt **reservations-ID**. I följande skärmbild ser du fälten som är relaterade till den reserverade SQL Database-kapaciteten.

![Skärmbild som visar information om en CSV-fil för reserverad kapacitet.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **ReservationId** i fältet **Ytterligare information** är reservationen av SQL Database-kapacitet som tillämpas på SQL Database-resursen.
2. **ConsumptionMeter** är SQL Database-resursens mätar-ID.
3. **Mätar-ID** är reservationens mätare. Kostnaden för den här mätaren är $0. Det här mätar-ID:t visas för alla SQL Database-resurser som är berättigade till reservationsrabatten i CSV-filen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Hantera Azure Reservations](manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](../manage/understand-vm-reservation-charges.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara som inte ingår i reservationer](reserved-instance-windows-software-costs.md)