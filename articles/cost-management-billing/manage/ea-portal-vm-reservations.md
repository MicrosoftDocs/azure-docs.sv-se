---
title: Reserverade instanser av virtuella Azure EA-datorer
description: I den här artikeln går vi igenom hur du kan spara pengar i din företagsregistrering med hjälp av reserverade VM-instanser i Azure.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 072377f98939d996547cb6ee9f9a535b4b32a3dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597956"
---
# <a name="azure-ea-vm-reserved-instances"></a>Reserverade instanser av virtuella Azure EA-datorer

I den här artikeln går vi igenom hur du kan spara pengar i din företagsregistrering med hjälp av reserverade VM-instanser i Azure. Du kan läsa mer om reservationer i [Vad är Azure-reservationer?](../reservations/save-compute-costs-reservations.md)

## <a name="reservation-exchanges-and-refunds"></a>Utbyten och återbetalningar för reservationer

Du kan byta en reservation mot en annan reservation av samma typ. Du kan också få återbetalning för en reservation, upp till 50 000 USD per år, om du inte längre behöver den. Du kan byta ut och få återbetalning för reservationer i Azure-portalen. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../reservations/exchange-and-refund-azure-reservations.md).

### <a name="partial-refunds"></a>Partiella återbetalningar

Vi utfärdar en partiell återbetalning när EA-kunder returnerar reservationer som köpts med överförbrukning, inte Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande).

Återbetalningen visas i EA-portalen som en negativ justering i föregående månad och en positiv justering i aktuell månad. Den visas på ett sätt som liknar ett reservationsutbyte. Kreditnotan refererar till det ursprungliga fakturanumret. För att stämma av det första köpet med kreditnotan bör du därför se det ursprungliga fakturanumret.

## <a name="reservation-costs-and-usage"></a>Kostnader för och användning av reservationer

Kunder med företagsavtal kan visa information om kostnader och användning i Azure-portalen och via REST-API:er. När det gäller kostnader för och användning av reservationer kan du göra följande:

- Hämta data om reservationsköp.
- Se vilken prenumeration, resursgrupp eller resurs som förbrukat en reservation.
- Få återbetalning för reservationsanvändning.
- Beräkna reservationsbesparingar.
- Hämta data om underutnyttjade reservationer.
- Amortera reservationskostnader.

Mer information om kostnader för och användning av reservationer finns i [Hämta kostnader för och användning av EA-reservationer](../reservations/understand-reserved-instance-usage-ea.md).

