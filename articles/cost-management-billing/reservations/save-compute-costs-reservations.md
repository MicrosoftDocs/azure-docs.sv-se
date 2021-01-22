---
title: Vad är Azure Reservations?
description: Lär dig mer om Azure-reservationer och priser för att spara kostnader för dina reserverade instanser för virtuella datorer, SQL-databaser, Azure Cosmos DB och andra resurser.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 0e45e9741e92bb9e1fe23af79695cae06e64e871
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602058"
---
# <a name="what-are-azure-reservations"></a>Vad är Azure Reservations?

Med Azure Reservations kan du spara pengar genom att registrera dig för en ett- eller tre-årsplan för flera produkter. Åtagande gör att du kan få rabatt på de resurser du använder. Reservationer kan avsevärt minska dina resurskostnader med upp till 72 procent jämfört med Betala per användning-priser. Reservation ger en rabatt och påverkar inte resursernas körningsstatus. När du har köpt en reservation tillämpas rabatten automatiskt på matchande resurser.

Du kan betala för en reservation i förväg eller månadsvis. Totalbeloppet för reservationer är detsamma oavsett om du betalar i förväg eller per månad. Det tillkommer inte några extra avgifter när du väljer månadsbetalning. Månatlig betalning är tillgänglig för Azure-reservationer, inte produkter från tredje part.

Du kan köpa en reservation i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Varför är det bra att köpa en reservation?

Om din resursanvändning är konsekvent och har stöd för reservationer kan du minska dina kostnader genom att köpa en reservation. När du till exempel kontinuerligt kör instanser av en tjänst utan en reservation debiteras du enligt priserna för betala per användning. När du köper en reservation tillämpas reservationsrabatten direkt. Resurserna debiteras inte längre enligt priserna för betala per användning.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Efter köpet tillämpas reservationsrabatten automatiskt på den resursanvändning som matchar de attribut som du väljer när du köper reservationen. Exempel på attribut är SKU, regioner (om tillämpligt) och omfång. Reservationsomfånget bestämmer var reservationsbesparingarna ska gälla.

Mer information om hur rabatten tillämpas finns i avsnittet om hur [rabatten för reserverade instanser tillämpas](reservation-discount-application.md).

Mer information om hur reservationsomfång fungerar finns i avsnittet om [reservationsomfång](prepare-buy-reservation.md#scope-reservations).

## <a name="determine-what-to-purchase"></a>Bestämma vad som ska köpas in 

Alla reservationer, förutom Azure Databricks, tillämpas per timme. Överväg att köpa reservationer baserat på den fasta basanvändningen. Du kan avgöra vilken reservation du ska köpa genom att analysera användningsdata eller med hjälp av reservationsrekommendationer. Rekommendationer är tillgängliga i

- Azure Advisor (endast virtuella datorer)
- Platsen för reservationsköp i Azure Portal
- Cost Management Power BI-appen
- API:er 

Mer information finns i  [Välja rätt reservation](determine-reservation-purchase.md) 

## <a name="buying-a-reservation"></a>Köpa en reservation 

Du kan köpa reservationer från Azure-portalen, API:er, PowerShell och CLI. 

Gå till [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) om du vill göra ett inköp.

Mer information finns i [Köpa en reservation](prepare-buy-reservation.md).

## <a name="how-is-a-reservation-billed"></a>Hur faktureras en reservation? 

En reservation debiteras enligt den betalningsmetod som är kopplad till prenumerationen. Reservationskostnaden dras av från saldot på din Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande), om tillgängligt. Om ditt saldo för Azure-förskottsbetalning inte täcker kostnaden för reservationen debiteras du för överförbrukningen. Om du har en prenumeration från en enskild plan med Betala per användning-priser debiteras det kreditkort som du har på ditt konto omedelbart för förskottsköp. Månatliga betalningar visas på din faktura, och ditt kreditkort debiteras månatligen. När du faktureras per faktura visas avgifterna på nästa faktura. 

## <a name="who-can-manage-a-reservation-by-default"></a>Vem kan hantera en reservation som standard?

Som standard kan följande användare visa och hantera reservationer:

- Den person som köper en reservation och kontoadministratören för den faktureringsprenumeration som används för att köpa reservationen läggs till i reservationsbeställningen.
- Faktureringsadministratörer för Enterprise-avtal och Microsoft-kundavtal.

Information om hur du gör så att andra kan hantera reservationer finns i [Hantera reservationer för Azure-resurser](manage-reserved-vm-instance.md).

## <a name="get-reservation-details-and-utilization-after-purchase"></a>Hämta reservationsinformation och användning efter köpet

Om du har behörighet att visa till reservationen kan du se den och dess användning i Azure-portalen. Du kan också hämta dessa data med hjälp av API:er. 

Mer information om hur du ser reservationer i Azure-portalen finns i  [Visa reservationer i Azure-portalen](view-reservations.md) 

## <a name="manage-reservations-after-purchase"></a>Hantera reservationer efter köp 

När du har köpt en Azure-reservation kan du uppdatera omfånget för att använda reservationen i en annan prenumeration, ändra vem som får hantera reservationen, dela upp en reservation i mindre delar eller ändra flexibel instansstorlek. 

