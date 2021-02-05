---
title: Reservationsrabatter för Azure App Service
description: Lär dig hur reservations rabatter gäller för Azure App Service Premium v3-instanser och isolerade stämplar.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: banders
ms.openlocfilehash: debe02a89e10712ad8a0b8d61b0fdc3f8a4bd7b2
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577807"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>Hur reservations rabatter gäller för Azure App Service Premium v3-instanser och isolerade stämplar

Den här artikeln hjälper dig att förstå hur rabatter gäller för Azure App Service Premium v3-instanser och isolerade stämplar.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Så här gäller reservations rabatter för Premium v3-instanser

När du har köpt en Azure App Service Premium v3-reserverad instans, tillämpas reservations rabatten automatiskt på App Service instanser som matchar attributen och mängden av reservationen. En reservation täcker kostnaden för Premium v3-instanserna. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Hur rabatten tillämpas på Azure App Service 

En reservations rabatt används *-IT-eller-förloras*. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.
När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

### <a name="reservation-discount-for-premium-v3-instances"></a>Reservations rabatt för Premium v3-instanser

Reservations rabatten för Azure används för att köra Premium v3-instanser per timme. De reservationer som du har köpt matchar användningen av de Premium v3-instanser som körs och som använder reservations rabatten. För Premium v3-instanser som kanske inte kör hela timmen kommer reservationen att fyllas från andra instanser som inte använder en reservation, inklusive inaktuella instanser som körs samtidigt. I slutet av timmen är reservations programmet för instanser i timmen låst. I händelse av att en instans inte körs för en timme eller samtidiga instanser inom timmen, fyller du inte i timmen för reservationen, reservationen underutnyttjas för den timmen. Följande diagram illustrerar tillämpningen av en reservation för fakturerbar VM-användning. Bilden baseras på ett reservationsköp och två matchande VM-instanser.

![Bild som visar programmet för en reservation för fakturerbar VM-användning](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  All användning som är ovanför reservationslinjen debiteras enligt de vanliga Betala per användning-priserna. Du debiteras inte för användning under reservationslinjen eftersom den redan har betalats som en del av reservationsköpet.
2.  Under timme 1 körs instans 1 i 0,75 timmar och instans 2 körs i 0,5 timmar. Total användning för timme 1 är 1,25 timmar. Du debiteras Betala per användning-priser för de återstående 0,25 timmarna.
3.  För timme 2 och timme 3 kördes båda instanserna 1 timme vardera. Den ena instansen omfattas av reservationen och den andra debiteras enligt Betala per användning-priserna.
4.  För timme 4 körs instans 1 i 0,5 timmar och instans 2 körs i 1 timme. Instans 1 omfattas helt av reservationen och 0,5 timmar av instans 2 omfattas. Du debiteras Betala per användning-priser för de återstående 0,5 timmarna.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i användningsrapporter för fakturering finns i [Förstå reservationsanvändning](understand-reserved-instance-usage-ea.md).

### <a name="discount-can-apply-to-different-sizes"></a>Rabatten kan tillämpas på olika storlekar

När du köper en reserverad Premium v3-instans och väljer **optimerad för storleks storlek för en instans**, gäller rabatt täckningen för den Premium v3-instans storlek som du väljer. Den kan också användas för andra instans storlekar i samma storlek som flexibilitet för instans storleks gruppen.

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Så här gäller reservations rabatter för isolerade stämplar

När du har köpt reserverad kapacitet för App Service Isolerad-stämpelavgift tillämpas rabatten automatiskt på stämpelavgiften i en region. Reservationsrabatten tillämpas på den användning som skickas av mätaren för isolerad stämpelavgift. Arbeten, ytterligare klientdelar samt andra resurser som associeras med stämpeln fortsätter att debiteras till det vanliga priset.

### <a name="reservation-discount-application"></a>Tillämpning av reservationsrabatter

Rabatten för App Service Isolerad-stämpelavgiften tillämpas per timme på isolerade stämplar som körs. Om du inte har någon stämpel distribuerad under en timme så går den reserverade kapaciteten för timmen förlorad. Den först inte vidare.

Efter köpet matchas den reservation som du köper till en isolerad stämpel som körs i en angiven region. Om du stänger av den stämpeln tillämpas reservationsrabatterna automatiskt på alla andra stämplar som körs i regionen. När det inte finns några stämplar tillämpas reservationen på nästa stämpel som skapas i regionen.

När stämplarna inte körs under en hel timme tillämpas reservationen automatiskt på andra matchande stämplar i samma region under samma timme.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Välj en stämpeltyp – Windows eller Linux

Som standard skickar en tom isolerad stämpel ut Windows-stämpelmätaren. Det gäller till exempel när inga arbeten distribueras. Den fortsätter att skicka ut mätaren när Windows-arbeten distribueras. Mätaren ändras till Linux-stämpelmätaren om du distribuerar ett Linux-arbete. Stämpeln skickar Windows-mätaren när både Linux-arbetet och Windows-arbetet distribueras.

Därmed kan stämpelmätaren ändras mellan Windows och Linux under stämpelns livslängd. Reservationer är specifika för ett visst operativsystem. Du behöver köpa en reservation som stöder de arbeten som du planerar att distribuera till stämpeln. Stämplar som endast är till för Windows samt blandade stämplar använder Windows-reservationen. Stämplar med Linux-exklusiva arbeten använder Linux-reservationen.

Den enda gång du bör köpa en Linux-reservation är om du planerar att _endast_ använda Linux-arbeten i stämpeln.

### <a name="discount-examples"></a>Exempel på rabatter

I följande exempel visas hur rabatten reserverad instans för isolerad stämpelavgift tillämpas beroende på distributionerna.

- **Exempel 1**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region utan App Service Isolerad-stämplar. Du distribuerar en ny stämpel till regionen och betalar reserverade priser för den stämpeln.
- **Exempel 2**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region där det redan finns en App Service Isolerad-stämpel distribuerad. Du börjar få det reserverade priset för den distribuerade stämpeln.
- **Exempel 3**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region där det redan finns en App Service Isolerad-stämpel distribuerad. Du börjar få det reserverade priset för den distribuerade stämpeln. Senare tar du bort stämpeln och distribuerar en ny. Du får det reserverade priset för den nya stämpeln. Rabatter förs inte vidare för varaktigheter utan distribuerade stämplar.
- **Exempel 4**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region och distribuerar sedan en ny stämpel i den regionen. När stämpeln ursprungligen distribueras utan arbetare skickar den Windows-stämpelmätaren. Ingen rabatt fås. När det första Linux-arbetet distribueras till stämpeln skickar det Linux-stämpelmätaren, och reservationsrabatten tillämpas. Om ett Windows-arbete senare distribueras till stämpeln återgår stämpelmätaren till Windows. Du får inte längre rabatt för reservationen av reserverad isolerad Linux-stämpel.

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- Om du vill veta mer om förskotts betalning App Service Premium v3 och isolerad stämpla reserverad kapacitet för att spara pengar, se [förskottsbetala Azure App Service med reserverad kapacitet](prepay-app-service.md).
- Mer information om Azure-reservationer finns i följande artiklar:
  - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
  - [Hantera reservationer i Azure](manage-reserved-vm-instance.md)
  - [Förstå reservationsanvändning för en prenumeration med Betala per användning-priser](understand-reserved-instance-usage.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