Information om priser finns i [Prissättning för virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Prissättning för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>API-stöd för reserverade instanser

Använd Azure-API:er för att programmatiskt hämta information för din organisation angående reservationer för Azure-tjänster eller programvara. Använd till exempel API:erna när du vill göra följande:

- Hitta reservationer att köpa
- Köpa en reservation
- Visa köpta reservationer
- Visa och hantera reservationsåtkomst
- Dela eller sammanslå reservationer
- Ändra reservationsomfånget

Mer information finns i [API:er för automation av Azure-reservation](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Reserverade instanser för virtuella Azure-datorer

Med reserverade instanser kan du minska dina kostnader för virtuella datorer med upp till 72 procent jämfört med Betala per användning-priser på alla virtuella datorer. Eller få besparingar på upp till 82 procent när de kombineras med Azure Hybrid-förmånen. Med hjälp av reserverade instanser kan du hantera arbetsbelastningar, budgetar och prognoser på ett bättre sätt med direkt betalning för ett eller tre år. Du kan också byta eller avboka reservationer i takt med att affärsbehoven ändras.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Så köper du reserverade instanser för virtuella datorer

Om du vill köpa en reserverad instans för en virtuell Azure-dator måste en Enterprise Azure-registreringsadministratör aktivera köpalternativet _Reserve Instance_ (Reservera instans). Alternativet finns i avsnittet _Enrollment Detail_ (Registreringsinformation) på fliken _Enrollment_ (Registrering) i [Azure EA-portalen](https://ea.azure.com/).

När EA-registreringen har aktiverats för att lägga till reserverade instanser kan alla kontoinnehavare med en aktiv prenumeration associerad kopplad till EA-registreringen köpa en reserverad instans för virtuell dator i [Azure-portalen](https://aka.ms/reservations). Mer information finns i [Prepay for virtual machines and save money with Reserved Virtual Machine Instances](../../virtual-machines/prepay-reserved-vm-instances.md) (Förskottsbetala för virtuella datorer och spara pengar med reserverade instanser för virtuell dator).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Så här visar du köpinformation om reserverade instanser

Du kan visa köpinformationen om reserverade instanser via menyn _Reservationer_ på den vänstra sidan av [Azure-portalen](https://aka.ms/reservations) eller via [Azure EA-portalen](https://ea.azure.com/). Välj **Reports** (Rapporter) i menyn till vänster och rulla ned till avsnittet _Charges by Services_ (Avgifter per tjänst) på fliken _Usage Summary_ (Användningsöversikt). Rulla längst ned i avsnittet om du vill se din lista med köp och användning av reserverade instanser i slutet, enligt det som anges av beteckningstexten `1 year` (1 år) eller `3 years` (3 år) bredvid tjänstens namn, till exempel: `Standard_DS1_v2 eastus 1 year` eller `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Hur ändrar jag prenumerationen som är kopplad till reserverad instans eller överföra mina reserverad instans-förmåner till en prenumeration under samma konto?

Du kan ändra den prenumeration som tar emot reserverad instans-förmåner genom att göra följande:

- Logga in på [Azure-portalen](https://aka.ms/reservations).
- Uppdatera det tillämpade prenumerationsomfånget genom ett koppla en annan prenumeration under samma konto.

Mer information om hur du ändrar omfånget för en reservation finns i [Ändra reservationens omfång](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Så här visar du användningsinformation om reserverade instanser

Du kan visa din reserverad instans-användningsinformation i [Azure-portalen](https://aka.ms/reservations) eller i [Azure EA-portalen](https://ea.azure.com/) (för EA-kunder som har åtkomst till att visa faktureringsinformation) under _Reports_ > _Usage Summary_ > _Charges by Services_ (Rapporter > Användningsöversikt > Avgifter per tjänst). Dina reserverade instanser kan identifieras som tjänstnamn som innehåller ”Reservation”, till exempel: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Din nedladdningsfil i CSV-format med användningsinformation och avancerad rapport har ytterligare användningsinformation om reserverad instans. Fältet _Additional Info_ (Mer information) gör det enklare att identifiera reserverad instans-användningen.

Om du inte har använt Azure Hybrid-förmånen för att köpa reserverad instans för virtuell Azure-dator så genererar reserverade instanser två mätare (maskinvara och programvara). När du använder Azure Hybrid-förmånen för att köpa reserverad instans visas inte programvarumätaren i användningsinformationen för reserverad instans.

### <a name="reserved-instance-billing"></a>Fakturering för reserverad instans

För företagskunder används Azure-förskottsbetalningar för att köpa reserverade VM-instanser i Azure. Om registreringen har ett tillräckligt stort Azure-förskottsbetalningssaldo för att täcka köpet av den reserverade instansen dras beloppet av från Azure-förskottsbetalningssaldot och du får ingen faktura för köpet.

Om Azure EA-kunder redan har använt hela sina Azure-förskottsbetalningar kan reserverade VM-instanser ändå köpas. Dessa inköp faktureras då på nästa faktura för överförbrukning. Om det finns överförbrukning av reserverade instanser blir den en del av din vanliga överförbrukningsfaktura.

### <a name="reserved-instance-expiration"></a>Upphörande av reserverad instans

Du får e-postaviseringar 30 dagar innan reservationen och när den upphör att gälla. När reservationen går ut fortsätter de distribuerade virtuella datorerna att köras och debiteras enligt avgifterna för betalning per användning. Mer information finns i [Reserverade VM-instanser (RI) i Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Nästa steg

- Du kan läsa mer om Azure-reservationer i [Vad är Azure-reservationer?](../reservations/save-compute-costs-reservations.md)
- Du ka läsa mer om kostnader för och användning av reservationer i [Hämta kostnader för och användning av EA-reservationer](../reservations/understand-reserved-instance-usage-ea.md).
- Information om priser finns i [Prissättning för virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Prissättning för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).