Mer information finns i  [Hantera reservationer för Azure-resurser](manage-reserved-vm-instance.md) 

## <a name="flexibility-with-azure-reservations"></a>Flexibilitet med Azure-reservationer

Azure-reservationer ger flexibilitet för att tillgodose dina föränderliga behov. Du kan byta en reservation mot en annan reservation av samma typ. Återbetalning av en reservation, upp till 50 000 USD under en 12-månaders period, är också möjligt om du inte längre behöver reservationen. Maxgränsen för återbetalning gäller alla reservationer inom omfånget för ditt avtal med Microsoft.

Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>Avgifter som omfattas av reservation

- **Reserverad VM-instans** – en reservation omfattar bara den virtuella datorns och molntjänsternas beräkningskostnader. Den omfattar inte avgifter för ytterligare programvara, Windows, nätverk eller lagring.
- **Reserverad kapacitet i Azure Storage** – En reservation omfattar lagringskapacitet för standardlagringskonton för blob-lagring eller Azure Data Lake Gen2-lagring. Reservationen omfattar inte bandbredds- eller transaktionsavgifter.
- **Reserverad Azure Cosmos DB-kapacitet** – en reservation omfattar det dataflöde som allokeras för dina resurser. Den omfattar inte avgifter för lagring och nätverk.
- **SQL Database-reserverad virtuell kärna** – Täcker både SQL Managed Instance och SQL Database – elastisk pool/enkel databas. Endast beräkningskostnaderna ingår i en reservation. SQL-licensen debiteras separat. 
- **Azure Synapse Analytics** – en reservation omfattar cDWU-användningen. Den omfattar inte lagrings- eller nätverksavgifter som är kopplade till Azure Synapse Analytics-användningen.
- **Azure Databricks** – En reservation täcker endast DBU-användningen. Andra avgifter för till exempel beräkning, lagring och nätverk debiteras separat.
- **App Service-stämpelavgift** – en reservation omfattar stämpelanvändning. Den gäller inte för arbeten, och därför debiteras andra resurser som är kopplade till stämpeln separat.
- **Azure Database for MySQL** – Endast beräkningskostnaderna ingår med en reservation. En reservation täcker inte programvaru-, nätverks- eller lagringskostnader som är kopplade till MySQL-databasservern.
- **Azure Database for PostgreSQL** – Endast beräkningskostnaderna ingår med en reservation. En reservation täcker inte programvaru-, nätverks- eller lagringskostnader som är kopplade till PostgreSQL-databasservrarna.
- **Azure Database for MariaDB** – Endast beräkningskostnaderna ingår med en reservation. En reservation täcker inte programvaru-, nätverks- eller lagringskostnader som är kopplade till MariaDB-databasservern.
- **Azure Data Explorer** – En reservation täcker påläggsavgifterna. En reservation gäller inte beräknings-, nätverks- eller lagringskostnader som är kopplade till klustren.
- **Azure Cache for Redis** – Endast beräkningskostnaderna ingår i en reservation. En reservation täcker inte nätverks- eller lagringskostnader som är kopplade till Redis Cache-instanser.
- **Azure-dedikerad värd** – Endast beräkningskostnaderna ingår med den dedikerade värden.
- **Azure-disklagringsreservationer** – en reservation täcker endast Premium-SSD med storleken P30 eller större. Den omfattar inte andra disktyper eller storlekar som är mindre än P30.

Programvaruplaner:

- **SUSE Linux-** – En reservation täcker kostnaderna för programvaruplanen. Rabatterna gäller endast SUSE-mätare och inte användningen av virtuella datorer.
- **Red Hat-planer** – En reservation täcker kostnaderna för programvaruplanen. Rabatterna gäller endast RedHat-mätare och inte användningen av virtuella datorer.
- **Azure VMware Solution by CloudSimple** – En reservation täcker VMware CloudSimple-noderna. Ytterligare programvarukostnader kan tillkomma.
- **Azure Red Hat OpenShift** – En reservation gäller för OpenShift-kostnaderna, inte för Azure-infrastrukturkostnader.

Reservationsrabatten gäller inte för programvarukostnaderna för virtuella Windows-datorer och SQL Database. Licensieringskostnaderna kan täckas av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure-reservationer med följande artiklar:
    - [Hantera Azure Reservations](manage-reserved-vm-instance.md)
    - [Förstå reservationsanvändning för din prenumeration med Betala per användning-priser](understand-reserved-instance-usage.md)
    - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
    - [Kostnader för Windows-programvara ingår inte i reservationer](reserved-instance-windows-software-costs.md)
    - [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](/partner-center/azure-reservations)

- Läs mer om reservationer för tjänstplaner:
    - [Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB-resurser med reserverad Azure Cosmos DB-kapacitet](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
    - [Azure Cache for Redis-resurser med reserverad kapacitet för Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md) Läs mer om reservationer för programvaruplaner:
    - [Red Hat-programvaruplaner från Azure-reservationer](../../virtual-machines/linux/prepay-suse-software-charges.md)
    - [SUSE-programvaruplaner från Azure-reservationer](../../virtual-machines/linux/prepay-suse-software-charges.md)
